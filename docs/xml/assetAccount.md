#Document Type `<assetAccount>`

* Attribute `version`. Integer. Hard-coded to 1.0.
* Attribute `type`. String. TODO
* Attribute `version`. Integer?
* Attribute `accountID`. Identifier.
* Attribute `userID`. Identifier.
* Attribute `serverID`. Identifier.
* Attribute `assetTypeID`. Identifier.
* Optional element `stashinfo cornItemNum` (optional)
    * Attribute `cronItemNum`. Integer. TODO
* Optional element `inboxHash value`. TODO when included?
    * Attribute `value`. Identifier.
* Optional element `outboxHash value`. TODO when included?
    * Attribute `value`. Identifier.
* Element `balance`.
    * Attribute `date`. Timestamp.
    * Attribute `amount`. Integer.
* Optional element `MARKED_FOR_DELETION`. TODO: client only?

#References
[Account::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/d032df5e2012ca15be9d09231e46e4a28c6cd51c/src/core/Account.cpp#l749)

#Notes

* The attribute `assetTypeID` is called `assetType` in other
  document types. Should be unified in future versions of the
  protocol.
