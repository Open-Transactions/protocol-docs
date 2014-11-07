# opentxs-protocol

Documentation for the current opentxs protocol

### Document Types

The directory [content/doctypes/](content/doctypes/) contains descriptions of
the XML structures that are created by `opentxs`.

* [Document Type `<notaryProviderContract>`](content/doctypes/notaryProviderContract.md)
* [Document Type `<digitalAssetContract>`](content/doctypes/digitalAssetContract.md)
* [Document Type `<OTuser>`](content/doctypes/OTuser.md)
* [Document Type `<OTmessage>`](content/doctypes/OTmessage.md)
* [Document Type `<accountLedger>`](content/doctypes/accountLedger.md)
* [Document Type `<transaction>`](content/doctypes/transaction.md)
* [Document Type `<item>`](content/doctypes/item.md)


### Description of Implementation

The files in the directory [content/docs/](content/docs/) summarize the
parsing, serialization and transport methods in `opentxs`.

* [OTASCIIArmor](content/docs/OTASCIIArmor.md)
* [OTContract](content/docs/OTContract.md)
  * [OTMessage](content/docs/OTMessage.md)
  * [OTServerContract](content/docs/OTServerContract.md)
* [Description of Client sending to Server](content/docs/transport_client_sending.md)
* [Description of Server receiving from Client](content/docs/transport_server_receiving.md)


# Documentation Format

All documentation is written in Markdown. To help readability of text and diffs
please hard-wrap text at 72 chars, and code blocks at 79.

Please use links to commit hashes, not to branches (hit the `y` key if you are
looking at a file on Github to get a stable link).
