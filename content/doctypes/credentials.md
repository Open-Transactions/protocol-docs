# Document Types Related to Credential System

Example of the doctypes can be found inside the [registerNym example](registerNym.xml).

See also [this colored representation](https://drive.google.com/a/monetas.net/file/d/0Bztm5gBf7t8xMUFHTXd0UzF3dXM/view), courtesy of Justus Raniver.
Background information (the why) concerning `credentials`:

* Each `credential` has a `nymIDSource` and three key's (`A`, `E`, `S`).
  * The `nymID` (Identifier) is based on the hash of the `nymIDSource`. A nym consists of all credentials (master and key credentials). 
  * The `nymIDSource` is the "root" of the trust-chain. It can be anything that can authenticate the `masterCredential`.
    Insecure example for illustration: if the `nymIDSource` was an url, http://monetas-authenticator.monetas.org/, you would check if that url returns the master credential `ID`. If it does,
    the master credential is authenticated.
    * The same `nymIDSource` is shared between the `masterCredential` as well as any `keyCredential`. This is how you know they are part of the same nym (`nymID`).
    * Currently, only self-signing is supported (`nymIDSource` = `masterCredential`'s S-key). In the future, Bitcoin addresses, Namecoin addresses, X.509 etc. could be integrated.
  * The `nymIDSource` is used to authenticate  the `masterCredential`, including the A/E/S keys. The `S`-key is used for any further signing action.
  * The credential `ID` is based on the hash of the whole armored `<credential>` contents,
    which is self-signed by the credential's `S`-key. (TODO: is the hash based on the signed or non-signed data?).
  * Key types: either `A` (authentication), `E` (encryption) or `S` (signature).
    * Only `S` seems to be in use (TODO: investigate).
    * RSA public keys at the moment, but see [NEW MEP TODO](TODO).
* The self-signed `masterCredential` document is enclosed in the `credential` document.
  * (look for the `BEGIN MASTER KEY CREDENTIAL SIGNATURE` section).
  * The `masterCredential`'s `S`-key is used to sign the `keyCredential` (see `masterSigned`), to authenticate that keycredential.
* There is an "outer" `keyCredential` document, as well as another `keyCredential` document nested inside. [Confusing naming is confusing.](#possibleimprovements)
* The outer `keyCredential` is, like the `masterCredential`, a self-signed document. It contains `masterSigned`.
  * `masterSigned` contains the "real", **inner** `keyCredential` document and a signature thereof, made by the `masterCredential`'s `S`-key.
  * General concept: Master source (`nymIDSource`) authenticates the `masterCredential`'s A/E/S keys. That `S`-key in turn signs the `keyCredential`. 
  * `nymIDSource`: Copy of the `masterCredential`'s `nymIDSource`.
  * The **inner** `keyCredential` is just like any other credential, coming with `nymIDSource` and A/E/S keys.
    In addition, it contains the full self-signed `masterCredential` document (a 1:1 copy of the signed `masterCredential` document, enclosed in `masterPublic`).
    Why? The reason is that the `keyCredential` should contain the information of the master that signed it, and the master's signature to prove it has been signed.
    **Possible improvement**: Enclose only the `masterCredential`'s credential-`ID` instead of the full thing. 

FAQ:
* Q. Why is each credential self-signed? A. To prove that the credential's private key exists.
* Q. Why does `credentialList` exist along with the [`<nymData>` document](nymData.md)? A. Contains `valid=true/false`, which could be used to revoke keys (TODO: used?).
  * TODO: The whole message is (presumably) signed by the keyCredential's `S`-key. How can that signature be allowed revoke the `masterCredential`? Shouldn't only something signed by the master be able to revoke subkeys?


## Document Type `<masterCredential>`

* Attribute `nymID`. Identifier.
* Element `nymIDSource`. String. Defines source of nymID.
* Element `publicInfo` of document type `publicInfo`. See below.

## Document Type `<keyCredential>`

### Structure

#### When in `<credentials>/<credential>`

* Attribute `nymID`. Identifier.
* Attribute `masterID`. Identifier. Credential ID of the `masterCredential`.
* Element `nymIDSource`. String.
* Element `masterSigned`. Armored version of this document that is signed by
  master credentials.

Signed by the signature key specified in `masterSigned` copy of these key
credentials.

#### When in `<credentials>/<credential>/<masterSigned>`

* Attribute `nymID`. Identifier.
* Attribute `masterID`. Identifier.
* Element `nymIDSource`. String.
* Element `masterPublic`. Armored copy of the **master** credentials that sign
  the key credentials.
* Element `publicInfo` of document type `publicInfo`. See below.

Signed by the keys contained in `<masterPublic>`.

## Document Type `<publicContents>`

### Structure

* Attribute `count`. Integer. Number of `publicInfo` sub-elements.
* List of `publicInfo` elements.
  * Attribute `key`. Either `A` (authentication), `E` (encryption) or `S`
      (signature).
  * Contains armored public key.

### Possible Improvements
* Disambiguate the two different `keyCredential` documents by renaming the "outer" one.
* The outer `keyCredential`'s `nymIDSource` can be removed, as it also appears nested in `masterSigned`.
  => Remove one layer. Remove the outer `keyCredential`
* Remove `masterPublic`, replace it by providing the `masterID` only. This is sufficient to prove that the master signed the keyCredential (as it is still inside `masterSigned`, with the master's signature).
* TODO (for Otto to fill out): Why could we remove the `masterCredential` document, as discussed in the hangout?
* Remove self-signing: it does not matter if there is proof of the private key, when looked at the credentials in isolation.
  However, you can't register credentials without signing off on the document, and cannot use the credential anywhere else either without a signature. Self-signing of credentials adds nothing. It complicates the whole structure, parsing, writing.
* Flatten the chain of trust: at the moment, the nymIDSource authenticates the master's A/E/S keys, and the S-key authenticates the subkeys, which also have A/E/S keys. There should be one master source only (either a pubkey or any other nym source), that authenticates subkeys, one for each type (A/E/S). (Not multiple subkeys, which each have A/E/S). That way, you can revoke keys individually, instead of A/E/S-bundles. This way, we would drop the master A/E/S keys, and use the nym source to directly authenticate subkeys of different types.
