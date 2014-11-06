A new version of these document types can be found in
[Notary Messages](../../spec/NotaryMessages.md).

# Document Type `<OTmessage>`

* Attribute `version`: String. Hard-coded to be `2.0`.
* Attribute `dateSigned`: Timestamp.
* Element that is instance of document type _UserCommand_.
* Optional element `ackReplies`. Armored list of seen server replies.

# Base Document Type _UserCommand_

## Base Request

This document type is the base type for all requests written by the Client and
read by the Notary.

* Attribute `nymID`: Identifier. The ID of the Nym which makes the request.
* Attribute `serverID`: Identifier. The ID of the Notary which should process
  the request, as defined in the ServerContract.
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
* Element `inResponseTo`: Armored text. The original request.


# User Commands

## createUserAccount

Requests the registration of a new Nym on the Notary.

* Element `credentialList`: TODO.
* Element `credentials`: TODO.

## @createUserAccount

* Element `nymfile`: Contains a signed [`<OTuser>` document](OTuser.md).

----

## getTransactionNum

Requests a new list of transaction numbers. The list will be in the Nymbox if
the request was successful.

* Attribute `nymboxHash`: Identifier. TODO.

## @getTransactionNum

* Attribute `nymboxHash`: Identifier. TODO.

----

## createAccount

Requests creation of a new asset account on the Notary.

* Attribute `assetType`: Identifier. ID of the asset type for the account.

## @createAccount

* Attribute `accountID`: Identifier.
* Element `newAccount`: Signed [`<assetAccount>` document](assetAccount.md).

----

## issueAssetType

Request creation of a new _issuer asset account_ on the Notary.

* Attribute `assetType`: Identifier. Hash of the `<digitalAssetContract>`.
* Element `assetContract`: Signed [`<digitalAssetContract>`
    document](digitalAssetContract.md)

## @issueAssetType

* Attribute `accountID`: Identifier.
* Attribute `assetType`: Identifier. Hash of the `<digitalAssetContract>`.
* Element `issuerAccount`: Signed [`<assetAccount>` document](assetAccount.md).

----

## notarizeTransactions

Requests the notarization of a transaction. TODO more detail.

* Attribute `nymboxHash`: Identifier. Optional. Request will fail if it doesn't
    match the server version.
* Attribute `accountID`: Identifier.
* Element `accountLedger`: Signed document of type `<accountLedger>` that only has
  one `<transaction>`.

## @notarizeTransactions

* Attribute `accountID`: Identifier.
* Element `responseLedger`: Signed document of type `<accountLedger>` that
  only has one `<transaction>`.

----

## getRequest

Get the current request number from the Notary. When the client doesn't know
what the current one is.

## @getRequest

* Attribute `nymboxHash`: Identifier. TODO.
* Attribute `newRequestNum`: Integer. Next request number to be used (?).

----

## getNymbox

Requests current Nymbox.

## @getNymbox

* Attribute `nymboxHash`: Identifier. TODO.
* Element `nymboxLedger`: [`<accountLedger>` document](accountLedger.md) of type `nymbox`.

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

* Attribute `nymboxHash`: Identifier. TODO.
* Attribute `accountID`: Identifier.
* Element `processLedger`. Armored [`<accountLedger>`
    document](accountLedger.md).

## @processInbox

* Attribute `accountID`: Identifier.
* Element `responseLedger`. Armored [`<accountLedger>`
    document](accountLedger.md).


----

# References

* [OTMessage::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/core/OTMessage.cpp#L298).
  Ctrl-F for `$UserCommand` or `At$UserCommand` in order to see the see the
  serialization for the different subtypes.
* [OT_API](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/client/OpenTransactions.cpp).
  This is where the `OTMessage` is initialized and the sub-documents are built.
* [OTClient](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/client/OTClient.cpp).
  This is where server replies are processed.
