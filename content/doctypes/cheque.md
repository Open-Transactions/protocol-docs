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
* Attribute `transactionNum`. Integer. Transaction number that will be 
    used by the sender for this transaction.
* Attribute `notaryID`. Identifier. Notary where the cheque is valid.
* Attribute `senderNymID`. Identifier. Nym to draw from.
* Attribute `senderAcctID`. Identifier. Account to draw from.
* Attribute `hasRecipient`. Boolean. `true` for cheque.
* Attribute `recipientNymID`. Identifier. Set for cheque.
* Attribute `hasRemitter`. Boolean. `true` for voucher.
* Attribute `remitterNymID`. Identifier. Set for voucher.
* Attribute `remitterAcctID`. Identifier. Set for voucher.
* Attribute `validFrom`. Timestamp.
* Attribute `validTo`. Timestamp.
* Optional element `memo`. Armored text.

### TODO

* Split into `<cheque>` and `<voucher>`
* Remove `has*` attributes (redundant)
* Rename `senderAcctID` to `senderAccountID`
