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

* Optional element `inReferenceTo`. ASCII-Armored document. TODO
* Optional element `cancelRequest`. ASCII-Armored document. TODO
* List of ASCII-Armored `<item>` elements.
  See [Document Type `<item>`](item.md).


## Document Types: Abbreviated Form

### Base Document Type

These attributes are common for all abbreviated records (?)

* Attribute `type`: String. Seems to be same as element name.
* Attribute `dateSigned`: Time.
* Attribute `receiptHash`: Identifier. TODO
* Attribute `transactionNum`: Integer. TODO
* Attribute `inRefDisplay`: Integer. TODO
* Attribute `inReferenceTo`: Integer. TODO

If `type` is `finalReceipt` or `basketReceipt`:

* Attribute `closingNum`: Integer. TODO

### Document Type `<paymentInboxRecord>`

* Attribute `displayValue`: TODO

### Document Type `<nymboxRecord>`

* TODO funny stuff is happening
    [here](https://github.com/Open-Transactions/opentxs/blob/63fcfb34c406e83d89b903ffe3c217f01614f445/src/core/OTTransaction.cpp#L4971)

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
