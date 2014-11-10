---
title: Document Type assetAccount
---

# Document Type `<assetAccount>`

## Example
```xml
<?xml version="1.0"?>

<assetAccount
 version="2.0"
 type="issuer"
 accountID="otxDTMAWCgcwmyUhWcQWYJk8ZUNd9ybiE7is"
 userID="otxEFjTbwqL45w6HMtJXGE4rkfdtzzser5pb"
 serverID="otxCCaj94hNjhTc9Gz8CJ6c8yKNLvBY4iDAs"
 assetTypeID="otxSRp81acSP921Z748v7CpD2xwpxSTT1e88" >

<balance date="1415624440" amount="0"/>

</assetAccount>
```

## Elements and attributes
* Attribute `version`. Integer. Hard-coded to 1.0.
* Attribute `type`. String. TODO
* Attribute `version`. Integer?
* Attribute `accountID`. Identifier.
* Attribute `userID`. Identifier.
* Attribute `serverID`. Identifier.
* Attribute `assetTypeID`. Identifier.
* Optional element `stashinfo` (optional)
    * Attribute `cronItemNum`. Integer. TODO
* Optional element `inboxHash`. TODO when included?
    * Attribute `value`. Identifier.
* Optional element `outboxHash`. TODO when included?
    * Attribute `value`. Identifier.
* Element `balance`.
    * Attribute `date`. Timestamp.
    * Attribute `amount`. Integer.
* Optional element `MARKED_FOR_DELETION`. TODO: client only?

#References
[Account::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/d032df5e2012ca15be9d09231e46e4a28c6cd51c/src/core/Account.cpp#l749)
