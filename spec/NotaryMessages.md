# Notary Messages

Client and Notary communicate through a request-reply model. The Client sends a
message deriving from `NotaryRequest` and message deriving from `NotaryReply`.


# Base Document Types

## Document Type "NotaryRequest"

This document type is the base type for all requests written by the Client and
read by the Notary.

It has some default attributes that are shared by deriving document types.

Default Attributes:
* `NymID`: Identifier. The Nym which makes the request.
* `NotaryID`: Identifier. The Notary which should process the request.
* `RequestNum`: Integer. The index of the current request being sent.
  See [RequestNumbers.md](RequestNumbers.md) for more information.


## Document Type "NotaryReply"

This is the base type for messages sent by the Notary in response to a
_NotaryRequest_ by the Client.

The element name is the same as for the corresponding request name, but ends with `Reply`.

Default Attributes:

* `NymID`: Identifier. The Nym which made the request.
* `NotaryID`: Identifier. The id of the notary
* `RequestNum`: Integer. Same as in the request.
* `Success`: Boolean. Indicates whether request was successful.

Default Elements:

* `InResponseTo`: Identifier. Hash of request message.

## Notes

* The old protocol had the attribute `serverID` instead of `notaryID`.
* The old protocol version has an `@` prefix for Notary responses instead of a
  `Reply` suffix.
* A few User Commands do not inherit all of the attributes. These exceptions are
  documented for the inheriting document type.


# UserCommand `RegisterNym`

## Document Type `RegisterNym`

Elements:

* `CredentialList`: Contains a signed document of type
  [PublicNymfile](../PublicNymfile.md)
* `Credentials`: Contains signed Key-Value pairs. TODO.


## Document Type `RegisterNymReply`

Elements:

* `Nymfile`: Contains a signed document of type
  [PublicNymfile](../PublicNymfile.md)

## Notes

* The original root element name was `createUserAccount`. The new element name
  matches the new naming conventions.
* The attribute `RequestNum` has the fixed value of `1` in this message.
* `CredentialList` and `Credentials` can probably be merged into one document
  type.


# UserCommand `NotarizeTransactions`

## Document Type `NotarizeTransaction`

Attributes:

Elements:

## Document Type `NotarizeTransactionReply`

Attributes:

Elements:


# UserCommand `GetTransactionNum`

## Document Type `GetTransactionNum`

Attributes:

Elements:


## Document Type `GetTransactionNumReply`

Attributes:

Elements:

# UserCommand `CreateAccount`

## Document Type `CreateAccount`

Attributes:

Elements:


## Document Type `CreateAccountReply`

Attributes:

Elements:

# UserCommand `IssueAssetType`

## Document Type `IssueAssetType`

Attributes:

Elements:


## Document Type `IssueAssetTypeReply`

Attributes:

Elements:

# UserCommand `NotarizeTransaction`

## Document Type `NotarizeTransaction`

Attributes:

Elements:


## Document Type `NotarizeTransactionReply`

Attributes:

Elements:

# UserCommand `GetRequest`

## Document Type `GetRequest`

Attributes:

Elements:


## Document Type `GetRequestReply`

Attributes:

Elements:

# UserCommand `GetNymbox`

## Document Type `GetNymbox`

Attributes:

Elements:


## Document Type `GetNymboxReply`

Attributes:

Elements:

# UserCommand `GetBoxReceipt`

## Document Type `GetBoxReceipt`

Attributes:

Elements:


## Document Type `GetBoxReceiptReply`

Attributes:

Elements:

# UserCommand `GetAccountFiles`

## Document Type `GetAccountFiles`

Attributes:

Elements:


## Document Type `GetAccountFilesReply`

Attributes:

Elements:

# UserCommand `ProcessInbox`

## Document Type `ProcessInbox`

Attributes:

Elements:


## Document Type `ProcessInboxReply`

Attributes:

Elements:
