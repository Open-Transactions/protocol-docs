# OTContract Summary

## Deserialization

Strings can be parsed into instances of [`OTContract`][OTContract]

The process is defined by the methods
* [ParseRawFile()][ParseRawFile]
* [LoadContractXML()][LoadContractXML]
* [ProcessXMLNode()][ProcessXMLNode]

A contract can be loaded if:

* It has a valid content section
* It has at least one valid signature section

### Sections

Contract documents are parsed line-by-line. Lines that start with a dash (`-`) have special meaning:

* A line that starts with `---` and has the `BEGIN` keyword marks the beginning of the content section, which is given in XML.

* A line that starts with `---` and has the `SIGNATURE` keyword marks a signature section, which is ended with a single dash `-`. A contract have contain many signatures. For more information on the signature format, see [Signatures](#Signatures).

Inside the content section, lines that start with a dash must be followed by a single space.

### XML Structure

A contract is defined by a root element with an arbitrary name, which is processed in the sub-classes. The root element has these XML elements:

Entity Name | Description
------------|------------
`entity`    | Describes the name and email address of the contract author.
`condition` | Describes the contract condition. A contract can have multiple conditions.
`signer`    | Describes the Nym of the contract author. The signer must provide a credential list that verifies the nym.

### Sample document

```xml
--- BEGIN
HASH: foo

<!--
This content that is aggregated into m_unsignedXML.

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
   Only the first credentials/credentialList seems to be
   recognized.

   A new nym is initialized with the nymId, the credentialList
   and the credentialMap. If verification success, the method
   returns true, otherwise, we continue.
   -->
</signer>

<!--
Sometimes you want to embed book-ended sections
that start with a triple-dash. You can do this by
adding a space after the first dash.
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

# Notes

* The current parser for this document format is too complex

[OTContract]: https://github.com/Open-Transactions/opentxs/blob/171bdbdd1327fa016f2043bf43d8662055d263d2/src/core/OTContract.cpp

[ParseRawFile]: https://github.com/Open-Transactions/opentxs/blob/db31c6aa45bbb773aebbdbd4298acd3755785420/src/core/OTContract.cpp#L1374

[LoadContractXML]: https://github.com/Open-Transactions/opentxs/blob/db31c6aa45bbb773aebbdbd4298acd3755785420/src/core/OTContract.cpp#L1642

[ProcessXMLNode]: https://github.com/Open-Transactions/opentxs/blob/db31c6aa45bbb773aebbdbd4298acd3755785420/src/core/OTContract.cpp#L2391
