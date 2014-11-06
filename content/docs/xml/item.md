# Document Type `<item>`

Contained in unabbreviated [`<transaction> documents`](transaction.md).

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
* Attribute `status`. String. TODO
* Attribute `outboxNewTransNum`. Integer. Only when
    `newOutboxTransactioNum == 0`. TODO.
* Attribute `numberOfOrigin`. Integer.
* Attribute `transactionNum`. Integer.
* Attribute `serverID`. Identifier.
* Attribute `userID`. Identifier.
* Attribute `fromAccountID`. Identifier.
* Attribute `toAccountID`. Identifier.
* Attribute `inReferenceTo`. Integer.
* Optional element `note`. ASCII-Armored. TODO
* Optional element `inReferenceTo`. ASCII-Armored. TODO
* Optional element `attachment`. ASCII-Armored. TODO
* If type is `balanceStatment`: Element `transactionReport`
    * Attribute `type`. String. Specifies receipt type. TODO.
    * Attribute `adjustment`. Integer.
    * Attribute `accountID`. Identifier.
    * Attribute `userID`. Identifier.
    * Attribute `numberOfOrigin`. Integer.
    * Attribute `transactionNum`. Integer.
    * Attribute `closingTransactionNum`. Integer.
    * Attribute `inReferenceTo`. Integer.

## Item Types

Transcribed from `OTItem.hpp`, non-exhaustive.

Every type has a corresponding `at` type, which indicates the server response.

#### Transfer

* `transfer`

#### Nymbox Resolution

* `acceptTransaction`
* `acceptMessage`
* `acceptNotice`

#### Inbox Resoultion

* `acceptPending`
* `rejectPending`


#### Info

* `balanceStatement`
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
