---
title: "Requirements for the Open-Transactions Document Format and Protocol"
menu:
  main:
    parent: other
---

## Requirements for the Open-Transactions Document Format and Protocol

Quote from OpenTransactions.org:

> *The Open-Transactions project is a collaborative effort to develop a robust,
commercial-grade, fully-featured, free-software toolkit implementing the OTX
protocol as well as a full-strength financial cryptography library, API, CLI,
and prototype server.*

Communication within Open-Transactions happens between these parties:

* Persistent Storage
* Client
* Notary
* Auditor services
* Human Auditors and Mediators

This list captures the requirements for the *document format* used in
Open-Transactions, and the *protocol* used for exchanging and interpreting those
documents. This should make it easier for developers to review and improve the
system.


### Simplicity

The format should be *as simple as possible*. Unnecessary features and aspects
should be removed. These requirements will help with most other criteria on this
list.


### Transactionality

Communication should be *state-less and message-based*, as opposed to something
stream-based like FTP or SSH.

As the name implies, most communication in Open-Transactions is transactional
and can be expressed as a message. From a protocol perspective, a message should
contain a document with everything that the recipients needs to know in
order to perform an action.

Exceptions are:
* Application-level state, like Nyms, accounts and balances.
* Request numbers (*Authenticity* requirement)

### Authenticity

A document recipient, as well as third parties, should be able to *verify
authenticity*. The signed content should reflect the intentions of the author
and include:

* The identity of the author.
* The intended recipient.
* The chronological order relative to other messages.
* The desired action to be performed.


### Readability

Documents should be *clear*, *unambiguous* and *easily understandable by
humans*.  This is a requirement for third-party auditors who must make a
decision based on the meaning of a signed document.

A computer must process a document as a human reader would expect it to be
processed.

The only things necessary for determining the meaning and validity of a message
should be the protocol specification and a program for computing hashes and
checking cryptographic signatures.


### Safety

Documents should be *well-formed* in order to be accepted. Ambiguous documents
are difficult to audit and can lead to unpredictable behavior on the system,
which can be exploited.

The message format should be simple, well-specified, and avoid ambiguity.

One solution for that is the use of XML schemas, at the cost of simplicity and
safety (Schema Attacks, XML Bombs).


### Composability

A document should be able to *contain other documents* and be able to *refer to
other documents*.

Document serialization must escape all characters that can break the format.
Documents can refer to other documents using their hash or smart-property
identifier.


### Confidentiality

Documents should *only be readable by the intended recipients*. For example, there
is no expectation that the communication channel between client and server will be
private.


### Efficiency

Since Open-Transactions should be able to run on systems with limited resources:

* Document processing should not require an large amount of memory or CPU.
* Document transport should not require a large amount of bandwidth.
* Document storage on disk should not require a large amount of space.

A simple format and protocol has small documents that are easy to decode. 
Modern XML parsers are very optimized and have minimal space and computation
requirements.

A Notary should be able to quickly identify the resources that need to
be manipulated from a request. This simplifies concurrent processing of
transactions.

Compression can be used to reduce space requirements on disk or network.


### Extensibility

As Open-Transactions continues to grow and improve, it should allow
changes to the specification, implementation, and to the format without
breaking existing systems. This can be achieved, for example, by attaching 
a version number to a document.


## Current State

1. *Simplicity*: The generation and interpretation of messages in
   Open-Transactions is defined by the source code only, which is difficult to
   follow. The new specification should be easy to understand and implement.

1. *Transactionality*: Open-Transactions uses a message-based protocol with a
   minimal amount of state. The messages that are exchanged can be understood as
   signed documents. A single message contains everything that is necessary
   to verify, process and audit the transaction. The Client sends a _request
   message_ to the Notary, which replies with a _response message_.

1. *Authenticity*: Open-Transactions solves this problem with the concept of a
   Nym and public-key cryptography. The actual message, encoded in XML, is wrapped
   inside a container format ([Section Format](SectionFormat.md)) that
   separates content and signatures.  The sender declares his identity in the
   message attribute `nymID`, specifies the intended recipient in the message
   `notaryID`, and declares the chronological order in the `requestNum` attribute.

1. *Readability*: Open-Transactions uses XML as the document format and
   _Section-Format_ as the container format. Encoding schemes that degrade
   readability, like Protocol Buffers and Base64, should be avoided when possible.
   Open-Transactions has few precautions against processing ambiguous messages.

1. *Safety*: Open-Transactions does not use an XML schema parser, avoiding a
   class of exploits that are possible with more fully-featured parsers. However, it
   relies on zlib, Protocol Buffers, and some hand-written parsers (SectionFormat)
   that are complex, difficult to review and contain bugs and unintentional
   behavior.

1. *Composability*: Open-Transactions uses an encoding scheme with Compression,
   Base64-encoding and Protocol Buffers (depending upon a compile-time configuration)
   to embed signed sub-documents. This severely impacts readability.

1. *Confidentiality*: Open-Transactions uses public-key cryptography when
   communicating between Client and Notary. The key management is part of the Nym
   credential system.

1. *Efficiency*: The document format used in Open-Transactions has not been
   optimized for efficiency. For example, initial tests show that avoiding base64
   encoding can reduce message size by half.

1. *Extensibility*: Messages and documents often contain version numbers that
   are currently unused. The current system for reading and writing documents is
   complex and difficult to extend.
