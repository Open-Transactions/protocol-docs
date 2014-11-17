---
title: Document Type digitalAssetContract
---

# Document Type `<digitalAssetContract>`

Inherits from base Document Type Contract.

## Elements and attributes

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

## Example
```xml
<digitalAssetContract version="2.0">

<entity shortname="Satoshi"
 longname="Satoshi Nakamoto"
 email="satoshi@nakamoto.com"/>

<issue company="Swedish Coins"
 email="info@swedishcoins.net"
 contractUrl="https://swedishcoins.net/contract"
 type="currency"/>

<currency name="Bitcoins" tla="BTC" symbol="BTC" type="decimal" factor="1000"
decimal_power="3" fraction="mBTC" />

<!-- CONDITIONS -->

<condition name="audit">
  Bitcoins are audited monthly by highly trusted people.
</condition>

</digitalAssetContract>
```


## References
[AssetContract::CreateContents()](https://github.com/Open-Transactions/opentxs/blob/be111238c0feb569462b2e710e7570c00aa3d8db/src/core/AssetContract.cpp#L776)
