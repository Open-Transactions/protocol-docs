---
title: Document Type accountLedger
---

# Document Type `<accountLedger>`

Typed container of [`<transaction>` documents](transaction.md).

For all`type`s but `message`, the transactions are included in abbreviated form (`nymboxRecord`, `inboxRecord`, ..., see below). Those include the hash of the transaction in the record's `receiptHash` attribute. The transactions themselves are stored separately, see [`<transaction>`](transaction.md).

If the `type` is `message`, the transactions are included in full as a list of `transaction` elements.

## Elements and attributes

* Attribute `version`: Integer.
* Attribute `type`: String. Specifies the ledger type. See _Ledger Types_ below.
* Attribute `numPartialRecords`: Integer. In abbreviated form, it is the number of abbreviated transaction elements (see below). In full form, it is constant _0_.
* Attribute `accountID`: Identifier.
* Attribute `userID`: Identifier. The Nym the ledger belongs to.
* Attribute `serverID`: Identifier. The Notary the ledger is stored with.

For ledgers in full form (only for ledgers of `type` `message`), for each transaction:

* Element `transaction`: [`<transaction>` document](transaction.md).
  
<a href="tx-abbreviated-elements"></a>For ledgers in abbreviated form, the transactions are stored in abbreviated form:

* For ledger type `nymbox`: Element `nymboxRecord`: [`nymboxRecord` document](transaction.md#document-type-nymboxrecord)
* For ledger type `inbox`: Element `inboxRecord`: [`inboxRecord` document](transaction.md#document-type-inboxrecord)
* For ledger type `outbox`: Element `outboxRecord`: [`outboxRecord` document](transaction.md#document-type-outboxrecord)
* For ledger type `paymentInbox`: Element `paymentInboxRecord`: [`paymentInboxRecord` document](transaction.md#document-type-paymentinboxrecord)
* For ledger type `recordBox`: Element `recordBoxRecord`: [`recordBoxRecord` document](transaction.md#document-type-recordboxrecord)
* For ledger type `expiredBox`: Element `expiredBoxRecord`: [`expiredBoxRecord` document](transaction.md#document-type-expiredboxrecord)

## Ledger Types

* `nymbox`. Nym-scoped.
* `inbox`. Account-scoped. Contains pending incoming transfers.
* `outbox`. Account-scoped. Contains pending outgoing transfers.
* `message`. Apparently used in OTMessage. Not abbreviated. TODO where and how.
* `paymentInbox`. Client-side only.
* `recordBox`. Client-side only.
* `expiredBox`. Client-side only.


# References

* [enum ledgerType](https://github.com/Open-Transactions/opentxs/blob/682fd05f/include/opentxs/core/OTLedger.hpp#L181)
* [OTLedger::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/core/OTLedger.cpp#L1779)

# Notes

Possible improvements

* Remove attribute `numPartialRecords`. It is redundant information, as it is either:
    * The number of transactions, if the ledger is in abbreviated form
    * 0 otherwise
   I.e., `numPartialRecords` is the number of [abbreviated transaction elements](#tx-abbreviated-elements).
   That element was probably added to make parsing simpler. The format should be easy to parse in the first place. 
