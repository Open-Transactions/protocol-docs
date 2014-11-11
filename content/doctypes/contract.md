---
title: Document Type Contract
---

# Document type `<Contract>`

This document type does not manifest itself alone. But several Document types
inherit from this type.

Inheriting Document types:
* notaryProviderContract
* digitalAssetContract
* TODO: anything else?

## Elements and attributes
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
  [OTuser](OTuser.md).
* Element `credentials`: TODO figure out what this is.

## Example

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
        $credentialMap (OTDB encoded, armored)
    </credentials>
</signer>

<!-- other XML written by subclass -->

</rootElement>
```

## References
[OTContract::CreateInnerContents](https://github.com/Open-Transactions/opentxs/blob/7cf2be697c5dc4e06cd95d77787373c9285ecce3/src/core/OTContract.cpp#L2158)
