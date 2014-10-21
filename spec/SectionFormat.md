# Section Format

This format is used in the [OTContract](../docs/OTContract.md) class in
Open-Transactions. Its purpose is to encode a human-readable, plain-text
document together with a list of digital signatures.

The format should be easy to parse (automatically) and easy to read (manually)
at the same time, as per the Ricardian contract criteria.



## Document Structure

A document MUST contain a content section that CAN be followed by zero or more
signature sections.

Lines that are outside sections are ignored.

### Sections

The document is read line-by-line. Line endings can be `\r\n` or `\n`.

A section consists of:
* a type specifier string;
* headers as key-value pairs of strings;
* a payload string

The start of a section is defined by a section marker line. The line has five
leading dashes, followed by the BEGIN keyword and a type specifier, ending with
five dashes: `-----BEGIN $type-----`

The section marker line is followed by headers, in the form of header lines.
Header lines have the format `Key: Value`, where key and value are separated by
a colon-space sequence (ASCII `0x3a 0x20`).

Headers are terminated by an empty line.

All subsequent lines define the payload. Line endings are normalized to `\n`.

A document MUST NOT contain lines starting with two dashes that are not section
markers. In order to encode a line like this, the dash-space (ASCII `0x2d 0x20)
escape sequence can be prepended when encoding the document. During decoding,
the sequence must be removed from the beginning of a line before adding it to
the payload.

An unterminated section is illegal. Section termination is defined separately
for each section type.

### Content Section

The first section in a document is the content section. The type specifier must
start with the character sequence `SIGNED`.

The content section is terminated by the start of a signature section or `EOF`.

### Signature Section

A signature section is defined by a section whose type specifier ends with
`SIGNATURE`.


Signature sections are terminated the _section end marker_: `-----END $type -----`

### Sample Document

```
-----BEGIN SIGNED CONTRACT-----
Hash: $hashType


$xmlContent

-----BEGIN CONTRACT SIGNATURE-----
Version: OpenTransactions 0.99
Comment: http://github.com/FellowTraveler/Open-Transactions/wiki
Meta: $signatureTag

$signatureData
-----END CONTRACT SIGNATURE-----

-----BEGIN CONTRACT SIGNATURE-----
Version: OpenTransactions 0.99
Comment: http://github.com/FellowTraveler/Open-Transactions/wiki
Meta: $signatureTag

$signatureData
-----END CONTRACT SIGNATURE-----
```


## Parsing Pseudocode (Golang)

```go
type Section struct {
    Type    string
    Headers map[string]string
    Payload string
}

type Document struct {
     Content    Section
     Signatures []Section
}

func ParseDocument(doc string) (Document, error)
```

## RFC 1421

This format has many similarities with the PEM format and the OpenPGP Message
Format:

* [RFC1421](https://tools.ietf.org/html/rfc1421)
* [RFC4880](https://tools.ietf.org/html/rfc4880)

## Section Validity

The OTContract class defines further constraints on the content section (XML
validity, semantic validity) and the signature sections (validity of signature
format). These are not covered in this spec, but in other documents.

## Discussion

This version of the specs aims for some compatibility with the current output of
the writing routine. Possible improvements that can be made in future versions:

Lines preceding the content section are ignored. This is a source of ambiguity
in the document. Suggestion: require the first line of the document to be the
content section marker.

The content section does not use the end marker.  The signature section writing
routine writes an end marker, but the parser only looks for a single terminating
dash. This behavior is used in the CLI tool however.


## Compliance in Opentxs

Long-term, all documents created and accepted by opentxs (`OTContract.cpp`)
should conform to this specification.

Compliance of the serialization routine can be achieved with a series of small
changes.

Making the parser reject all malformed input can only be achieved via a larger
rewrite.

### Necessary Changes

The current opentxs writer and parser do not comply with this specification and
have several bugs.

Changes in the document generation methods: (relevant for signature generation)

* Header lines must be excluded from the payload.
* The dash-space escape sequence must be excluded from the payload.

Changes in the document reading methods to accept valid documents:

* Header lines must be excluded from the payload.
* The dash-space escape sequence must be excluded from the payload.
* Do not check number of spaces in header fields.
* Accept arbitrary string values in header fields.

Changes in the document reading methods to reject malformed documents:

* Section start markers must be checked entirely.
* Signature sections must require the section end marker instead only a dash
* Headers must only appear at the top of a section and are terminated by an
  empty line.

