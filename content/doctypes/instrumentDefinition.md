# Document Type `<instrumentDefinition>`

Inherits from base Document Type Contract.

## Elements and attributes

* Attribute `version`. String. Hard-coded to 2.0
* Element `entity`.
    * Attribute `shortname`. String.
    * Attribute `longname`. String.
    * Attribute `email`. String.
* Element `issue`.
    * Attribute `company`. String.
    * Attribute `email`. String.
    * Attribute `contractUrl`. String.
    * Attribute `type`. String. "currency" or "shares"
* Optional element `currency`. Included if type is "currency".
    * Attribute `name`. String. The "unit of account", this is the
      name used in normal conversation (e.g., dollars, not cents)
    * Attribute `tla`. String. Three-letter acronym for the currency
    * Attribute `symbol`. String. A small symbol used to indicate this
      currency, e.g. $
    * Attribute `type`. String. Currently unused
    * Attribute `factor`. String. How many of the "fraction" units in
      the "unit of account (e.g. 100 cents to the dollar)
    * Attribute `decimal_power`. String. Number of zeros after the
      decimal point (e.g. dollars and cents have 2)
    * Attribute `fraction`. String. The name of the smallest unit that
      can be transacted (e.g., cents, not dollars)
* Optional element `shares`. Included if type is "shares".
    * Attribute `name`. String.
    * Attribute `symbol`. String.
    * Attribute `type`. String.
    * Attribute `issuedate`. String.

## Example

```xml
<instrumentDefinition version="2.0">

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

</instrumentDefinition>
```

## References

[AssetContract::CreateContents()](https://github.com/Open-Transactions/opentxs/blob/be111238c0feb569462b2e710e7570c00aa3d8db/src/core/AssetContract.cpp#L776)

[currency element](http://www.systemics.com/docs/ricardo/issuer/contract.html)
