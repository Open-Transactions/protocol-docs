---
title: "Document Type"
---

# Document Type

Open-Transactions uses XML for serialization of local data and network messages.

The specification describes all _Document Types_ that occur in
Open-Transactions.

This is usually done using XML schemas, but given the complexity of schema
languages, we will use our own language. A document type describes the XML
elements, their attributes and sub-elements. We will not use any advanced XML
features like namespaces, entities or transformations.

## Document Type Definition

A document type consists of

* The name (ASCII alphabetic characters). Same as the name of the root element.
* A list of attributes and their data types.
* A list of sub-elements and their data types.

Element names must be in `PascalCase`, attributes in `lowerCamelCase`.

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

* **Key-Value Pairs**: Keys and values are UTF8-encoded strings. Serialized as
  a list of `<Item key="$key">$value</Item>`.
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
attributes and elements.

## Example

* The document types described in the [Notary Messages](NotaryMessages.md)
  document.

# Notes

* Data types that might be included:
   * **Big Integer**
   * **Decimal** (Double will not be
       supported: http://stackoverflow.com/questions/3730019/)
* A formal definition of document can be translated into source code.
  [Example (TODO)](CodeFromSpecification.md)
