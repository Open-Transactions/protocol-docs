# opentxs-protocol

Documentation for the current opentxs protocol and spec for a new version.

## Protocol Documentation


### Document Types

The directory [content/docs/xml/](content/docs/xml/) contains descriptions of
the XML structure that is created.

* [Document Type `<OTuser>`](content/docs/xml/notaryProviderContract.md)
* [Document Type `<notaryProviderContract>`](content/docs/xml/notaryProviderContract.md)
* [Document Type `<OTmessage>`](content/docs/xml/OTmessage.md)
* [Document Type `<digitalAssetContract>`](content/docs/xml/digitalAssetContract.md)
* [Document Type `<accountLedger>`](content/docs/xml/accountLedger.md)
  * [Document Type `<transaction>`](content/docs/xml/transaction.md)
    * [Document Type `<item>`](content/docs/xml/item.md)


### Implementation

The directory [content/docs/](content/docs/)
summarizes the parsing and serialization methods of various classes in
`opentxs`.

* [OTASCIIArmor](content/docs/OTASCIIArmor.md)
* [OTContract](content/docs/OTContract.md)
  * [OTMessage](content/docs/OTMessage.md)
  * [OTTransactionType](content/docs/OTTransactionType.md) (TODO)
  * [OTServerContract](content/docs/OTServerContract.md)
* [OTServerConnection](content/docs/OTServerConnection.md) (TODO)
* [Client sending to Server transport](content/docs/transport_client_sending.md)
* [Server receiving from Client transport](content/docs/transport_server_receiving.md)

### Guidelines

Please use links to commit hashes, not to branches (hit the `y` key if you are
looking at a file on Github to get a stable link).


## Protocol Specification

A specification for the new protocol can be found in [content/spec/](content/spec/).


* [DocumentTypes](content/spec/DocumentTypes.md): How we will describe messages
* [NotaryMessages](content/spec/NotaryMessages.md):
  Document types for communication between Client and Notary.

### Encoding

* [SectionFormat](content/spec/SectionFormat.md) (might become deprecated)


# Documentation Format

All documentation is written in Markdown. To help readability of text and diffs
please hard-wrap text at 72 chars, and code blocks at 79.

Recommended vim settings:
```
set textwidth=0 wrap linebreak nolist
```
