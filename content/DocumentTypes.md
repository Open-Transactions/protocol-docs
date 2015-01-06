# Document Types

The structure and meaning of the messages exchanged between Client and Notary is
described by _Document Types_.

A document type consists of

* A list of attributes and their data types.
* A list of sub-elements with attributes and content.

## Example

TODO I want to use `<notaryProviderContract>` and am waiting for a merge.

## Data Types

An attribute can have one of these types:

* **Boolean**: Either the string `true` or `false`.
* **Integer**: ASCII-encoded signed number.
* **Timestamp**: Human-readable UTC timestamp in ISO8601 format. For example "2015-01-06T14:41:19".
* **String**: UTF-8-Encoded, Plain-Text character sequence. TODO is it really
    UTF-8-encoded?
* **Identifier**: Human-readable representation of a hash value.
* **Armored**: _Deflate_-Compressed and Base64-encoded string. May have
    decorative section markers. See [Section Format](encoding/SectionFormat.md).

Elements can have attributes and contain these *additional* types:

* **Key-Value Pairs**: Protocol-Buffer encoded key-value pairs. TODO clean up or
    describe.
* **Sub-Document**: the element contains a sub-document. Usually signed and
    armored.
* **Signed**: Messages and some armored documents are signed. Signed content and
  signature are encoded using the [Section Format](encoding/SectionFormat.md).

Elements can be optional or occur repeatedly.

## Inheritance

Some document types have commonalities (UserCommands, `<notaryProviderContract>`
and `<instrumentDefinition>`). The commonalities can be described in _Base
Document Type_ which other element types can then inherit the elements and
attributes from.

## Tip: Command line dearmoring

Armored data can be decoded using this command, if `openssl` was compiled
with `zlib` support:

```sh
alias dearmor="sed '/[-:]/d' | base64 -d | openssl zlib -d"
cat ~/.ot/armoredstuff | dearmor
```

## More Examples

* See [`doctypes`](doctypes/)
