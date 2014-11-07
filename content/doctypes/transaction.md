# Document Type `<transaction>`


Contained in [`<accountLedger>` documents](accountLedger.md).

Transactions can either be _full_ or _abbreviated_. They are abbreviated when
inside `<accountLedger>`, except `<OTmessage>`.

Full transactions contain a list of [`<item>` documents](item.md).

# Base Document Type

* Attribute `type`: String. See section _Transaction Types_.
* Attribute `dateSigned`: Time.
* Attribute `accountID`: Identifier.
* Attribute `userID`: Identifier.
* Attribute `serverID`: Identifier.
* Attribute `numberOfOrigin`: Integer. TODO
* Attribute `transactionNum`: Integer. TODO

## Transaction Types

Possible values for the `type` attribute. Taken from `OTTransaction.hpp`.

### Nymbox Transaction Types

* `blank`: Freshly issued transaction number
* `message`: Message from another Nym
* `notice`: Server notice
* `replyNotice`: A copy of a server reply to a previous request you sent
* `successNotice`: A transaction number has successfully been signed out

### Inbox and Outbox Transaction Types

* `pending`: Server puts this in your outbox (when sending) and in recipient's
    inbox.

### Inbox receipts

* `transferReceipt`
* `chequeReceipt`
* `voucherReceipt`
* `marketReceipt`
* `paymentReceipt`
* `finalReceipt`: When a cron item is expired or cancelled.
* `basketReceipt`: When a basket exchange is processed.

### Payment Inbox, Payment Outbox, Record Box

* `instrumentNotice`
* `instrumentRejection`

### Messages

* `processNymbox`
* `atProcessNymbox`
* `processInbox`
* `atProcessInbox`
* `transfer`
* `atTransfer`
* `deposit`
* `atDeposit`
*


## Document Type: Full Form

* Optional element `inReferenceTo`. Armored document. TODO
* Optional element `cancelRequest`. Armored document. TODO
* List of armored [`<item>` documents](item.md).


## Document Types: Abbreviated Form

### Base Document Type

These attributes are common for all abbreviated records (?)

* Attribute `type`: String. See Transaction Types
* Attribute `dateSigned`: Time in seconds, when the instrument was last signed.
* Attribute `receiptHash`: Identifier. TODO
* Attribute `transactionNum`: Integer. Transaction number.
* Attribute `inRefDisplay`: Integer. Transaction number, reference to transaction for display purposes.
* Attribute `inReferenceTo`: Integer. Transaction number that is related to this transaction.

If `type` is `finalReceipt` or `basketReceipt`:

* Attribute `closingNum`: Integer. TODO

### Document Type `<paymentInboxRecord>`

* Attribute `displayValue`: TODO

### Document Type `<nymboxRecord>`

* Attribute `totalListOfNumbers`: Comma separated list of transaction numbers successfully signed out.
* TODO funny stuff is happening
    [here](https://github.com/Open-Transactions/opentxs/blob/63fcfb34c406e83d89b903ffe3c217f01614f445/src/core/OTTransaction.cpp#L4971)

#### Example
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
### Document Type `<outboxRecord>`

* Attribute `displayValue`: TODO
* Attribute `adjustment`: Integer. TODO
* Attribute `numberOfOrigin`: Integer. TODO

### Document Type `<expiredBoxRecord>`

Inherits from Document Type `<outboxRecord>`.

### Document Type `<recordBoxRecord>`

* Attribute `displayValue`: TODO
* Attribute `adjustment`: Integer. TODO
* Attribute `numberOfOrigin`: Integer. TODO


# References

* [enum transactionType](https://github.com/Open-Transactions/opentxs/blob/682fd05f/include/opentxs/core/OTTransaction.hpp#L450)
* [OTTransaction::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/682fd05f/src/core/OTTransaction.cpp#L4352)

# Notes

Possible improvements

* Use composition over inheritance.
* Use document types over `type` attribute.
* The `message` type in particular is out of place.
