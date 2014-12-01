# Document Types Related to Credential System

## Document Type `<masterCredential>`

* Attribute `nymID`. Identifier.
* Element `nymIDSource`. String. Defines source of nymID.
* Element `publicInfo` of document type `publicInfo`. See below.

## Document Type `<keyCredential>`

### Structure

#### When in `<credentials>.<credential>`

* Attribute `nymID`. Identifier.
* Attribute `masterCredentialID`. Identifier.
* Element `nymIDSource`. String.
* Element `masterSigned`. Armored version of this document that is signed by
  master credentials.

Signed by the signature key specified in `masterSigned` copy of these key
credentials.

#### When in `<credentials>.<credential>.<masterSigned>`

* Attribute `nymID`. Identifier.
* Attribute `masterCredentialID`. Identifier.
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

