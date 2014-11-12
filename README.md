# opentxs-protocol

Documentation for the current opentxs protocol. The transport is described in
[Transport](content/Transport.md).

## Document Types

The directory [content/doctypes/](content/doctypes/) contains descriptions of
the XML structures that are created by `opentxs`. The format is described in
[Document Types](content/DocumentTypes.md).

* [Base Document Type `<Contract>`](content/doctypes/contract.md)
* [Document Type `<notaryProviderContract>`](content/doctypes/notaryProviderContract.md)
* [Document Type `<digitalAssetContract>`](content/doctypes/digitalAssetContract.md)
* [Document Type `<OTuser>`](content/doctypes/OTuser.md)
* [Document Type `<OTmessage>`](content/doctypes/OTmessage.md)
* [Document Type `<accountLedger>`](content/doctypes/accountLedger.md)
* [Document Type `<transaction>`](content/doctypes/transaction.md)
* [Document Type `<item>`](content/doctypes/item.md)
* [Document Type `<assetAccount>`](content/doctypes/assetAccount.md)

## Description of Implementation

The files in the directory [content/docs/](content/docs/) summarize the
parsing, serialization and transport methods in `opentxs`.

* [OTASCIIArmor](content/docs/OTASCIIArmor.md)
* [OTContract](content/docs/OTContract.md)
  * [OTMessage](content/docs/OTMessage.md)
* [Description of Client sending to Server](content/docs/transport_client_sending.md)
* [Description of Server receiving from Client](content/docs/transport_server_receiving.md)

# Offline rendering

The documentation can be rendered offline by means of the [Hugo](http://gohugo.io/)
static generator, v. 0.12 or greater. Install it, run the `hugo` command in the
top level directory of the repo, and a static website will be generated in the
`public` directory.

You can also run the `hugo server` command and point a browser at
<http://localhost:1313/>.

FIXME: links among documents are currently broken in the rendered website.

# Documentation Format

All documentation is written in Markdown. To help readability of text and diffs
please hard-wrap text at 72 chars, and code blocks at 79.

Please use links to commit hashes, not to branches (hit the `y` key if you are
looking at a file on Github to get a stable link).
