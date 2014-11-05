# Document type `<OTuser>`

## Example
```xml
<?xml version="2.0"?>
<OTuser version="1.0"
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

</OTuser>
```

## Elements and attributes
* Element `OTuser`: a container.
  * Attribute `version`: the container version.
  * Attribute `nymID`: The ID of the Nym being described.
* Element `nymIDSource`: the source passed to the 'newnym' command when the Nym
  was created, or the generated public key when nothing was passed. This is a
  repeatition of the earlier nymIDSource element.
* Element `masterCredential`: TODO: I believe this is the top level key of the
  set of credentials a Nym has. I though this was only used so sign other keys
  or something so I don't know why it's included here.
  * Attribute `ID`: The ID of the (sub)credential.
  * Attribute `valid`: whether or not this key is still valid. TODO: who/what
    determines this?
* Element `keyCredential`: presumably one of the encryption or signing keys.
  * Attribute `masterID`: The ID of the master credential.
  * Attribute `ID`: The ID of the (sub)credential.
  * Attribute `valid`: whether or not this key is still valid. TODO: who/what
    determines this?
