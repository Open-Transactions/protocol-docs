---
title: "Document Type"
---

# Document Type

The structure and meaning of the messages exchanged between Client and Notary is
described by _Document Types_.

A document type consists of

* A list of attributes and their data types.
* A list of sub-elements with attributes and content.

# Example

TODO I want to use `<notaryProviderContract>` and am waiting for a merge.


## Data Types

An attribute can have one of these types:

* **Boolean**: Either the string `true` or `false`.
* **Integer**: ASCII-encoded signed number.
* **Timestamp**: ASCII-encoded UNIX timestamp.
* **String**: UTF-8-Encoded, Plain-Text character sequence. TODO is it really
    UTF-8-encoded?
* **Identifier**: Human-readable representation of a hash value. See
    [Identifier.md](encoding/Identifier.md) TODO fix link.
* **Armored**: Base64-Encoded character sequence with some additional
    processing. Explained in [Armoring.md](encoding/Armoring.md) TODO fix link.
    Optional whitespace (line breaks) is ignored during decoding.

Elements can have attributes and contain these *additional* types:

* **Key-Value Pairs**: Protocol-Buffer encoded key-value pairs. TODO clean up or
    describe.
* **Sub-Document**: the element contains a sub-document. Usually armored or
    signed and armored.

Elements can be optional or occur repeatedly.

## Inheritance

Some document types have commonalities (UserCommands, `<notaryProviderContract>`
and `<digitalAssetContract>`). The commonalities can be described in _Base
Document Type_ which other element types can then inherit the elements and
attributes from.

## More Examples

* See directory [`doctypes/`](doctypes/)
