# Document Type `<accountLedger>`

Typed container of abbreviated [`<transaction>` documents](transaction.md).

## Structure

* Attribute `version`: Integer.
* Attribute `type`: String. Specifies the ledger type. See _Ledger Types_ below.
* Attribute `numPartialRecords`: Integer.
* Attribute `accountID`: Identifier.
* Attribute `userID`: Identifier. The Nym the ledger belongs to.
* Attribute `serverID`: Identifier. The Notary the ledger is stored with.
* List of ASCII-Armored [`<transaction>` documents](transaction.md). Only
  ledgers of type `message` are not stored in abbreviated form.

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
