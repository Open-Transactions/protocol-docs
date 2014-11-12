---
title: Document Type item
---

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


# Structure

* Attribute `type`. String. See section _Item Types_
* Attribute `status`. String. Possible values:
    * `request`: the item is requested from client
    * `acknowledgement`: This item is an acknowledgment from the server. The server has signed it. Only in response.
    * `rejection`: This item represents a rejection of the request by the server. Server will not sign it. Only in response.
    * `error-unknown`: Error during processing transaction. TODO seems that never used.
* Attribute `outboxNewTransNum`. Integer. Only when
    `newOutboxTransactioNum == 0`. TODO not sure if used.
    Used only in `atBalanceStatement` with status `acknowledgement`. Server informs client that server issued new transaction number and put new transaction with this number into outbox.
* Attribute `numberOfOrigin`. Integer. Reference to original transaction.
* Attribute `transactionNum`. Integer. Number of current transaction.
* Attribute `serverID`. Identifier.
* Attribute `userID`. Identifier. NymID of the user who created this item. 
* Attribute `fromAccountID`. Identifier. Source account ID
* Attribute `toAccountID`. Identifier. Destination account ID.
* Attribute `inReferenceTo`. Integer. Reference to transaction which does not need to be included in items.
* Optional element `note`. Armored. A text field for the user. The note can be from server or from client. The note is padded to minimal size 100. TODO: why?
* Optional element `inReferenceTo`. Armored. Copy of transaction item that current item refers.
* Optional element `attachment`. Armored. Digital cash token is sent here. For example voucher is here when withdraw voucher.
* If type is `balanceStatement`: Element `transactionReport`
    * balance statement contains transactionReport for each transaction in inbox or outbox.
    * Attribute `type`. String. Specifies receipt type. TODO.
    * Attribute `adjustment`. Integer. Amount, how much incoming into the account (negative for outgoing).
    * Attribute `accountID`. Identifier. Destination account ID.
    * Attribute `userID`. Identifier. User ID.
    * Attribute `numberOfOrigin`. Integer. Reference to original transaction.
    * Attribute `transactionNum`. Integer.
          * the attribute could have special value 1 that is used when client does not know transaction number. The transaction number will be in response in `outboxNewTransNum`.
          * see https://github.com/Open-Transactions/opentxs/blob/develop/src/client/OpenTransactions.cpp#L12418 and https://github.com/Open-Transactions/opentxs/blob/develop/src/core/OTTransaction.cpp#L1987
    * Attribute `closingTransactionNum`. Integer. Only for `finalReceipt` or `basketReceipt`.
    * Attribute `inReferenceTo`. Integer.

## Item Types

Transcribed from `OTItem.hpp`, non-exhaustive.

Every type has a corresponding `at` type, which indicates the server response.

#### Transfer

* `transfer`
    * used in `notarizeTransactions` by client to initiate transfer from one account to another account.
    * `note` contains user defined description
    * only one `transfer` can be in transaction
    * amount must be positive integer
    * must be in transaction together with `balanceStatement`
    * notarizing `transfer` generates new pending transactions in source account outbox, destination account inbox

##### Example

```xml
<item type="transfer"
status="request"
numberOfOrigin="0"
transactionNum="608"
serverID="otx127PFVQPLRrYrCENt4yqeHXcYxZtZ4669V"
userID="otx16n7N8Mxd8CiY4M6zhrbeatiMbbTBUQctN"
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

##### Example

```xml
<item type="atTransfer"
status="acknowledgement"
numberOfOrigin="608"
transactionNum="608"
serverID="otx127PFVQPLRrYrCENt4yqeHXcYxZtZ4669V"
userID="otx1iDN28aAkxYsFQreU1hBC4X4Ss6btPKGR"
fromAccountID="otx1DuqE35bzbuDv2kwMPmWt7EdxVMZtds1Ew"
toAccountID=""
inReferenceTo="608"
amount="0" >

<inReferenceTo>
...
</inReferenceTo>

</item>
```

#### Nymbox Resolution

* `acceptTransaction`
    * client-side acceptance of a transaction number (a blank) in the Nymbox
    * attribute `totalListOfNumbers`: comma-separated list of numbers of blank transactions.

##### Example

```xml
<item type="acceptTransaction"
status="request"
numberOfOrigin="0"
transactionNum="0"
totalListOfNumbers="509,510,511,512,513,514,515,516,517,518,519,520,521,522,523,524,525,526,527,528,529,530,531,532,533,534,535,536,537,538,539,540,541,542,543,544,545,546,547,548,549,550,551,552,553,554,555,556,557,558,559,560,561,562,563,564,565,566,567,568,569,570,571,572,573,574,575,576,577,578,579,580,581,582,583,584,585,586,587,588,589,590,591,592,593,594,595,596,597,598,599,600,601,602,603,604,605,606,607,608"
serverID="otx127PFVQPLRrYrCENt4yqeHXcYxZtZ4669V"
userID="otx16n7N8Mxd8CiY4M6zhrbeatiMbbTBUQctN"
fromAccountID="otx16n7N8Mxd8CiY4M6zhrbeatiMbbTBUQctN"
toAccountID=""
inReferenceTo="509"
amount="0" >

</item>
```
* `acceptMessage`
* `acceptNotice`

#### Inbox Resolution

* `acceptPending`
    * client-side acceptance of a pending transfer
* `rejectPending`
    * client-side rejection of a pending transfer
    * TODO never used in current implementation


#### Info

* `balanceStatement`
    * is created by client to change its own account balance with respect to other items in the transaction. The balance statement is then validated and signed by server.
    * element `attachment`
        * contains armored [`OTuser` document](../doctypes/OTuser.md) that updates issued nums and transaction nums.
    * element `transactionReport`
        * balance statement contains transactionReport for each transaction in inbox or outbox.

##### Example

```xml
<item type="balanceStatement"
status="request"
numberOfOrigin="0"
transactionNum="710"
serverID="otx1C2jLg8GpXB7An3pWv1eQdbkV4sCiDGQzt"
userID="otx1FQ6w3YBsijh24BYRxCAPFqvwj1oiwMpMJ"
fromAccountID="otx1K4mr87GwCXt83xsneQhPYE6ztwSyNSi7b"
toAccountID=""
inReferenceTo="0"
amount="999998" >
<attachment>
</attachment>

<transactionReport type="transferReceipt"
adjustment="-1"
accountID="otx1K4mr87GwCXt83xsneQhPYE6ztwSyNSi7b"
userID="otx1FQ6w3YBsijh24BYRxCAPFqvwj1oiwMpMJ"
serverID="otx1C2jLg8GpXB7An3pWv1eQdbkV4sCiDGQzt"
numberOfOrigin="711"
transactionNum="827"
closingTransactionNum="0"
inReferenceTo="823" />

<transactionReport type="transfer"
adjustment="-1"
accountID="otx1K4mr87GwCXt83xsneQhPYE6ztwSyNSi7b"
userID="otx1FQ6w3YBsijh24BYRxCAPFqvwj1oiwMpMJ"
serverID="otx1C2jLg8GpXB7An3pWv1eQdbkV4sCiDGQzt"
numberOfOrigin="710"
transactionNum="1"
closingTransactionNum="0"
inReferenceTo="710" />

</item>
```
* `transactionStatement`

#### Cash Withdrawal and Deposit

* `withdrawal`
* `deposit`

#### Cheques and Vouchers

* `withdrawVoucher`
* `depositCheque`

#### Receipt Types

* `voucherReceipt`
* `marketReceipt`
* `paymentReceipt`
* `transferReceipt`
* `replyNotice`
* `successNotice`

# References

* [enum itemType](https://github.com/Open-Transactions/opentxs/blob/f403ce1496ddddea9a0887c87d4f76535697c9a3/include/opentxs/core/OTItem.hpp#L169)
* [OTItem::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/f403ce1496ddddea9a0887c87d4f76535697c9a3/src/core/OTItem.cpp#L2254)

# Notes

## TODO: Special Casing

On these item types:

* `acceptTransaction`
* `request`
* `acknowledgement`
* `rejection`

## TODO: other item types

Outside the current description task

#### Receipt Acknowledgement and Dispute

* `acceptCronReceipt`
* `disputeCronReceipt`

* `acceptItemReceipt`
* `disputeItemReceipt`

* `acceptFinalReceipt`
* `disputeFinalReceipt`

* `acceptBasketReceipt`
* `disputeBasketReceipt`

#### Fees

* `serverfee`
* `issuerfee`

#### Cron / Basket Transactions

* `finalReceipt` (cron)
* `basketReceipt`

#### Paying Dividend on Shares of Stock

#### Trading On Markets

#### Payment Plans

#### Smart Contracts

#### Cancelling of Market Offers and Payment Plans

## Code Smell: Switch Statement

[Source](http://sourcemaking.com/refactoring/switch-statements). In this case on
the `type` attribute.

The number of possible types of this class the product of
 * Number of possible transaction types
 * Number of possible items inside the transaction type
 * Number of message types (two, request and response)

Should be solved by a better type hierarchy.
