# Document Type

Open-Transactions uses XML (and some hand-written formats) for serialization of
local data and network messages.

The specification describes all _Document Types_ that occur in
Open-Transactions.

This is usually done using XML schemas, but given the complexity of schema
languages, we will use our own language. A document type describes the XML
elements, their attributes and sub-elements. We will not use any advance XML
features like namespaces, entities or transformations.

## Document Type Definition

A document type consists of

* The root element name (ASCII alphabetic characters)
* A list of attributes
* A list of sub-elements

## Data Types

An attribute can have one of these types:

* **Boolean**: Either the string `true` or `false`.
* **Integer**: ASCII-encoded signed number. Should fit into the signed 64 bit
    data type.
* **String**: UTF-8-Encoded, Plain-Text character sequence.
* **Identifier**: Base58-Encoded characters sequence of a 256-bit hash value.
* **Binary**: Base64-Encoded character sequence. Optional whitespace
    (line breaks) is ignored during decoding.

Elements can have these *additional* types:

* **Key-Value Pairs**: Keys and values are UTF8-encoded strings.
* **Sub-Document**: the element contains a (signed) sub-document. This is key
  for building complex messages.

Elements may be optional or appear multiple times.

## Signed Documents

Documents can be encoded in plain XML, or as *signed documents*, consisting of a
content section and a signature section. This is currently achieved with the
[Section Format](SectionFormat.md), but may be different in the protocol defined
by this specification.

## Inheritance

Some document types have commonalities (User Commands). One way to express these
commonalities is by _inheritance_.

When a document types inherits from another document type, it inherits the
type name, the attributes and elements.

## Example

* The document types
  [NotaryRequest and NotaryReply](NotaryRequestAndNotaryReply.md)
  document types of the user command [RegisterNym](UserCommand/RegisterNym.md).

# Notes

* Data types that might be included:
   * **Big Integer**
   * **Double**
* A formal definition of document can be translated into source code.
  [Example](CodeFromSpecification.md)
