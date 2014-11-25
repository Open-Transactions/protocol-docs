---
title: Base Document Type Contract
menu:
  main:
    parent: doctypes
---

## Base Document Type `<Contract>`

This document type does not manifest itself alone. But several Document types
inherit from this type.

Inheriting Document types:

* [notaryProviderContract](notaryProviderContract.md)
* [instrumentDefinition](instrumentDefinition.md)
* basketContract

### Elements and attributes
* Element `condition`: a human readable piece of (legal) text. Can appear more
  than once.
  * Attribute `name`: a human readable marker.
* Element `signer`: the Nym doing the signing.
  * Attribute `hasCredentials`: whether or not the signer has credentials
    associated with it.
  * Attribute `nymID`: the Nym doing the signing.
  * Attribute `altLocation`: some optional value passed when creating a Nym.
* Element `nymIDSource`: the source passed to the 'newnym' command when the Nym
  was created, or the generated public key when nothing was passed.
* Element `credentialList`: a list of public credentials if present. See
  [nymData](nymData.md).
* Element `credentials`: List of credentials mentioned in `credentialList`
  * Element `credential`: Armored signed credential (document `keyCredential` or `masterCredential`)
    * Attribute `ID`: credential ID

### Example

```xml
<rootElement> <!-- written by subclass -->

<condition name="$name">
    $value
</condition>

<signer hasCredentials="$hasCredentials"
        nymID="$nymId"
        altLocation="$altLocation">

    <nymIDSource>
        $nymIDSource (armored)
    </nymIDSource>

    <credentialList>
        $credentialList (armored)
    </credentialList>

    <credentials>
        <credential ID="otxBtT5iGYJWENegXYyHRbjUCXE5RY6qnXTq">
            $credential (armored)
        </credential>
    </credentials>
</signer>

<!-- other XML written by subclass -->

</rootElement>
```

### References

* [OTContract::CreateInnerContents](https://github.com/Open-Transactions/opentxs/blob/7cf2be697c5dc4e06cd95d77787373c9285ecce3/src/core/OTContract.cpp#L2158)
* [BasketContract::CreateContents](https://github.com/Open-Transactions/opentxs/blob/7cf2be697c5dc4e06cd95d77787373c9285ecce3/src/basket/BasketContract.cpp#L175)
* [AssetContract::CreateContents](https://github.com/Open-Transactions/opentxs/blob/7cf2be697c5dc4e06cd95d77787373c9285ecce3/src/core/AssetContract.cpp#L776)
* [OTServerContract::CreateContents](https://github.com/Open-Transactions/opentxs/blob/7cf2be697c5dc4e06cd95d77787373c9285ecce3/src/core/OTServerContract.cpp#L204)
