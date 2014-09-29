# OTContract Summary

This is the superclass for many other classes in Open-Transactions, most notably
`OTMessage` and `OTTransactionType`.

## Deserialization

Strings can be parsed into instances of [`OTContract`][OTContract].

Defined by

* [ParseRawFile()][ParseRawFile]
* [LoadContractXML()][LoadContractXML]
* [ProcessXMLNode()][ProcessXMLNode]

A contract can be loaded if:

* it has a valid content section;
* it has at least one valid (parsable) signature section.

### Sections

Contract documents are parsed line-by-line. Lines that start with a dash (`-`)
have special meaning:

* A line that starts with `---` and has the `BEGIN` keyword marks the beginning
  of the content section, which is given in XML.

* A line that starts with `---` and has the `SIGNATURE` keyword marks a
  signature section, which is ended with a single dash `-`. A contract may
  contain many signatures. For more information on the signature format, see
  [Signatures](#Signatures).

Inside the content section, lines that start with a dash must add a space after
the first dash.

### XML Structure

A contract is defined by a root element with an arbitrary name, which is
processed in the sub-classes. The root element has these XML elements:

Entity Name | Description
------------|------------
`entity`    | Describes the name and email address of the contract author.
`condition` | Describes the contract condition. A contract can have multiple conditions.
`signer`    | Describes the _signer_ Nym of this contract. The signer must provide a credential list that verifies the Nym.

### Sample document

```xml
--- BEGIN
HASH: foo

<!--
This content is aggregated into m_unsignedXML.

Only the subsequent tags are recognized, all other content is ignored.
-->

<rootElement>

<entity shortname="string" longname="string" email="string">

<condition name="key">
    Plain-Text Value
    <!-- these end up in a map that is local to OTContract -->
</condition>

<condition name="other-key">
   Plain-text
   <!-- if a condition has the same name as a previous one,
        the previous one is overwritten -->
</condition>

<signer nymID="mandatory string" hasCredentials="bool" altLocation="asciiarmor">
    <!-- altLocation is unsupported and is ignored, see comments -->
    <nymIDSource>Mandatory ASCII Armored stuff</nymIDSource>
    <credentialList>
        <!-- loaded into var ascArmor, dearmored into credentialList -->
        <credentials>
        <!-- dearmored and decoded into type OTDB::StringMap -->
        <credentials>
    </credentialList>
    <!--
    Only the first credentials/credentialList seems to be recognized.
 
    A new nym is initialized with the nymId, the credentialList
    and the credentialMap. If verification succeeds the method
    returns true, otherwise we continue.

    The nym is stored as this contract's `signer`.
    -->
</signer>

<!--
Sometimes you want to embed book-ended sections that start with a triple-dash.
You can do this by adding a space after the first dash.
-->

- --- BEGIN
- --- SIGNATURE

<!-- the content section ends with a signature marker -->
--- SIGNATURE
Meta: four-letter signature metadata

<!-- encoded signature data -->

</rootElement>
-

--- SIGNATURE
multiple signatures allowed
-
```

## Verification

A contract instance can be verified for integrity for an authentic signature
by the `signer` nym.

Defined by

* [VerifyContract(nym)][VerifyContract]
* [VerifySignature(myn)][VerifySignature]


Contract verification as defined in `VerifyContract()` succeeds if

1. The contract-id field `m_ID` (hash) matches the hash of the trimmed field
   `m_strRawFile`.
1. Check if one of the parsed signatures matches one of the _signing_ keys of
   the contract's `signer` Nym.

## Serialization

Contracts can be serialized into strings

Defined by

* [RewriteContract()][RewriteContract]
* [AddBookendsAroundContent()][AddBookendsAroundContent]




# Signature Format

A signature block is written as:

```
-----BEGIN $contractType SIGNATURE-----
Version: OpenTransactions [version] // ignored
Comment: http://github.com/FellowTraveler/Open-Transactions/wiki // ignored
Meta:    [four-character tag]

[signature data]
```

The four-character `Meta` tag is defined as:

* The key type: `E`, `S` or `A` (Encryption, Signing or Authentication)
* First character of the Nym Id
* First character of the Master-Credential Id
* First character of the Sub-Credential Id

This tag aids the signature verification process. If the 

# Notes and open questions

## Deserialization

* The current parser for this document format is too complex and probably has
  some bugs.
* The content field is marked by the `BEGIN` marker, the signature field by the
  `SIGNATURE` marker. During serialization, the signature bookend is written as
  `BEGIN <contractType> SIGNATURE`. When a contract is missing an explicit
  content section, the first signature block is interpreted as the content.
* The `HASH:` field is meant to be read right after the `BEGIN` marker, but can
  in fact be set anywhere: [Link][ProcessHash]. Possible unintended
  consequences, maybe even exploit.
* The first character of metadata defines the type of key that is used. Is there
  any way that a key that doesn't start with `S` can be valid?


## Verification

* The integrity check compares the value of class field `m_ID` against the
  calculated hash value of the contract. When is the ID field passed explicitly
  and not derived from the content section of the contract?
* We authenticate against either the `signer` field or the `contract` field that
  is not read during deserialization. I'm guessing that `contract` is
  depreacted.
* There deprecated single-key-system makes an appearance also when loading the
  public keys for signature verification.
* Signature verification takes a `OTPasswordData` argument with unclear purpose.

[OTContract]: https://github.com/Open-Transactions/opentxs/blob/171bdbdd1327fa016f2043bf43d8662055d263d2/src/core/OTContract.cpp

[ParseRawFile]: https://github.com/Open-Transactions/opentxs/blob/db31c6aa45bbb773aebbdbd4298acd3755785420/src/core/OTContract.cpp#L1374

[LoadContractXML]: https://github.com/Open-Transactions/opentxs/blob/db31c6aa45bbb773aebbdbd4298acd3755785420/src/core/OTContract.cpp#L1642

[ProcessXMLNode]: https://github.com/Open-Transactions/opentxs/blob/db31c6aa45bbb773aebbdbd4298acd3755785420/src/core/OTContract.cpp#L2391

[ProcessHash]: https://github.com/Open-Transactions/opentxs/blob/171bdbdd1327fa016f2043bf43d8662055d263d2/src/core/OTContract.cpp#L1575



[RewriteContract]: https://github.com/Open-Transactions/opentxs/blob/171bdbdd1327fa016f2043bf43d8662055d263d2/src/core/OTContract.cpp#1196

[AddBookendsAroundContent]: https://github.com/Open-Transactions/opentxs/blob/171bdbdd1327fa016f2043bf43d8662055d263d2/src/core/OTContract.cpp#1148



[VerifyContract]: https://github.com/Open-Transactions/opentxs/blob/171bdbdd1327fa016f2043bf43d8662055d263d2/src/core/OTContract.cpp#330

[VerifySignature]: https://github.com/Open-Transactions/opentxs/blob/171bdbdd1327fa016f2043bf43d8662055d263d2/src/core/OTContract.cpp#818
