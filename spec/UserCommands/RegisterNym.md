Deriving from [NotaryRequest and NotaryReply](../NotaryRequestAndNotaryReply.md)
respectively.

# Document Type `RegisterNym`

Root Element Name: `registerNym`.

Additional Elements:

* `credentialList`: Contains a signed document of type
  [PublicNymfile](../PublicNymfile.md)
* `credentials`: Contains signed Key-Value pairs. TODO.


# Document Type `RegisterNymReply`

Root Element Name: `registerNymReply`.

Additional elements:

* `nymfile`: Contains a signed document of type
  [PublicNymfile](../PublicNymfile.md)

# Notes

* The original root element name was `createUserAccount`. The new element name
  matches the new naming conventions.
* The attribute `requestNum` has the fixed value of `1` in this message.
* `CredentialList` and `Credentials` can probably be merged into one document
  type.
