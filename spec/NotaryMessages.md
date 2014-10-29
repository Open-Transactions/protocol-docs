# Notary Messages

Client and Notary communicate through a request-reply model. The Client sends a
message deriving from `NotaryRequest` and message deriving from `NotaryReply`.


# Base Document Types

## NotaryRequest

This document type is the base type for all requests written by the Client and
read by the Notary.

It has some default attributes that are shared by deriving document types.

Default Attributes:
* `NymID`: Identifier. The Nym which makes the request.
* `NotaryID`: Identifier. The Notary which should process the request.
* `RequestNum`: Integer. The index of the current request being sent.
  See [RequestNumbers](RequestNumbers.md) for more information.


## NotaryReply

This is the base type for messages sent by the Notary in response to a
_NotaryRequest_ by the Client.

The element name is the same as for the corresponding request name, but ends
with `Reply`.

Default Attributes:

* `NymID`: Identifier. The Nym which made the request.
* `NotaryID`: Identifier. The id of the notary
* `RequestNum`: Integer. Same as in the request.
* `Success`: Boolean. Indicates whether request was successful.

Default Elements:

* `InResponseTo`: Identifier. Hash of request message.

### Notes

* The old protocol had the attribute `serverID` instead of `notaryID`.
* The old protocol version has an `@` prefix for Notary responses instead of a
  `Reply` suffix.
* A few requests do not inherit all of the attributes. These exceptions are
  documented for the inheriting document type.

----

## RegisterNym

Requests the registration of a new Nym on the server.

Elements:

* `CredentialList`: Contains a signed document of type [Nymfile](Nymfile.md)
* `Credentials`: Contains signed Key-Value pairs. TODO.


## RegisterNymReply

Elements:

* `Nymfile`: Contains a signed document of type Nymfile.

### Notes

* The original root element name was `createUserAccount`. The new element name
  matches the new naming conventions.
* The attribute `RequestNum` has the fixed value of `1` in this message.
* `CredentialList` and `Credentials` can probably be merged into one document
  type.

----

## GetTransactionNum

Requests a new list of [Transaction Numbers](TransactionNumbers.md)

Attributes:

* `NymboxHash`: Identifier. Hash of the current [Nymbox](Nymbox.md) (?).


## GetTransactionNumReply

Attributes:

* `NymboxHash`: Identifier. New Nymbox hash (?).

The list of transaction numbers will be in the Nymbox.

----

## CreateAccount

Requests creation of a new account.

Attributes:

* `AssetTypeID`: Identifier. ID of the [AssetContract](AssetContract.md)


## CreateAccountReply

Attributes:

* `AssetTypeID`: Identifier. Same as in Request.

Elements:

* `NewAccount`: Signed [Account](Account.md) document.

### Notes

* `AssetTypeID` previously was `assetType`


----

## IssueAssetType

Issues an asset and creates an _issuer account_ on the Notary.

Attributes:

* `AssetTypeID`: Identifier. Hash of the AssetContract.

Elements:

* `AssetContract`: Signed document of type [AssetContract](AssetContract.md).

## IssueAssetTypeReply

Attributes:

* `AssetTypeID`: Identifier. Hash of AssetContract.
* `AccountID`: Identifier. Hash of the Account.

Elements:

* `IssuerAccount`: Signed document of type [Account](Account.md).

### Notes

* `AssetTypeID` previously was `assetType`

----

## NotarizeTransaction

Requests the notarization of a transaction. TODO more detail.

Attributes:

* `NymboxHash`: Identifier. Hash of Nymbox.
* `AccountID`: Identifier. Account ID referenced in transaction (?).

Elements:

* `Transaction`: Signed document of type [Transaction](Transaction.md)

## NotarizeTransactionReply

Attributes:

* `AccountID`: Identifier. Account ID referenced in transaction (?).

Elements:

* `ResponseTransaction`: Signed document of type [Transaction](Transaction.md).

### Notes

* `Transaction` previously was deprecated type `Ledger`.

----

## GetRequest

Requests new request numbers.

No additional attributes or elements.

## GetRequestReply

Attributes:

* `NymboxHash`: Identifier. Hash of remote Nymbox. TODO purpose?
* `NewRequestNum`: Integer. Next request number to be used.

----

## GetNymbox

Requests current [Nymbox](Nymbox.md).

## GetNymboxReply

Attributes:

Elements:

----

## GetBoxReceipt

Attributes:

Elements:

## GetBoxReceiptReply

Attributes:

Elements:

----

## GetAccountFiles

Attributes:

Elements:

## GetAccountFilesReply

Attributes:

Elements:

----

## ProcessInbox

Attributes:

Elements:

## ProcessInboxReply

Attributes:

Elements:

