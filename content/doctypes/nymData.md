---
title: Document Type nymData
---

# Document type `<nymData>`

## Elements and attributes
* Attribute `version`: String. The container version.
* Attribute `nymID`: Identifier. The ID of the Nym being described.
* Element `nymIDSource`: the source passed to the `newnym` command when the Nym
  was created, or the generated public key when nothing was passed.
* Element `masterCredential`: This is the top level key of the set of
  credentials a Nym has. Is used to validate the signature on the subcredentials.
  * Attribute `ID`: Identifier. The ID of the (sub)credential.
  * Attribute `valid`: Boolean. Whether or not this key is still valid. TODO: who/what
    determines this?
* Element `keyCredential`: presumably one of the encryption or signing keys.
  Can occur more than once.
  * Attribute `masterID`: Identifier. The ID of the master credential.
  * Attribute `ID`: Identifier. The ID of the (sub)credential.
  * Attribute `valid`: Boolean. Whether or not this key is still valid. TODO: who/what
    determines this?
* Element `subCredential`: presumably one of the encryption or signing keys.
  Can occur more than once.
  * Attribute `masterID`: Identifier. The ID of the master credential.
  * Attribute `ID`: Identifier. The ID of the (sub)credential.
  * Attribute `valid`: Boolean. Whether or not this key is still valid, eg not
    revoked. TODO: who/what determines this?


# Example
```xml
<nymData version="1.0"
 nymID="voeqDVAJwDwROywFtg5mEyYpcu2dcPJRLFHYh7tyhqk">

  <nymIDSource>
    $nymIDSource (armored)
  </nymIDSource>

  <masterCredential
   ID="UESzvzSw6zqC7BuvlcHmbPKTmf5BIbG0RNvCTpDx1kH"
   valid="true"/>

  <keyCredential
   ID="EvcfMaNJ3fiK4lUVhQNhHmX1Qc3mjlA6q0MS96JXNNO"
   masterID="UESzvzSw6zqC7BuvlcHmbPKTmf5BIbG0RNvCTpDx1kH"
   valid="true"/>

</nymData>
```

# References
* [OTPseudonym::OTPseudonym::SavePseudonym](https://github.com/Open-Transactions/opentxs/blob/0de3b72/src/core/OTPseudonym.cpp#L4273)
* [OTPseudonym::SaveCredentialListToString](https://github.com/Open-Transactions/opentxs/blob/0de3b72/src/core/OTPseudonym.cpp#L4098)
* [OTPseudonym::GetPublicCredentials](https://github.com/Open-Transactions/opentxs/blob/0de3b72/src/core/OTPseudonym.cpp#L4023)
* [OTPseudonym::GetPrivateCredentials](https://github.com/Open-Transactions/opentxs/blob/0de3b72/src/core/OTPseudonym.cpp#L4055)
* [OTCredential::SerializeIDs](https://github.com/Open-Transactions/opentxs/blob/da07760/src/core/crypto/OTCredential.cpp#L1553)

# Notes
This document type is often embedded in other document types which already
include `nymIDSource` leading to some duplication here. TODO it's unclear how
prevalent this is or whether it is necessary to correct this.

