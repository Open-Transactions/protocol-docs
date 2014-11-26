---
title: Document Type cheque
menu:
  main:
    parent: doctypes
---

## Document Type `<cheque>`

Cheque or voucher. Sent out-of-band to a recipient who can use it in a
transaction in order to withdraw funds from the sender's account.

### Elements and attributes

* Attribute `version`. String.
* Attribute `instrumentDefinitionID`. Identifier.
* Attribute `transactionNum`. Integer. Transaction number that will be closed
    for the sender.
* Attribute `notaryID`. Identifier. Notary where the cheque is valid.
* Attribute `senderUserID`. Identifier. Nym to draw from.
* Attribute `senderAcctID`. Identifier. Account to draw from.
* Attribute `hasRecipient`. Boolean. `true` for cheque.
* Attribute `recipientUserID`. Identifier. Set for cheque.
* Attribute `hasRemitter`. Boolean. `true` for voucher.
* Attribute `remitterUserID`. Identifier. Set for voucher.
* Attribute `remitterAcctID`. Identifier. Set for voucher.
* Attribute `validFrom`. Timestamp.
* Attribute `validTo`. Timestamp.
* Optional element `memo`. Armored text.

### TODO

* Split into `<cheque>` and `<voucher>`
* Remove `has*` attributes (redundant)
* Rename `senderUserID`to `senderNymID` or `senderID`
* Rename `senderAcctID` to `senderAccountID`
