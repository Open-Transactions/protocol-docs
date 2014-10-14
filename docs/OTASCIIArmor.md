# OTASCIIArmor

The opentxs class `OTASCIIArmor` defines an encoding for certain data types to
an array of printable ASCII characters. This is useful in two contexts:

* Embedding binary data in a plain-text document. The _Section Format_ used in
  [OTContract](OTContract.md) is defined as a document that is readable by
  humans.  In order to be able to attach signatures, it is necessary to encode
  the binary data in [Base64][Base64].
* Embedding plain-text strings in XML documents. As structured data format, it
  is impossible to safely embed arbitrary data in an XML file when using string
  concatenation, which is currently used to assemble XML in opentxs.  Plain-text
  data can contain characters with special meaning, like `<` and `&`.

## Encoding Algorithm

#### Packing

Armoring is defined for three input types:

* `OTData` and `OTString`. Both input types are converted to byte arrays using
  the OT-defined default packer, which is hard-coded to Protocol Buffers in the
  current source code ([Source][ProtobufHardcode]). The same packer must be used
  on the client and server.
* `std::map<std::string, std::string>`. This data type is encoded using Protocol
  Buffers as well.

The packing of these generic data types is defined here:

* [Generics.proto][GenericsProto]
* TODO document more.

#### Compression (strings only)

When the input is of type `OTString`, the packed data is compressed using the
[_zlib_ `deflate` algorithm][Deflate].

#### Base64-Encoding

The resulting byte array is Base64-encoded and stored using the superclass
method `OTString::Set(string)`.

The encoded can be retrieved via the `OTString::Get()` method.

#### Adding Bookends and Headers (Optional)

The class supports methods for writing bookends around the encoded data
using the Method `OTASCIIArmor::WriteArmoredString()`. The format is as follows:

```
-----BEGIN OT ARMORED $type-----
Version: Open Transactions %s
$base64
-----END OT ARMORED $type-----
```

Where `$type` is passed as a method parameter.

## Decoding Algorithm

The decoding steps mirror the encoding steps:

1. Bookend parsing (optional with `OTASCIIArmor::LoadFromString()`)
1. Base64-decode
1. Uncompress (strings only)
1. Unpack

# Notes

## Armoring in Other Contexts

[Ricardian contracts](http://iang.org/papers/ricardian_contract.html) form the
foundation of OT. A Ricardian contract is a human and machine readable text and
signed with OpenPGP in clear text form (see section 3.1 of aforementioned
document). The OpenPGP [RFC](http://tools.ietf.org/html/rfc4880) states that in
the case where there is a risk of damage to the data due to transport, data
conversions or character set translations the content may be armored.


## Network Message Format Depends on Storage System

The _Packing_ stage of armoring depends on the `OTDB` subsystem, which is
designed as an abstraction for local storage. The implementation allows choosing
between different storage and encoding backends to some extent. It is possible
to choose between the `MessagePack` and `ProtocolBuffer` libraries for packing.
This however requires that server and client must be compiled using the same
Packer in order to communicate.

## Unnecessary Operations

Armoring combines three separate steps: packing, compression and Base64
encoding. In many cases, some or all of the steps are unnecessary and waste
resources.

#### Unnecessary Packing

In addition being dependent on a compile-time flag, there is no discernible
reason for packing. The comments indicate:

> I added these pieces 1-by-1 over time. At first the messages were too int64_t,
so I started compressing them. Then they were not binary compatible across
various platforms, so I added the packing.

It is unclear what is meant by this. Byte sequences are always binary compatible
across platforms. If anything, packing as implemented now increases
platform-dependence.

Human readability being a priority, an XML encoding of a `string ->
string` map is preferable to packing with a binary format. This massively
simplifies the messaging protocol by removing an unnecessary dependency:

```xml
<map>
    <entry key="$key"> $value </key>
    <!-- more entries -->
</map>
```

#### Unnecessary Compression

It is unclear whether compression and subsequent Base64 encoding reduces the
size of the string. The Base64 encoding has a fixed space overhead of 33%
([Source][Base64]).

Still, a compressed, Base64-encoded string might take less space than the
unprocessed equivalent. However, it is possible that this decreases the level of
achievable compression of the overall XML document which contains armored
sections.

Comparative measurements are necessary here.

#### Unnecessary Base64

In some cases, Base64-encoding of binary data is applied even though there is no
need to:

1. Base64-encoding a message before sending it via the ZeroMQ transport.

## Loss of Readability

Each contract can have multiple nested contracts embedded in them. It is
entirely possible that one needs to run the de-armor operation on a message
several times before it is fully human readable.

Using [`CDATA`](http://en.wikipedia.org/wiki/CDATA) sections in order to embed
plain text data should be explored. Where byte-equivalence is necessary
(contract signatures), it must be ensured that whitespace is not modified. An
XML serializer might change line endings and indentation.

One way to achieve byte equivalence is to normalize embedded sections. The
normalization algorithm would trim whitespace for each line and set the line
ending to `\n`.


# Future Changes

In order to simplify the protocol, we should explore the option of dropping
armoring in the message protocol. Binary data can be encoded with traditional
Base64 encoding and does not require a new encoding format.

Outside the protocol, the _packing_ stage in armoring can almost certainly be
dropped for data and strings.

<!--- links -->

[ProtobufHardcode]: https://github.com/Open-Transactions/opentxs/blob/c531e0faccfa370bb761711ff28ad2386abbcd75/include/opentxs/core/OTStorage.hpp#L148

[Base64]: http://en.wikipedia.org/wiki/Base64

[Deflate]: http://en.wikipedia.org/wiki/Zlib

[GenericsProto]: https://github.com/Open-Transactions/opentxs/blob/7d2e2dbeca45256ce91b41f63d4aadb88f947169/src/core/otprotob/Generics.proto
