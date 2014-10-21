# Section Format

This format is used in the [OTContract](../docs/OTContract.md) class in
Open-Transactions. It's purpose is to encode a human-readable, plain-text
document together with a list of digital signatures.

The format should be easy to parse.

## Document Structure

The document is read line-by-line. Line endings can be `\r\n` or `\n`.

Lines preceding the content section are ignored.

A document MUST contain a content section that CAN be followed by zero or more
signature sections.

### Sections

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

The dash-space escape sequence (ASCII `0x2d 0x20`) must be removed from the
beginning of a line before adding it to the payload.

A document MUST NOT contain lines starting with two dashes that are not section
markers.

An unterminated section is illegal.

### Content Section

The first section in a document is the content section. The type specifier must
start with the character sequence `SIGNED`. Lines preceding the content section
are ignored.

The content section is ended by the start of a signature section or `EOF`.

### Signature Section
A signature section is defined by a section whose type specifier ends with
`SIGNATURE`.

A signature section must be ended by a line that starts with a dash (ASCII
`0x2d`).

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
* [RFC1421](https://tools.ietf.org/html/rfc4880)

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

The content section and the signature sections are terminated differently, and
not using the end marker. The content section writing routine does not write an
end marker at all. The signature section writing routine writes an end marker,
but only looks for a single terminating dash. This also complicates the spec.
Suggestion: require all sections to terminate using the matching end marker
`-----END $type-----`.


## Compliance in Opentxs

Long-term, all documents created and accepted by opentxs (`OTContract.cpp`)
should conform to this specification.

Compliance of the serialization routine can be achieved with a series of small
changes.

Making the parser reject all malformed input can only be achieved via a larger
rewrite.

## Necessary Changes

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

* Section markers must be checked entirely.
* Headers must only appear at the top and are terminated by an empty line.

