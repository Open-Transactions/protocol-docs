---
title: Document Type transaction
menu:
  main:
    parent: doctypes
---

## Document Type `<transaction>`

Contained in [`<accountLedger>` documents](accountLedger.md).

Transactions can either be in _full_ or _abbreviated_ form. They are
abbreviated when inside `<accountLedger>`, except for ledger type "message"
(`<notaryMessage>`).

Full transactions contain a list of [`<item>` documents](item.md).

Abbreviated transactions do not contain the list of
[`<item>` documents](item.md). The items will be loaded one by one during
verification, using the [`getBoxReceipt`](notaryMessage.md#getboxreceipt)
message.

* [TODO, verify if this is true].
* Reason: if the inbox contains hundreds of receipts (items), downloading the
  inbox would take too long or fail altogether, creating resyncing issues.
* The full transactions referenced by abbreviated transactions are stored in
  the `*box/*.r/*.rct` files.
  See [OTTransaction::SaveBoxReceipt()](#ref-saveboxreceipt).

A transaction can be "in reference to" an [`<item>`](item.md).

## Base Document Type

### Elements and attributes

Those attributes are common to the [full form document type](#document-type-
full-form) and the [abbreviated form document types](#document-types-
abbreviated-form).

* Attribute `type`: String. See section _Transaction Types_.
* Attribute `dateSigned`: Time. The date when the instrument was last signed.
* Attribute `accountID`: Identifier.
* Attribute `nymID`: Identifier.
* Attribute `notaryID`: Identifier.
* Attribute `numberOfOrigin`: Integer.
    * Constant _0_ if not applicable.
    * If `type` is one of (`chequeReceipt`, `voucherReceipt`) and the referenced
      [`<item>`](item.md) is of type [`depositCheque`](item.md#cheques-and-vouchers):
      Same as the referenced item's [`numberOfOrigin`](item.md#structure).
    * If `type` is one of (`pending`, `marketReceipt`, `paymentReceipt`,
      `finalReceipt`, `basketReceipt`): same as `inReferenceTo`.
    * If `type` is one of (`blank`, `message`, `notice`, `replyNotice`,
      `successNotice`, `processNymbox`, `atProcessNymbox`, `transferReceipt`,
      `deposit`, `atDeposit`, `instrumentNotice`, `instrumentRejection`):
      Not applicable.
    * For all other `type`s: same as `transactionNum`.
* Attribute `transactionNum`: Integer. The notary issues this in response to a
  [`getTransactionNumbers` message](notaryMessage.md#gettransactionnumbers).
  TODO: link to [tx num spec](https://github.com/monetas/opentxs-protocol/issues/89).
* Attribute `inReferenceTo`: Integer. Transaction number of the item this
  transaction is in reference to.

### Transaction Types

Possible values for the `type` attribute. Taken from `OTTransaction.hpp`.

#### Nymbox Transaction Types

* `blank`: Freshly issued transaction number
* `message`: Message from another Nym
* `notice`: Server notice
* `replyNotice`: A copy of a server reply to a previous request you sent
* `successNotice`: A transaction number has successfully been signed out

#### Inbox and Outbox Transaction Types

* `pending`: Server puts this in your outbox (when sending) and in recipient's
    inbox.

#### Inbox receipts

* `transferReceipt`
* `chequeReceipt`
* `voucherReceipt`
* `marketReceipt`
* `paymentReceipt`
* `finalReceipt`: When a cron item is expired or cancelled.
* `basketReceipt`: When a basket exchange is processed.

#### Payment Inbox, Payment Outbox, Record Box

* `instrumentNotice`
* `instrumentRejection`

#### Messages

* `processNymbox`
* `atProcessNymbox`
* `processInbox`
* `atProcessInbox`
* `transfer`
* `atTransfer`
* `deposit`
* `atDeposit`

### Document Type: Full Form

* Optional element `closingTransactionNumber`. Only if `type` is `finalReceipt` or `basketReceipt`. TODO
* Optional element `inReferenceTo`. Armored contract (TODO: figure out if needed and what can be in there, looks like it can contain different document types depending on the situation).
* Optional element `cancelRequest`. Armored document. TODO
* List of armored [`<item>` documents](item.md).

### Document Types: Abbreviated Form

#### Base Document Type

These attributes are common for all abbreviated records (?)

##### Elements and attributes

* Attribute `type`: String. See Transaction Types
* Attribute `dateSigned`: Time in seconds, when the instrument was last signed.
* Attribute `receiptHash`: Identifier. TODO
* Attribute `transactionNum`: Integer. Transaction number.
* Attribute `inRefDisplay`: Integer. Transaction number, reference to transaction for display purposes.
* Attribute `inReferenceTo`: Integer. Transaction number that is related to this transaction.

If `type` is `finalReceipt` or `basketReceipt`:

* Attribute `closingNum`: Integer. TODO

#### Document Type `<paymentInboxRecord>`

##### Elements and attributes

* Attribute `displayValue`: TODO

#### Document Type `<nymboxRecord>`

##### Elements and attributes

* Attribute `totalListOfNumbers`: Comma separated list of transaction numbers successfully signed out.
* TODO funny stuff is happening
  [here](https://github.com/Open-Transactions/opentxs/blob/63fcfb34c406e83d89b903ffe3c217f01614f445/src/core/OTTransaction.cpp#L4971)

##### Example

```xml
<nymboxRecord type="successNotice"
 dateSigned="1414495363"
 requestNumber="0"
 transSuccess="false"
 receiptHash="2nG5wGlWKbzF5Mo8M6P9YH44S6FazSNQleaaTWRR0av"
 transactionNum="714"
 totalListOfNumbers="614,615,616,617,618,619,620,621,622,623,624,625,626,627,628,629,630,631,632,633,634,635,636,637,638,639,640,641,642,643,644,645,646,647,648,649,650,651,652,653,654,655,656,657,658,659,660,661,662,663,664,665,666,667,668,669,670,671,672,673,674,675,676,677,678,679,680,681,682,683,684,685,686,687,688,689,690,691,692,693,694,695,696,697,698,699,700,701,702,703,704,705,706,707,708,709,710,711,712,713"
 inRefDisplay="614"
 inReferenceTo="614" />
```

#### Document Type `<outboxRecord>`

##### Elements and attributes

* Attribute `displayValue`: TODO
* Attribute `adjustment`: Integer. TODO
* Attribute `numberOfOrigin`: Integer. TODO

#### Document Type `<expiredBoxRecord>`

Inherits from Document Type `<outboxRecord>`.

#### Document Type `<recordBoxRecord>`

##### Elements and attributes

* Attribute `displayValue`: TODO
* Attribute `adjustment`: Integer. TODO
* Attribute `numberOfOrigin`: Integer. TODO

## References

* [enum transactionType](https://github.com/Open-Transactions/opentxs/blob/682fd05f/include/opentxs/core/OTTransaction.hpp#L450)
* [OTTransaction::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/core/OTTransaction.cpp#L4352)
* [numberOfOrigin](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/core/OTTransaction.cpp#L5790)
* <a name="ref-saveboxreceipt"></a>[OTTransaction::SaveBoxReceipt()](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/core/OTTransaction.cpp#L3007)

## Notes

Possible improvements

* Use composition over inheritance.
* Use document types over `type` attribute.
* The `message` type in particular is out of place.
* Clean up this hack:
  https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/server/OTServer.cpp#L786-802
