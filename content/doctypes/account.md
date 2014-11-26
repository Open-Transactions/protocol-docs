---
title: Document Type account
menu:
  main:
    parent: doctypes
---

## Document Type `<account>`

### Elements and attributes
* Attribute `type`. String. TODO
* Attribute `version`. Integer?
* Attribute `accountID`. Identifier.
* Attribute `nymID`. Identifier.
* Attribute `notaryID`. Identifier.
* Attribute `instrumentDefinitionID`. Identifier.
* Optional element `stashinfo` (optional)
    * Attribute `cronItemNum`. Integer. Never set anywhere in the old code.
      Probably dead code.
* Optional element `inboxHash value`. Only included after at least one
  transaction was received in this account.
    * Attribute `value`. Identifier.
* Optional element `outboxHash`. Only included after at least one
  outgoing transaction was done from this account.
    * Attribute `value`. Identifier.
* Element `balance`.
    * Attribute `date`. Timestamp. When balance was last modified.
    * Attribute `amount`. Integer.

## Example
```xml
<account
 version="2.0"
 type="issuer"
 accountID="otxDTMAWCgcwmyUhWcQWYJk8ZUNd9ybiE7is"
 nymID="otxEFjTbwqL45w6HMtJXGE4rkfdtzzser5pb"
 notaryID="otxCCaj94hNjhTc9Gz8CJ6c8yKNLvBY4iDAs"
 instrumentDefinitionID="otxSRp81acSP921Z748v7CpD2xwpxSTT1e88" >

<balance date="1415624440" amount="0"/>

</account>
```

## References

[Account::UpdateContents()](https://github.com/Open-Transactions/opentxs/blob/d032df5e2012ca15be9d09231e46e4a28c6cd51c/src/core/Account.cpp#l749)
