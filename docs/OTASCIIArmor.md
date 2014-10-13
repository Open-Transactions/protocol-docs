# OTASCIIArmor

The opentxs class `OTASCIIArmor` defines an encoding for certain data types to
the printable ASCII character set. This is useful in two contexts:

* Embedding binary data in a plain-text document. The _Section Format_ used in
  [OTContract](OTContract.md) is defined a document that is readable by humans.
  In order to be able to attach signatures, it is necessary to encode the binary
  signature data a Base64 encoded form.
* Embedding data in XML documents. As structured data format, it is impossible
  to safely embed arbitrary data in an XML file when using string concatenation.
  This is particularly true for binary data, but also for plain-text data where
  characters with special meaning in like `<` and `&` can appear.

## Encoding Algorithm

### Packing

Armoring is defined for three input types:

* `OTData` and `OTString`. Both input types are converted to byte arrays using
  the OT-defined default packer, which is hard-coded to Protocol Buffers in the
  current source code [1]. The same packer must on the client and server.
* `std::map<std::string, std::string>`. This data type is encoded using Protocol
  Buffers as well.

### Compression (strings only)

When the input is of type `OTString`, the packed data is compressed using the
zlib `deflate` algorithm.

### Base64-Encoding

The resulting byte array is Base64-encoded and stored using the superclass
method `Set(string)`.

### Adding Bookends and Headers (Optional)

The class supports methods for writing bookends around the encoded data. The
format is as follows:

```
-----BEGIN OT ARMORED $type-----
Version: Open Transactions %s
$base64
-----END OT ARMORED $type-----
```

Where `$type` is passed as a parameter.

## Decoding Algorithm

The decoding steps mirror the encoding steps:

1. Bookend parsing (optional)
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

Armoring always combines three separate steps: packing, compression and base64
encoding. In many cases, some or all of the steps are unnecessary and waste
resources.

### Unnecessary Packing

In addition being dependent on a compile-time flag, there is no discernible
reason for packing. The comments indicate:

> I added these pieces 1-by-1 over time. At first the messages were too int64_t,
so I started compressing them. Then they were not binary compatible across
various platforms, so I added the packing.

It is unclear what is meant by this. Byte sequences are always binary compatible
across platforms. If anything, packing as implemented now increases
platform-dependence.

Human readability being a priority, an XML encoding of a `string ->
string` map is preferable to packing with a binary format and massively
simplifies the messaging format:

```xml
<map>
    <entry key="$key"> $value </key>
    <!-- more entries -->
</map>
```

### Unnecessary Compression

The `map<string, string>` structure is often used to carry data that cannot be
compressed, like credentials.

### Unnecessary Base64

In some cases, Base64-encoding is applied even though there is no need to:

1. Base64-encoding a message before sending it via the ZeroMQ transport.
1. Base64-encoding of plain-text strings. It is possible to encode arbitrary
   plain-text data when using an XML serializer.


### Loss of readability

Each contract can have multiple nested contracts embedded in them. It is
entirely possible that one needs to run the de-armor operation on a message
several times before it is fully human readable.

Plain strings can be embedded safely in XML using
[`CDATA`](http://en.wikipedia.org/wiki/CDATA) sections.

There may be some increase in message length in some cases where the increase in
size due to the base64 encoding is offset by the compression, this however
should be measured and weighted against the loss of readability.


<!--- links -->

[1]: https://github.com/Open-Transactions/opentxs/blob/c531e0faccfa370bb761711ff28ad2386abbcd75/include/opentxs/core/OTStorage.hpp#L148
