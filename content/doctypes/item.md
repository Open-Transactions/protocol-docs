# Document Type `<item>`

Contained in unabbreviated [`<transaction>` documents](transaction.md).

Quote from comment in `OTItem.hpp`:

> Item as in "Transaction Item"

> An OTLedger contains a list of transactions (pending transactions, inbox or
outbox.) Each transaction has a list of items that make up that transaction.
I think that the Item ID shall be the order in which the items are meant to be
processed.  Items are like tracks on a CD. It is assumed there will be several
of them, they come in packs. You normally would deal with the transaction as a
single entity, not the item. A transaction contains a list of items.

## Elements and attributes

* Attribute `type`. String. See section _Item Types_
* Attribute `status`. String. Possible values:
    * `request`: the item is requested from client
    * `acknowledgement`: This item is an acknowledgment from the server.
      The server has signed it. Only in response.
    * `rejection`: This item represents a rejection of the request by the
      server. Server will not sign it. Only in response.
    * `error-unknown`: Error during transaction processing. TODO seems to be never used.
* Attribute `outboxNewTransNum`. Integer. Only when
    `newOutboxTransactioNum == 0`. TODO not sure if used.
    Used only in `atBalanceStatement` with status `acknowledgement`. Server
    informs client that server issued new transaction number and put new
    transaction with this number into outbox.
* Attribute `numberOfOrigin`. Integer. Reference to original transaction.
* Attribute `transactionNum`. Integer. Number of current transaction.
* Attribute `notaryID`. Identifier.
* Attribute `nymID`. Identifier. NymID of the user who created this item. On
  items from client it is the client user ID, on items from server it is 
  the server user id.
* Attribute `fromAccountID`. Identifier. Source account ID
* Attribute `toAccountID`. Identifier. Destination account ID.
* Attribute `inReferenceTo`. Integer. Reference to transaction which does not
  need to be included in items.
* Optional element `note`. Armored. A text field for the user. The note can be
  from server or from client. The note is padded to minimal size 100. TODO: why?
* Optional element `inReferenceTo`. Armored. Copy of transaction item that
  current item refers to.
* Optional element `attachment`. Armored. Digital cash token is sent here.
  For example voucher is here when withdraw voucher.
* If type is `balanceStatement`: Element `transactionReport`
    * balance statement contains transactionReport for each transaction in
      inbox or outbox.
    * Attribute `type`. String. Specifies receipt type. TODO.
    * Attribute `adjustment`. Integer. Amount, indicating an incoming deposit 
      into the account (negative for an outgoing withdrawal).
    * Attribute `accountID`. Identifier. Destination account ID.
    * Attribute `nymID`. Identifier. User ID.
    * Attribute `numberOfOrigin`. Integer. Reference to the original transaction.
    * Attribute `transactionNum`. Integer.
        * the attribute could have special value 1 that is used when a client
          does not know the transaction number. The transaction number will be
          in response in attribute `outboxNewTransNum`.
        * see https://github.com/Open-Transactions/opentxs/blob/develop/src/client/OpenTransactions.cpp#L12418 and https://github.com/Open-Transactions/opentxs/blob/develop/src/core/OTTransaction.cpp#L1987
    * Attribute `closingTransactionNum`. Integer. Only for `finalReceipt` or `
      basketReceipt`.
    * Attribute `inReferenceTo`. Integer.

## Item Types

Transcribed from `OTItem.hpp`, non-exhaustive.

Every type has a corresponding `at` type, which indicates the server response.

### Transfer

* `transfer`
    * used in `notarizeTransaction` by a client to initiate a transfer 
      from one account to another account.
    * `note` contains a user defined description
    * only one `transfer` can be included in a transaction
    * amount must be positive integer
    * a transaction that includes `transfer` must also include `balanceStatement`
    * notarizing `transfer` generates new pending transactions in the source
      account outbox and in the destination account inbox

#### Example

```xml
<item type="transfer"
status="request"
numberOfOrigin="0"
transactionNum="608"
notaryID="otx127PFVQPLRrYrCENt4yqeHXcYxZtZ4669V"
nymID="otx16n7N8Mxd8CiY4M6zhrbeatiMbbTBUQctN"
fromAccountID="otx1DuqE35bzbuDv2kwMPmWt7EdxVMZtds1Ew"
toAccountID="otx14N8M2es7RRbraFtFRib4njx4zJGvd5gKV"
inReferenceTo="0"
amount="100" >

<note>
eNrjyi3PLMlIKUosV/B09/MPclXQ1VXw8w9xVQhwdHHx9HMH8XHIcJGuAwB9Yxw2
</note>

</item>
```

* `atTransfer`

#### Example

```xml
<item type="atTransfer"
status="acknowledgement"
numberOfOrigin="608"
transactionNum="608"
notaryID="otx127PFVQPLRrYrCENt4yqeHXcYxZtZ4669V"
nymID="otx1iDN28aAkxYsFQreU1hBC4X4Ss6btPKGR"
fromAccountID="otx1DuqE35bzbuDv2kwMPmWt7EdxVMZtds1Ew"
toAccountID=""
inReferenceTo="608"
amount="0" >

<inReferenceTo>
...
</inReferenceTo>

</item>
```

### Nymbox Resolution

* `acceptTransaction`
    * client-side acceptance of a fresh (unused) transaction number in the Nymbox
    * attribute `totalListOfNumbers`: comma-separated list of fresh (unused) 
      transaction numbers.

#### Example

```xml
<item type="acceptTransaction"
status="request"
numberOfOrigin="0"
transactionNum="0"
totalListOfNumbers="509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608"
notaryID="otx127PFVQPLRrYrCENt4yqeHXcYxZtZ4669V"
nymID="otx16n7N8Mxd8CiY4M6zhrbeatiMbbTBUQctN"
fromAccountID="otx16n7N8Mxd8CiY4M6zhrbeatiMbbTBUQctN"
toAccountID=""
inReferenceTo="509"
amount="0" >

</item>
```
* `acceptMessage`
   * client-side acceptance of a transaction message in the Nymbox
* `acceptNotice`
   * client-side acceptance of a server notification in the Nymbox

### Inbox Resolution

* `acceptPending`
    * client-side acceptance of a pending transfer
* `rejectPending`
    * client-side rejection of a pending transfer
    * TODO never used in current implementation

### Info

* `balanceStatement`
    * is created by client to change its own account balance with respect to
      other items in the transaction. The balance statement is then validated
      and signed by server.
    * element `attachment`
        * contains armored [`nymData` document](../doctypes/nymData.md) that
          updates issued nums and transaction nums.
    * element `transactionReport`
        * balance statement contains `transactionReport` for each transaction
          in inbox or outbox.

#### Example

```xml
<item type="balanceStatement"
status="request"
numberOfOrigin="0"
transactionNum="710"
notaryID="otx1C2jLg8GpXB7An3pWv1eQdbkV4sCiDGQzt"
nymID="otx1FQ6w3YBsijh24BYRxCAPFqvwj1oiwMpMJ"
fromAccountID="otx1K4mr87GwCXt83xsneQhPYE6ztwSyNSi7b"
toAccountID=""
inReferenceTo="0"
amount="999998" >
<attachment>
</attachment>

<transactionReport type="transferReceipt"
adjustment="-1"
accountID="otx1K4mr87GwCXt83xsneQhPYE6ztwSyNSi7b"
nymID="otx1FQ6w3YBsijh24BYRxCAPFqvwj1oiwMpMJ"
notaryID="otx1C2jLg8GpXB7An3pWv1eQdbkV4sCiDGQzt"
numberOfOrigin="711"
transactionNum="827"
closingTransactionNum="0"
inReferenceTo="823" />

<transactionReport type="transfer"
adjustment="-1"
accountID="otx1K4mr87GwCXt83xsneQhPYE6ztwSyNSi7b"
nymID="otx1FQ6w3YBsijh24BYRxCAPFqvwj1oiwMpMJ"
notaryID="otx1C2jLg8GpXB7An3pWv1eQdbkV4sCiDGQzt"
numberOfOrigin="710"
transactionNum="1"
closingTransactionNum="0"
inReferenceTo="710" />

</item>
```
* `transactionStatement`
    * client has to sign a new `transactionStatement` when transaction numbers
      are changed to update information on which transaction numbers are
      owned by the client.
      This is also changed when a client receives new transaction numbers from 
      server or when a transaction is cleared.
    * element `attachment` contains document [`nymData`](nymData.md) with
      the current list of transaction numbers.

### Cash Withdrawal and Deposit

* used in `notarizeTransaction`
* attachment contains a purse document

* `withdrawal`
    * client digital cash withdrawal
* `deposit`
    * client cash deposit (of a purse containing blinded tokens)

### Cheques and Vouchers

* `withdrawVoucher`
    * client request to purchase a voucher (a cashier's cheque)
    * element `attachment` contains voucher (document cheque)
    * used in transaction `withdrawal`
* `depositCheque`
    * client request to deposit a cheque
    * used in transaction `deposit`
    * element `attachment` contains voucher (document cheque)

## Receipt Types

* `replyNotice`
    * for some special messages, the server also drops the reply into Nymbox to
      guarantee that the Nym will receive and process the message. This is used
      in a `replyNotice` transaction
         * used as reply for messages: `issueAssetType`, `createAccount`,
           `deleteUser`, `deleteAssetAcct`, `processNymbox`, `processInbox`,
           `notarizeTransaction`
    * element `attachment` contains the reply message
    * TODO I do not see replyNotice in communication
* `successNotice`
    * seems that this is never used, see `successNotice` in [transaction](transaction.md)

### Payment receipts

* used only in `balanceStatement`, not in `item` but as `transactionReport`,
  for usage in inbox see [transaction](transaction.md)
* TODO this should be moved, is not part `item`, it is only part of OTItem.hpp

* `voucherReceipt`
* `marketReceipt`
    * server result of market trading
    * element `attachment` contains document `marketOffer`
* `paymentReceipt`
    * server result of smart contract
    * element `attachment` contains document `smartContract`
    * used also in transaction `paymentReceipt`
* `transferReceipt`
    * server receipt dropped into an inbox as result of transfer being accepted

## References

* [enum itemType](https://github.com/Open-Transactions/opentxs/blob/f403ce1496ddddea9a0887c87d4f76535697c9a3/include/opentxs/core/OTItem.hpp#L169)
* [OTItem::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/f403ce1496ddddea9a0887c87d4f76535697c9a3/src/core/OTItem.cpp#L2254)

## Notes

### Code Smell: Switch Statement

[Source](http://sourcemaking.com/refactoring/switch-statements). In this case on
the `type` attribute.

The number of possible types of this class the product of
 * Number of possible transaction types
 * Number of possible items inside the transaction type
 * Number of message types (two, request and response)

Should be solved by a better type hierarchy.
