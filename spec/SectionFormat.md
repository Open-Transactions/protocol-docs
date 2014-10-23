# Section Format

Open-Transactions uses the _Section Format_ in some plain-text documents. This
is the general appearance of a section:

```
-----BEGIN $TYPE-----
Key: Value
Other-Key: Value

payload plain-text
which can have multpile lines
-----END $TYPE-----
```

Sections are used in different document types, which are defined later.

## Specification

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
markers.

An unterminated section is illegal. Section termination is defined separately
for each section type.

Some, but not all sections are terminated with the _default section end marker_
`-----END $type-----`.

## Similarity to Other Formats

Section-Format has many similarities with other formats used in cryptographic
messaging:

* [RFC1421](https://tools.ietf.org/html/rfc1421): PEM Format
* [RFC4880](https://tools.ietf.org/html/rfc4880): OpenPGP Message Format

## Section Validity

The classes can define further constraints on the sections (XML validity,
semantic validity, signature and Base64 encoding). These constraints can be
defined for each section type.


## Usage In Open-Transactions

* [OTContract](../docs/OTContract.md): Contracts should be easy to read by humans
    and easy to parse by computers, as per Ricardian Contract criteria. A
    contract contains a list of digital signatures, which are encoded in Base64.
    Content and signatures are encoded using the section format.

* [OTASCIIArmor](../docs/OTASCIIArmor.md): Section Format is used here in order
    to provide human readers a hint to the type of the encoded data.


These use of the Section Format in these instances is defined below.

# Signed Message Document

This document type is defined by the _OTContract_ class and is used to attach
signatures to a plain-text message.

A signed message MUST contain a _content section_ that CAN be followed by zero
or more _signature sections_.

Lines that are outside sections are ignored.

#### Content Section

The first section in a document is the content section. The type specifier MUST
start with the character sequence `SIGNED`.

The content section is not terminated by the default section end marker, but by
the start of a signature section or `EOF`.

#### Signature Section

A signature section is defined by a section whose type specifier ends with
`SIGNATURE`.

Signature sections are terminated the _section end marker_:
`-----END $type -----`

#### Sample Signed Message

```
-----BEGIN SIGNED CONTRACT-----
Hash: $hashType


$xmlContent

// note missing end marker

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


# ASCII-Armored Document

Defined by the _OTASCIIArmor_ class. Here, section format is used to add a type
hint for a Base64-encoded data block.

An ASCII-Armored document consists of exactly one section. The type specifier
MUST start with `OT ARMORED`. The section is terminated by the default section
end marker.

Lines outside the section markers MUST be ignored.

The payload is encoded in Base64 which MAY contain line breaks that are to be
stripped.

The parsing routine MUST Base64-decode the payload.

Note: currently, the Base64-decoded content is still encoded using Protocol
Buffers and may also been compressed using the zlib DEFLATE algorithm. These
encodings may be removed in the future and are thus not part of this
specification.

#### Sample Document

```
-----BEGIN OT ARMORED FILE-----
Version: Open Transactions 0.93.0-gd6f687f
Comment: http://github.com/FellowTraveler/Open-Transactions/wiki

eNptk8mOo0gQhs/DU1h1LXWxGQOlrmphzGoDBmO2G4Y0e7Iv5knnccbVPVL3SBO3
+CPjj1QoPuTvv759xV6QFH1zUSRdOGxE5ST8VBE57NP3zYXTfAT5/mOpys0Euj6r
4ccL/oa9/Ph8yn2WQBCLWQmQ31XiWUU2ZR2F5SHrPl7go+qfwv35CoYV+HjhpnCo
F+4ER8etKcWXfRfKD1YIlihccyyPJ23ts0C6eevL5mvMV+s5fJR1GH8iQG+coh+N
uuA4ywZr26L1eMQLTnW8GvpmYUfHVnCXeicydagk+9g+ry2rBYS/VOBKIQ8HxjxJ
s5VMEizby6rnrK1Fr9g9gWe0iRK9rPA1LNARiLeZeZX7c+z1uCz1JHBClkJyNOSO
gyyJhX8OcV4fNGaNR3fa350gwqDebS+6mBw0tLIWPPYp1+QuZ4ktp5QebU2HyCFs
jqIm2uQwSq+BK8r2sh0GeWjpQCvF5JHjDXULArteLGzbbBN6wGQgnJdj2GAqzl6Q
vG14hrw6OJaR7Kio6ZixfEsP1bkdq4v1MGT/cDO2tB94ox8U97KmJWDavsScqDha
GGSPGT0gylxBGcILilImHMOjCcyqu91Q77zVNulOhnqT5HSfsueVM8F6FTgCv1wh
2VqIQBQzmT7Sju2D/G6MBdyv3gAfsL2DS3kbFBpW5utM4E1adHvoiqKURmEacQEc
PNiySECusuXKfJ1y6YPNsMBpawOnVOdSszE++DXtdwfNlyDvHOVL9fy7Hjf57e5y
B3BQJQmBzURQj9PsyssFRLSaL8BnPJNqbYFiIyZvAim7yZMKBK9N3G5Eb3u9h3e5
1DFpq110hGsJYuEgv0PDcmGmRFRYewWZK22rq5B0GSbGmoobkSHws8hTmV236rRc
D3QfYJzHCUiauDd1jGqoaAHLogKJruR5nmbpsYeLh30g39H/XO4z/w3MJ/IHfF/U
/SSQs6/Wv/w5v3h63xgNgBu7C2EfRsNT6TfYG0u+Yd+SeHffMfQd4euqAnB436TD
0LyjaJIN6Xh7i+oKFUFZ1vOzewIl6NAvr29/eqFzVmTI04HbT4L3euU55SQZjLkV
dvlhNc+ukHhrIo6wlPxsHHf747FgiTJxsv68143T62ERVvWOxFBR9XBWCgNlk3JL
WSJfuul2Z6Z9QczAsiyP6MXnnc1b1tS51zrX8J7VuJkGp2W+yUhkUjE5rvdARGHb
RUZhREIix1TvmZO4lWg39ZhCqecZrBKPMx+/1ifoh/9b3j9H4ZNi
-----END OT ARMORED FILE-----
```

Payload (after unpacking and decompression, using `opentxs decode`)

```
-----BEGIN SIGNED FILE-----
Hash: SAMY

<?xml version="1.0"?>

<signedFile
 version="2.0"
 localDir="nyms"
 filename="AvatoxALnuVWo5IYHYWnHy9EZxcazj0jdvMzsiZGbXz" >

<filePayload>
eNpVksuOokAARTezqq/ouK1kAJVXonYQkTcKqEWxo6F8oaIgBdTPzq9MZ2YxmeU5
yVndC379mH3299sHJXVzqR7z0fgnP/pcgNlm1zak/ueFbw8+HsPdXs1HGs3eVa95
j/aAKtHGFkYPa1CNtM8zduWvBfVZc0nNr4SNFgDM/mRx1dY5WQASPG9lvh7uTMNn
DapKFMFT3tuG+ZWFHTx4ttHtq7ZMlFgyj1p5bZZToxR04p4g7t0HeEPxKap0J19S
jqpC83UV10i39uIJhui9Cq7tmPqumSRyOHYDbO47YZXuYZkflo7GeQTYG8L5dcx8
B0Ose2ljI/82XZklH2VOX720Ror6to6XzTQ7rHnNpgj7sh9PzAQezUEA21SUn3qR
E2kw3hyhr9sZjfOuknBzXtnynqfeSlbtI7nmQ+w21phkrBnWFFGhcahcAZntXnq9
Z3zHRWHCohAhy9i0ZVqoO15JVSo9d1tYo7YrDMYGnCVKHSmTYGNdpjbfWADeDJGG
npv25yLwWHxSec7JjxeY8XQ5qTE59c8jpZGibHvJeEXqgWru/bBNsnfHlN0G4MSN
Aq22xAnC6/alx8vgFI9TzeiWG4mUEgri0FdMJ1OcOECwFC5iToqJvxUD7sZ0AXAE
hgWbJuconIMZ99/E3/z3PwvwGyBnxX0=
</filePayload>

</signedFile>
-----BEGIN FILE SIGNATURE-----
Version: Open Transactions 0.93.0-gd6f687f
Comment: http://github.com/FellowTraveler/Open-Transactions/wiki

CoABvEX+UCAILGO8Q4E6jDzQPWEgXzgFunlGYiuu6BKKk92lgVisPBNOL+DxEzJf
dnIJNawIkO/9gl45RFClWh46Qhsk2weRRRX2sFlH2w49QNA+ojM1s9MAw7eLxwbH
cQ5d3uzfZF/nqrcOkOcEgHd5sXQvF4G7WhX8kIowwezGC18=
-----END FILE SIGNATURE-----
```

The resulting document is of type _Signed Message_.

The data inside `<filePayload>` is also referred to as "ASCII-Armored" in
opentxs. However it is outside of this specification since it is not
section-formatted.

## Parsing Pseudocode (Golang)

### For Sections

```go
type Section struct {
    Type    string
    Headers map[string]string
    Payload string
}

func ParseSection(doc string) (Section, error)

// For sections without end marker (Content in Signed Message)
func ParseUnterminatedSection(doc string) (Section, error)
```

### For Signed Message Documents

Access to type and header information might be useful, so we should return all
section data.

```go
type SignedMessage struct {
     Content    Section
     Signatures []Section
}

func ParseSignedMessage(doc string) (SignedMessage, error)
```


### For ASCII-Armored Documents

Loading ASCII-Armoring only decodes the Base64 payload. Type information
and headers are ignored.

```
func ParseAsciiArmor(doc string) ([]byte, error)
```


## Compliance in Opentxs

Long-term, all documents created and accepted by opentxs (`OTContract.cpp`,
`OTASCIIArmor.cpp` and others) should conform to this specification.

Making the parsers reject all malformed input can only be achieved via a larger
rewrite.

The current opentxs parsers do not comply with this specification and have
several bugs.

Changes in the document reading methods to accept valid documents:

* Do not check number of spaces in header fields.
* Accept arbitrary string values in header fields.

Changes in the document reading methods to reject malformed documents:

* Section start markers must be checked entirely.
* Signature sections must require the section end marker instead only a dash.
* Headers must only appear at the top of a section and are terminated by an
  empty line.


## Future Improvements

This version of the specs aims for some compatibility with the current output of
the writing routine. Possible improvements that can be made in future versions.

### Dash-Escape Sequence

The dash-space escape should be excluded from the payload. This would simplify
other processing steps, because it would allow the payload to contain
arbitrary plain text (other Section-Formatted text in particular).

"Section" would then contain this paragraph:

> A document MUST NOT contain lines starting with two dashes that are not section
markers. In order to encode a line like this, the dash-space (ASCII `0x2d 0x20`)
escape sequence can be prepended when encoding the document. During decoding,
the sequence must be removed from the beginning of a line before adding it to
the payload.

### Lines Outside Content Sections

Lines outside sections are ignored. This is a source of ambiguity in the
document. It is unclear whether lines outside section markers are ever generated
in opentxs.

Suggestion: Make non-whitespace lines outside sections illegal.


### End Markers

The content section in signed messages does not use the end marker.

The signature section writing routine writes an end marker, but the parser only
looks for a single terminating dash. This behavior is used in the CLI tool
however.

The ASCII-Armored section writes an end marker and requires (part) of an end
marker.

Suggestion: In order to simplify the specification, always require section end
markers.

