# opentxs-protocol

Documentation for the current opentxs protocol and spec for a new version.

## Protocol Documentation

The documentation process happens in [docs/](docs/). Currently, it's just
summarizing the parsing and serialization format of various classes and the
communication system.

* [Armoring](docs/OTASCIIArmor.md)
* [OTContract](docs/OTContract.md)
  * [OTMessage](docs/OTMessage.md)
  * [OTTransactionType](docs/OTTransactionType.md) (TODO)
  * [OTServerContract](docs/OTServerContract.md)
* [OTServerConnection](docs/OTServerConnection.md) (TODO)
* [Client sending to Server transport](docs/transport_client_sending.md)
* [Server receiving from Client transport](docs/transport_server_receiving.md)

### Guidelines

The goal is to provide summaries for the various message types. Message types
are strongly correlated to classes that derive from
[OTContract.cpp](https://github.com/Open-Transactions/opentxs/blob/3d651e813b20
5d44a467b2c91ee7782a8d4ebe5a/src/client/OTClient.cpp), so it makes sense to
just write a summary for each deriving class and write a _Notes_ section that
describe improvements or other observations.

The communication system is defined by these classes (non-exhaustive)

* [OTClient.cpp](https://github.com/Open-Transactions/opentxs/blob/3d651e813b205d44a467b2c91ee7782a8d4ebe5a/src/client/OTClient.cpp#L8905)
* [UserCommandProcessor.cpp](https://github.com/Open-Transactions/opentxs/blob/3d651e813b205d44a467b2c91ee7782a8d4ebe5a/src/server/UserCommandProcessor.cpp#L166)
* [OTServerContract](https://github.com/Open-Transactions/opentxs/blob/3d651e813b205d44a467b2c91ee7782a8d4ebe5a/src/core/OTServerContract.cpp)
* [OTServerConnection](https://github.com/Open-Transactions/opentxs/blob/3d651e813b205d44a467b2c91ee7782a8d4ebe5a/src/client/OTServerConnection.cpp)

Please use links to commit hashes, not to branches (hit the `y` key if you are
looking at a file on Github to get a stable link).


## Specification

A specification for the new protocol can be found in [spec/](spec).


* [DocumentType](spec/DocumentType.md): How we will describe messages
* [NotaryMessages](spec/NotaryMessages.md):
  Document types for communication between Client and Notary.

### Encoding

* [SectionFormat](spec/SectionFormat.md) (might become deprecated)


### Guidelines

The goal of the spec is to define a more formalized, cleaned-up "1.1" version
of the current protocol that should be easy to process and implement in
different languages. It should not be too far removed from the current system.


# Documentation Format

All documentation is written in Markdown. To help readability of text and diffs
please hard-wrap text at 72 chars, and code blocks at 79.

Recommended vim settings:
```
set textwidth=0 wrap linebreak nolist
```
