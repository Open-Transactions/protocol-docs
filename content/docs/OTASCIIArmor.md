---
title: "OTASCIIArmor"
---

# OTASCIIArmor

The opentxs class [`OTASCIIArmor`][OTASCIIArmorCpp] defines an encoding for
certain data types to an array of printable ASCII characters. This is useful in
three contexts:

* Embedding binary data in a plain-text document. The _Section Format_ used in
  [OTContract](OTContract.md) is defined as a document that is readable by
  humans.  In order to be able to attach signatures, it is necessary to encode
  the binary data in [Base64][Base64].
* Embedding plain-text strings in Section-Format documents when using string
  concatenation. (Lines starting with `-----` must be escaped)
* Embedding plain-text strings in XML documents when using string concatenation.
  (Characters like `<` and `&` must be escaped)

## Encoding Algorithm

#### Packing

Armoring is defined for two input types:

* `OTData` using `OTASCIIArmor::SetAndPackData()` (alias `SetData()`). Packs
  using `OTDB::CreateObject(OTDB::STORED_OBJ_BLOB)`.
* `OTString` using `OTASCIIArmor::SetAndPackString()` (alias `SetString()`).
  Packs using `OTDB::CreateObject(OTDB::STORED_OBJ_STRING)`.

The data types are packed using  OT-defined default packer, which is hard-coded
to Protocol Buffers in the current source code ([Source][ProtobufHardcode]). The
same packer must be used on the client and server.

The packing is defined by the complex `OTDB`/`OTStorage` system defined by these
files (non-exhaustive):

* [OTStorage.cpp][OTStorageCpp], [OTStorage.hpp][OTStorageHpp]: Abstract storage
    subsystem.
* [OTStoragePB.hpp][OTStoragePbHpp]: Protocol-buffer implementation of storage.
* [Generics.proto][GenericsProto]: Protocol-buffer definitions for packing
  generic data types like strings and binary data.

#### Compression (strings only)

When the input is of type `OTString`, the packed data is compressed using the
[_zlib_ `deflate` algorithm][Deflate].

#### Base64-Encoding

The resulting byte array is Base64-encoded and stored using the superclass
method `OTString::Set(string)`.

The encoded can be retrieved via the `OTString::Get()` method.

All encoding and methods accept the boolean parameter `bLineBreaks`. This
parameter is passed to the underlying OpenSSL routine. When `true`, the Base64
output is formatted to produce lines with a length of 64 characters.

The default value is `true`. I have not found an example where `false` is
passed.

#### Adding Section Markers and Headers (Optional)

The class supports methods for writing section markers around the encoded data
using the method `OTASCIIArmor::WriteArmoredString()`. The format is defined in
[Section Format](../spec/SectionFormat.md).


## Decoding Algorithm

The decoding steps mirror the encoding steps:

1. Section Format parsing (optional)
1. Base64-decode
1. Uncompress (strings only)
1. Unpack


The decode methods are:

* `OTASCIIArmor::LoadFromString()` (called first when reading from section
  format)
* `OTASCIIArmor::GetAndUnpackData()` (alias `GetData()`)
* `OTASCIIArmor::GetAndUnpackString()` (alias `GetString()`)


### Section Format

The parser for the section format is similar to the one used in
[OTContract](OTContract.md). As with the contract format, it unintentionally
recognizes a much broader set of messages than what is actually written:


* The begin marker is completely parameterized and defaults to `----BEGIN`
* The end marker is not parameterized and is hard-coded to `-----END`
* Lines leading up to the section marker are ignored.
* Lines after the end marker are ignored.
* Lines starting with `Version:` and `Comment:` are valid everywhere and skipped
  completely

# Notes

## Naming

The class is located in the `src/core/crypto` directory despite being unrelated
to cryptography.

## Armoring in Other Contexts

[Ricardian contracts](http://iang.org/papers/ricardian_contract.html) form the
foundation of OT. A Ricardian contract is a human and machine readable text and
signed with OpenPGP in clear text form (see section 3.1 of aforementioned
document). The [_OpenPGP Document Format_](http://tools.ietf.org/html/rfc4880)
states that in the case where there is a risk of damage to the data due to
transport, data conversions or character set translations the content may be
armored.


## Inconsistent Use

Because opentxs writes simultaneously XML and Section-Format using string
concatenation, some kind of escaping is necessary when embedding strings.
Base64 is a escaping mechanism that removes all problematic characters, at the
cost of also removing all human readability. However, it is not enforced at the
serialization level, which raises security concerns: Plain-text concatenation is
used to construct messages in some places:
[OTContract::CreateInnerContents()][CreateInnerContents].

String concatenation should be eliminated as a means of constructing messages
and be replaced by document-specific serializers which take care of escaping
strings.

Where byte-equivalence is necessary (contract signatures), it must be ensured
that whitespace is not modified. An XML serializer might change line endings and
indentation.

One way to achieve byte equivalence is to normalize embedded sections. The
normalization algorithm would trim whitespace for each line and set the line
ending to `\n`.

## Loss of Readability

When used as a format-agnostic escaping mechanism, all human readability of
plain text is eliminated (the characters are still printable, but meaning is
lost). This is opposite to the goal of a Ricardian contract.

## Network Message Format Depends on Storage System

The _Packing_ stage of armoring depends on the `OTDB` subsystem, which is
designed as an abstraction for local storage. The implementation allows choosing
between different storage and encoding backends to some extent. It is possible
to choose between the `MessagePack` and `ProtocolBuffer` libraries for packing.
This however requires that server and client must be compiled using the same
Packer in order to communicate.

## Line Breaks

ASCII-Armored Base64 output should always be formatted with a maximum line
length. Having a single Base64-encoded line breaks readability.

This is a flaw in the underlying Base64 encoding method that is used in
`OTCryptoOpenSSL`, which does Base64 *plus* line breaking. Both can and should
can be done separately (see [boost][boostBase64], [golang][golangBase64]).

Decoding ASCII-Armored blocks should just strip all whitespace before base64
decode (see [boost][boostBase64], [golang][golangBase64]).


## Unnecessary Operations

Armoring combines three separate steps: packing, compression and Base64
encoding. In many cases, some or all of the steps are unnecessary, increase
complexity and waste resources.

#### Unnecessary Packing

In addition being dependent on a compile-time flag, there is no discernible
reason for packing.

I managed to trace the packing code to these comments:

```
// If adding packing STILL didn't make us binary compatible, then I need to try this next:
// Do the compression, THEN PACK...
// On the other way, UNPACK, THEN Uncompress.
//
// Right now I'm doing packing before compression, and unpacking after uncompression.
// Basically if that doesn't work (even though easyzlib appears to care about endian/platform)
// then switch the, (though that seems to make less logical sense to me.)
// Maybe have to pack before both? Seems crazy.
```

> I added these pieces 1-by-1 over time. At first the messages were too long,
so I started compressing them. Then they were not binary compatible across
various platforms, so I added the packing.

It is unclear what is meant by this. Byte sequences are always binary compatible
across platforms. If anything, packing as implemented now increases
platform-dependence.


#### Unnecessary Compression

Assuming armoring is used in order to decrease overall message size, it is
unclear whether that goal is achieved. Base64 encoding has a fixed space
overhead of 33% ([Source][Base64]).

The compression ratio is at least as good when embedding strings as plain-text
and compressing the message in a final step, before encryption.

Analysis and comparative measurements are necessary here.

This procedure is also recommended in the [OpenPGP Document Format (Section
2.3)](http://tools.ietf.org/html/rfc4880#section-2.3).

#### Unnecessary Base64

In some cases, Base64-encoding of binary data is applied even though there is no
need to. For example, Base64-encoding is applied when sending data via the
ZeroMQ transport.

## Unnecessary Conflation of Operations

Even if the individual steps in armoring are useful (very debateable), there is
no good reason to conflate them in armoring. There should only be one input
type `OTData` (variable-length byte array). Any operation like packing or
compression can be independent from Base64 encoding.


# Future Changes

## Using Format-Specific Escaping

In order to simplify the protocol, we should explore the option of dropping
armoring in the message protocol as a means of escaping plain-text data.
String concatenation should be abandoned as a means of generating XML or
Section-Format documents.

Binary data can be encoded with traditional Base64 encoding.

## Alternatives to Nesting

Nesting documents of the same format degrades human-readability, whether by
Base64-encoding or other types of escaping.

Alternatives should be considered. One possibility is referencing to another
document by hash and attaching it to the message on the top level. This is
common in legal documents as well. Example using the Section-Format:

```xml
-----BEGIN SIGNED CONTRACT-----
<contract id="$hashOfContract">
  <subcontract ref="$hashOfDocument" />
</contract>
-----BEGIN CONTRACT SIGNATURE-----
$signature
-----END CONTRACT SIGNATURE-----


<!-- this is the subcontract -->
-----BEGIN SIGNED CONTRACT-----
Document-Id: $hashOfContent

<contract>
  $otherContract
</contract>
-----END CONTRACT SIGNATURE-----
```

When dropping Section-Format altogether:

```xml
<document id="$hashOfContent">
  <content>
    $content
    <subdocument ref="$hashOfOtherDocument" />
  </content>
  <signature meta="$meta">signature</signature>
</document>

<document id="$hash">
  <!-- etc -->
</document>
```

This would probably also allow a higher compression level.

<!--- links -->

[ProtobufHardcode]: https://github.com/Open-Transactions/opentxs/blob/c531e0faccfa370bb761711ff28ad2386abbcd75/include/opentxs/core/OTStorage.hpp#L148

[OTASCIIArmorCpp]: https://github.com/Open-Transactions/opentxs/blob/c2c362e9fd77422fc1d0d82d0db6a58aa36c69db/src/core/crypto/OTASCIIArmor.cpp

[OTStorageCpp]: https://github.com/Open-Transactions/opentxs/blob/7d2e2dbeca45256ce91b41f63d4aadb88f947169/src/core/OTStorage.cpp

[OTStorageHpp]: https://github.com/Open-Transactions/opentxs/blob/7d2e2dbeca45256ce91b41f63d4aadb88f947169/include/opentxs/core/OTStorage.hpp

[OTStoragePbHpp]: https://github.com/Open-Transactions/opentxs/blob/7d2e2dbeca45256ce91b41f63d4aadb88f947169/include/opentxs/core/OTStoragePB.hpp

[Base64]: http://en.wikipedia.org/wiki/Base64

[Deflate]: http://en.wikipedia.org/wiki/Zlib

[GenericsProto]: https://github.com/Open-Transactions/opentxs/blob/7d2e2dbeca45256ce91b41f63d4aadb88f947169/src/core/otprotob/Generics.proto

[boostBase64]: http://www.boost.org/doc/libs/1_36_0/libs/serialization/doc/dataflow.html

[golangBase64]: http://golang.org/pkg/encoding/base64

[CreateInnerContents]: https://github.com/Open-Transactions/opentxs/blob/develop/src/core/OTContract.cpp#L2172

[Opentxs295]: https://github.com/Open-Transactions/opentxs/issues/295
