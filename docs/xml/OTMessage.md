An new version of these document types can be found in the
[NotaryMessage.md](../spec/NotaryMessage.md) directory.

# Document Type _OTmessage_

* Attribute `version`: Integer.
* Attribute `dateSigned`: Timestamp.
* Element that is instance of document type _UserCommand_.

# Base Document Type _UserCommand_

## Base Request

This document type is the base type for all requests written by the Client and
read by the Notary.

* Attribute `nymID`: Identifier. The ID of the Nym which makes the request.
* Attribute `serverID`: Identifier. The ID of the Notary which should process
  the request, as defined in the [ServerContract](ServerContract.md).
* Attribute `requestNum`: Integer. The request number.

## Base Response

This is the base type for messages sent by the Notary in response to a
_Base Request_ by the Client.

The element name is the same as for the corresponding request name, but is
prefixed with the at symbol (`@`).

* Attribute `nymID`: Identifier. The Nym which made the request.
* Attribute `serverID`: Identifier. The ID of the responding Notary.
* Attribute `requestNum`: Integer. Same as in the request.
* Attribute `success`: Boolean. Indicates whether request was successful.
* Element `inResponseTo`: ASCII-Armored text. The original request. Always the
  last element inside document body.

## Reoccurring Attributes

Some attribute types that only occur for some subtypes:

* Attribute `nymboxHash`: Identifier. Hash of the Nymbox.
* Attribute `accountID`: Identifier. Specifies the asset account the request is
    for.


# User Commands

## createUserAccount

Requests the registration of a new Nym on the Notary.

* Element `credentialList`: TODO.
* Element `credentials`: TODO.

## @createUserAccount

* Element `nymfile`: Contains a signed document of type Nymfile.

----

## getTransactionNum

Requests a new list of transaction numbers. The list will  be in the Nymbox if
the request was successful.

* Attribute `nymboxHash`: Identifier.

## @getTransactionNum

* Attribute `nymboxHash`: Identifier.

----

## createAccount

Requests creation of a new asset account on the Notary.

* Attribute `assetType`: Identifier. ID of the [AssetContract](AssetContract.md).


## @createAccount

* Attribute `assetType`: Identifier. Same as in Request.
* Element `newAccount`: Signed [Account](Account.md) document.

----

## issueAssetType

Request creation of a new _issuer asset account_ on the Notary.

* Attribute `assetTypeID`: Identifier. Hash of the AssetContract.
* Element `assetContract`: Signed document of type [AssetContract](AssetContract.md).

## @issueAssetType

* Attribute `accountID`: Identifier.
* Attribute `assetType`: Identifier. Hash of AssetContract.
* Element `issuerAccount`: Signed document of type [Account](Account.md).

----

## notarizeTransaction

Requests the notarization of a transaction. TODO more detail.

* Attribute `nymboxHash`: Identifier.
* Attribute `accountID`: Identifier.
* Element `transaction`: Signed document of type `OTledger` that only has one
    item.

## @notarizeTransaction

* Attribute `accountID`: Identifier.
* Element `responseTransaction`: Signed document of type OTledger that only has
    one item.

----

## getRequestNum

Get the current request number from the Notary. When the client doesn't know
what the current one is.

## @getRequestNum

* Attribute `nymboxHash`: Identifier.
* Attribute `newRequestNum`: Integer. Next request number to be used (?).

----

## getNymbox

Requests current [Nymbox](Nymbox.md).

## @getNymbox

* Attribute `nymboxHash`: Identifier.
* Element `nymboxLedger`: Document of type OTledger.

----

## getBoxReceipt

Requests a transaction receipt from a Nymbox, Inbox or Outbox.

* Attribute `transactionNum`: Integer. Transaction number for which to get the
  receipt.
* Attribute `boxType`: String. Specifies which box the transaction is in. Either
  `nymbox`, `inbox` or `outbox`.
* Attribute `accountID`: Identifier. When `boxType` is `inbox` or `outbox`, this
  specifies the account. When `boxType` is `nymbox`, must be equal to `nymID`.

## @getBoxReceipt

* Attribute `transactionNum`: Integer. Same as in request?
* Attribute `boxType`: String. Same as in request?
* Attribute `accountID`: Identifier. Same as in request?
* Element `boxReceipt`: TODO figure out what happens
    [here](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/server/UserCommandProcessor.cpp#L4122-L4290)

----

## getAccountFiles

* Attribute `accountID`: Identifier.

## @getAccountFiles

* Attribute `accountID`: Identifier.
* Attribute `inboxHash`: Identifier. Hash of the contained Inbox.
* Attribute `outboxHash`: Identifier. Hash of the contained Outbox.
* Element `acctFiles`: The account files.
  * TODO document sub-elements

----

## processInbox

* Attribute `nymboxHash`: Identifier.
* Attribute `accountID`: Identifier.
* Element `processLedger`. Document of type OTledger.

## @processInbox

* Attribute `accountID`: Identifier.
* Element `responseLedger`. Document of type OTledger.


