---
title: Document Type OTmessage
---

A new version of these document types can be found in
[Notary Messages](../../spec/NotaryMessages.md).

# Document Type `<OTmessage>`

## Elements and attributes

* Attribute `version`: String. Hard-coded to be `2.0`.
* Attribute `dateSigned`: Timestamp.
* Element that is instance of document type _UserCommand_. See below.
* Optional element `ackReplies`. Armored list of seen server replies.

# Base Document Type _UserCommand_

## Base Request

This document type is the base type for all requests written by the Client and
read by the Notary.

### Elements and attributes

* Attribute `nymID`: Identifier. The ID of the Nym which makes the request.
* Attribute `serverID`: Identifier. The ID of the Notary which should process
  the request, as defined in the ServerContract.
* Attribute `requestNum`: Integer. The request number.

## Base Response

This is the base type for messages sent by the Notary in response to a
_Base Request_ by the Client.

The element name is the same as for the corresponding request name, but is
suffixed with "Response".

### Elements and attributes

* Attribute `nymID`: Identifier. The Nym which made the request.
* Attribute `serverID`: Identifier. The ID of the responding Notary.
* Attribute `requestNum`: Integer. Same as in the request.
* Attribute `success`: Boolean. Indicates whether request was successful.
* Element `inReferenceTo`: Armored text. The original request.


# User Commands

## createUserAccount

Requests the registration of a new **nym** on the Notary. It uploads the **nym** file and sends the public credentials. It does not send the private credentials.
The public credentials will include the master credentials and the sub credentials. For example, it could be a master credential and four sub credentials, or two master credentials and two separate credential.

* Element `credentialList`: Contains armored [`<OTuser>` document](OTuser.md).
* Element `credentials`: Armored key-value pairs of credentials. TODO.

## createUserAccountResponse

* Element `nymfile`: Contains [`<OTuser>` document](OTuser.md).

----

## getTransactionNum

Requests a new list of transaction numbers. The list will be in the **nymbox** if
the request was successful. Transaction numbers need to be requested from each Notary that a client transacts with. Additionally, each **nym** on a client needs it's own transaction numbers. When the requested Transaction Numbers arrive in the corresponding **nymbox**, the client performs a ProcessNYMBox to accept and sign the transaction numbers. Each transaction sent to the Notary needs to include a transaction number provided by that same Notary. Some complex financial instruments may require multiple transaction numbers.

* Attribute `nymboxHash`: Identifier. TODO.

## getTransactionNumResponse

* Attribute `nymboxHash`: Identifier. TODO.
* this response does not have element `inReferenceTo`

----

## createAccount

Requests creation of a new asset account on the Notary. It includes the **nym** ID, notary ID and asset type. The reply to this message from the Notary includes a True or False success variable, the account ID of the new account, and the account itself, which is a signed balance file. The account file contains the **nymID** of the account owner, the notaryID where the account is located, the AssetType ID of the account, the balance, and the account type. The new account starts with a zero balance.


* Attribute `assetType`: Identifier. ID of the asset type for the account.

## createAccountResponse

* Attribute `accountID`: Identifier.
* Element `newAccount`: Signed [`<assetAccount>` document](assetAccount.md).

----

## issueAssetType

Request creation of a new _issuer asset account_ on the Notary. issueAssetType is a message sent by an issuer that wants to issue currency onto the notary. An asset contract must be uploaded to the notary by the currency issuer that wants to issue a currency on that notary. The issuer is the one who made the asset contract and signed it.  This message includes the **nymID**, the notary ID, the queried asset types, and the asset contract. The asset type is the hash of the asset contract. The notary verifies that the asset contract hash equals the asset type ID. The notary will also load up the public credentials for the **nym**, to verify the signature on the asset contract.

* Attribute `assetType`: Identifier. Hash of the `<digitalAssetContract>`.
* Element `assetContract`: Signed [`<digitalAssetContract>`
    document](digitalAssetContract.md)

## issueAssetTypeResponse

* Attribute `accountID`: Identifier.
* Attribute `assetType`: Identifier. Hash of the `<digitalAssetContract>`.
* Element `issuerAccount`: Signed [`<assetAccount>` document](assetAccount.md).

----

## notarizeTransaction

Requests the notarization of a transaction. This is the primary method of processing transactions. Only one transaction can be processed at one time by *notarizeTransaction*. Two transaction items are attached; the first item is a balance agreement, which contains the balance and the list of transaction numbers currently signed out to this **nym**, and the second item contains specific information about the of transaction to be performed (for example, transfer or withdrawal).

* Attribute `nymboxHash`: Identifier. Optional. Request will fail if it doesn't
    match the server version.
* Attribute `accountID`: Identifier.
* Element `accountLedger`: Signed document of type `<accountLedger>` that only has
  one `<transaction>`.

## notarizeTransactionResponse

* Attribute `accountID`: Identifier.
* Element `responseLedger`: Signed document of type `<accountLedger>` that
  only has one `<transaction>`.

----

## getRequest

Get the current request number from the Notary, when the client doesn't know
what the current one is.  Request numbers ensure synchronization of messages, between a client and a Notary, and also reduce the risk of replay attacks.
For example, when a client sends a message with request #100, the Notary will expect the next message from this client to use request#101. However, if the client sends a series of messages that do not arrive at the Notary, the next message received by the Notary from this client might be request#105. This is not the request number expected by the Notary and it will be rejected. To resync messages with the Notary, the client makes a getRequest to the Notary to check which request# the Notary is expecting. The client can then retry with the request# expected by the Notary.


## getRequestResponse

* Attribute `nymboxHash`: Identifier. TODO.
* Attribute `newRequestNum`: Integer. Next request number to be used (?).
* this response does not have element `inReferenceTo`

----

## getNymbox

Downloads current **nymbox** from the Notary. A **nymbox** is where any messages, instruments, notices, and new transaction numbers are sent to a **nym** from the Notary. The **nymbox** is signed and it has a hash.


## getNymboxResponse

* Attribute `nymboxHash`: Identifier. TODO.
* Element `nymboxLedger`: [`<accountLedger>` document](accountLedger.md) of type `nymbox`.
  * present only if `success` is `true`
* Element `inReferenceTo`: only if `success` is `false`.

----

## getBoxReceipt

Requests a transaction receipt from a **nymbox**, **inbox** or **outbox**. This message includes the **nymID**, Notary ID, transaction number, and request number. Additionally, the message must specify the box type: **nymbox**, **inbox** or **outbox**. 
*getBoxReceipt* downloads full receipts because the **nymbox**, **inbox**, or **outbox** will only contain the stub and not the full receipt. When the client has downloaded the full receipt, it will hash the receipt and compare it to the hash that is stored in the stub to verify that it corresponds to the same receipt. 

* Attribute `transactionNum`: Integer. Transaction number for which to get the
  receipt.
* Attribute `boxType`: String. Specifies which box the transaction is in. Either
  `nymbox`, `inbox` or `outbox`.
* Attribute `accountID`: Identifier. When `boxType` is `inbox` or `outbox`, this
  specifies the account. When `boxType` is `nymbox`, must be equal to `nymID`.

## getBoxReceiptResponse

* Attribute `transactionNum`: Integer. Same as in request?
* Attribute `boxType`: String. Same as in request?
* Attribute `accountID`: Identifier. Same as in request?
* Element `boxReceipt`: TODO figure out what happens
    [here](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/server/UserCommandProcessor.cpp#L4122-L4290)

----

## getAccountData

Downloads the inbox, the outbox, and the account balance file.

* Attribute `accountID`: Identifier.

## getAccountDataResponse

* Attribute `accountID`: Identifier.
* Attribute `inboxHash`: Identifier. Hash of the contained Inbox.
* Attribute `outboxHash`: Identifier. Hash of the contained Outbox.
* Element `acctFiles`: The account files as armored key-value pairs. Keys are `account`, `inbox`, `outbox`.
  * present only if `success` is `true`
  * TODO document sub-elements
* Element `inReferenceTo`: only if `success` is `false`.

----

## processInbox

*processInbox* is the only transaction that has its own message.  It includes a balance agreement and it has a process inbox item, which is the only type of item that it can have. The process inbox item contains information about the receipts in the inbox that the client is accepting. The client can selectively accept the receipts in the inbox. It does not have to accept all of the receipts in the inbox when performing this transaction. Only the receipts specified in this transaction will be accepted. Processing an inbox item means accepting it and signing it.

* Attribute `nymboxHash`: Identifier. TODO.
* Attribute `accountID`: Identifier.
* Element `processLedger`. Armored [`<accountLedger>`
    document](accountLedger.md).

## processInboxResponse

* Attribute `accountID`: Identifier.
* Element `responseLedger`. Armored [`<accountLedger>`
    document](accountLedger.md).


----

## queryAssetTypes

*queryAssetTypes* sends the notary a list of asset type IDs in a string map (for example, gold or silver), to query the Notary if it supports these asset types. It is a simple message for determining if certain asset types are there are not. The Notary replies with true or false, in response to each queried asset type.

* Attribute: TODO.

## queryAssetTypesResponse

* Attribute: TODO.

----

## sendUserInstrument

*sendUserInstrument* is used to send a payment instrument to another client.
The message is encrypted with the recipient's public key. If the sender does not have the recipient's public key, it will call the *checkNym* message, download the user's public key, encrypt the message to that public key, and then call *sendUserInstrument* to send it to the user. The message goes into the recipient’s **nymbox**.

* Attribute: TODO.

## sendUserInstrumentResponse

* Attribute: TODO.

----

## sendUserMessage

*sendUserMessage* is used to send a message to another client. The message is encrypted with the recipient's public key. If the sender does not have the recipient's public key, it will call the *checkNym* message, download the user's public key, encrypt the message to that public key, and then call *sendUserMessage* to send it to the user. The message goes into the recipient’s **nymbox**.


* Attribute: TODO.

## sendUserMessageResponse

* Attribute: TODO.

----

## usageCredits

*usageCredits* can be used by a client to ask the Notary how many usage credits are available for the specified **nym**. Usage Credits is a number that can be attached to a **nym**. The Notary can turn Usage Credits on or off. If the Notary turns the feature on, then the usage credits of the **nym** will decrement with every protocol message and with every request reply.  All messages (except *usageCredits*) will fail when a **nym** has consumed all of its' usage credits.

* Attribute: TODO.

## usageCreditsResponse

* Attribute: TODO.

----

## checkNym

*checkNym* downloads the public information for another client. For example, this message would be used to download another client’s public key so that we can send him a message and encrypt it to his key. If this message succeeds, it provides the public key for the specified user. If it fails, it returns your original request.


* Attribute: TODO.

## checkNymResponse

* Attribute: TODO.

----

## deleteUserAccount

*deleteUserAccount* deletes the specified **nym** from the Notary. However, this action will fail if the **nym** has any accounts on the notary that do not have a zero balance. If the client wants to delete an asset account, it needs to first reduce the account balance to zero, delete the account, and then delete the **nym** off the Notary.  Alternatively, the user could stop using a **nym** without deleting it and just leave it on the Notary. However, as a courtesy, the unused **nym** should be deleted from the Notary.


* Attribute: TODO.

## deleteUserAccountResponse

* Attribute: TODO.

----

## checkServerID

*checkServerID* is similar to a ping command. It sends a message to the Notary to see if it can get a reply. It verifies if the Notary is listening and responding. This is the first message used in a transaction.

*checkServerID* sends the **nym** authentication key and the encryption key to the Notary. This is one of the few messages that must include these keys in the message. In most other messages the Notary already has these keys, because the **nym** is already registered with the Notary.  The Notary replies to *checkServerID* with true or false. The Notary replies false to this message if an incorrect *notaryID* is used in this message.

* Attribute: TODO.

## checkServerIDResponse

* Attribute: TODO.

----

## triggerClause

*triggerClause* is a transaction used for triggering scripts on smart contracts. Smart contracts have clauses, which are scripts on the contract that can be activated in response to a condition or scheduled to run at certain times. 
For example, a smart contract may have a clause that can be triggered in case of a dispute. Or there may be clauses that are triggered everyday to perform a daily action.

* Attribute: TODO.

## triggerClauseResponse

* Attribute: TODO.

----

## getMint

*getMint* requests the Notary to provide the public mint key for the cash instrument. The cash instrument has a series of minting keys and the minting keys are stored in the mint.There is a public mint and a private mint, the notary keeps the private mint key and it will give a copy of the public mint key to the requesting client. The notary should give all clients the same public mint key. If different public mint keys were provided to different clients, the cash would become traceable.

* Attribute: TODO.

## getMintResponse

* Attribute: TODO.

----

## getContract

Downloads an asset contract from the Notary. The response message from the Notary, if the success is *true*, will include the associated asset contract for the  specified asset type ID. The client can hash the contract provided by the Notary and compare the hash with the asset type ID. 

* Attribute: TODO.

## getContractResponse

* Attribute: TODO.

----

## deleteAssetAccount

Deletes the specified asset account from the Notary. A number of prerequisite actions must be performed before the *deleteAssetAccount* request can be  sent to the Notary.
Firstly, the account balance needs to be reduced to zero. The Notary won’t allow the deletion of an asset account that contains a balance. Secondly, the inbox needs to be empty. Any transaction receipts in the inbox must be closed out.
Once the inbox is empty and the account balance is zero, the *deleteAssetAccount* message can be sent to the Notary and the specified asset account is deleted.


* Attribute: TODO.

## deleteAssetAccountResponse

* Attribute: TODO.

----

# References

* [OTMessage::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/core/OTMessage.cpp#L298).
  Ctrl-F for `$UserCommand` or `At$UserCommand` in order to see the see the
  serialization for the different subtypes.
* [OT_API](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/client/OpenTransactions.cpp).
  This is where the `OTMessage` is initialized and the sub-documents are built.
* [OTClient](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/client/OTClient.cpp).
  This is where server replies are processed.
