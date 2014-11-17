---
title: "Notary Messages"
---

# Notary Messages

Client and Notary communicate through a request-response model. The Client sends
a message deriving from `NotaryRequest` and message deriving from
`NotaryResponse`.


# Base Document Types

## NotaryRequest

This document type is the base type for all requests written by the Client and
read by the Notary.

It has some attributes that are shared by deriving document types.

Default Attributes:
* `nymID`: Identifier. The ID of the Nym which makes the request.
* `notaryID`: Identifier. The ID of the Notary which should process the request,
  as defined in the [Server Contract](../doctypes/ServerContract.md).
* `requestNum`: Integer. The index of the current request being sent.
  See [Request Numbers](RequestNumbers.md) for more information.


## NotaryResponse

This is the base type for messages sent by the Notary in response to a
_NotaryRequest_ by the Client.

The element name is the same as for the corresponding request name, but ends
with `Response`.

Attributes:

* `nymID`: Identifier. The Nym which made the request.
* `notaryID`: Identifier. The ID of the responding Notary.
* `requestNum`: Integer. Same as in the request.
* `success`: Boolean. Indicates whether request was successful.

Elements:

* `InResponseTo`: Identifier. Hash of request message.

### Notes

* A few requests do not inherit all of the attributes. These exceptions are
  documented for the inheriting document type.

----

## RegisterNym

Requests the registration of a new Nym on the Notary.

Elements:

* `CredentialList`: Contains a signed document of type [Nymfile](Nymfile.md).
* `Credentials`: Contains signed Key-Value pairs. TODO.


## RegisterNymResponse

Elements:

* `Nymfile`: Contains a signed document of type Nymfile.

### Notes

* The attribute `requestNum` has the fixed value of `1` in this message.
* `CredentialList` and `Credentials` can probably be merged into one document
  type.

----

## GetTransactionNumbers

Requests a new list of transaction numbers.

Attributes:

* `nymboxHash`: Identifier. Hash of the current Nymbox.


## GetTransactionNumbersResponse

Attributes:

* `nymboxHash`: Identifier. New Nymbox hash (?).

The list of transaction numbers will be in the Nymbox.

----

## CreateAccount

Requests creation of a new account.

Attributes:

* `assetTypeID`: Identifier. ID of the [Asset Contract](AssetContract.md).


## CreateAccountResponse

Attributes:

* `assetTypeID`: Identifier. Same as in Request.

Elements:

* `NewAccount`: Signed [Account](Account.md) document.

### Notes

----

## IssueAssetType

Issues an asset and creates an _issuer account_ on the Notary.

Attributes:

* `assetTypeID`: Identifier. Hash of the AssetContract.

Elements:

* `AssetContract`: Signed document of type [Asset Contract](AssetContract.md).

## IssueAssetTypeResponse

Attributes:

* `assetTypeID`: Identifier. Hash of AssetContract.
* `accountID`: Identifier. Hash of the Account.

Elements:

* `IssuerAccount`: Signed document of type [Account](Account.md).

### Notes

----

## NotarizeTransaction

Requests the notarization of a transaction. TODO more detail.

Attributes:

* `nymboxHash`: Identifier. Hash of Nymbox.
* `accountID`: Identifier. Account ID referenced in transaction (?).

Elements:

* `Transaction`: Signed document of type [Transaction](Transaction.md).

## NotarizeTransactionResponse

Attributes:

* `accountID`: Identifier. Account ID referenced in Transaction (?).

Elements:

* `ResponseTransaction`: Signed document of type [Transaction](Transaction.md).

### Notes

* `Transaction` and `ResponseTransaction` previously was of deprecated type
  `Ledger` that only contained one item.

----

## GetRequest

Requests new request numbers.

No additional attributes or elements.

## GetRequestResponse

Attributes:

* `nymboxHash`: Identifier. Hash of remote Nymbox. TODO purpose?
* `newRequestNum`: Integer. Next request number to be used.

----

## GetNymbox

Requests current [Nymbox](Nymbox.md).

## GetNymboxResponse

Attributes:

Elements:

----

## GetBoxReceipt

Attributes:

Elements:

## GetBoxReceiptResponse

Attributes:

Elements:

----

## GetAccountFiles

Attributes:

Elements:

## GetAccountFilesResponse

Attributes:

Elements:

----

## ProcessInbox

Attributes:

Elements:

## ProcessInboxResponse

Attributes:

Elements:
