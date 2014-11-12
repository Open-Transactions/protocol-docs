---
title: Document Type digitalAssetContract
---

# Document Type `<digitalAssetContract>`

Inherits from base Document Type Contract.

* Attribute `version`. Integer. Hard-coded to 1.0.
* Attribute `type`. String. TODO
* Attribute `version`. Integer. Hard-coded to 2.0
* Element `entity`.
    * Attribute `shortname`. String.
    * Attribute `longname`. String.
    * Attribute `email`. String.
* Element `issue`.
    * Attribute `company`. String.
    * Attribute `email`. String.
    * Attribute `contractUrl`. String.
    * Attribute `type`. String.
* Optional element `currency`. TODO when included?
    * Attribute `name`. String.
    * Attribute `tla`. String.
    * Attribute `symbol`. String.
    * Attribute `type`. String.
    * Attribute `factor`. String.
    * Attribute `decimal_power`. String.
    * Attribute `fraction`. String.
* Optional element `shares`. TODO when included?
    * Attribute `name`. String.
    * Attribute `symbol`. String.
    * Attribute `type`. String.
    * Attribute `issuedate`. String.

# References
[AssetContract::CreateContents()](https://github.com/Open-Transactions/opentxs/blob/be111238c0feb569462b2e710e7570c00aa3d8db/src/core/AssetContract.cpp#L776)
