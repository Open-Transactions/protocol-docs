---
title: Document Type notaryProviderContract
---

# Document type `<notaryProviderContract>`

Inherits from Base Document Type [Contract](contract.md).

The contract contains

* Description of the entity running the Notary.
* Description of how to connect to the Notary.
* Terms and conditions of using the Notary (inherited from Contract)
* A description of the keys to use in order to verify received messages and send
  encrypted message (inherited from Contract)

The hash of the `notaryProviderContract` yields the `serverID`, which is set in
[`<OTmessage>` documents](OTmessage.md).

A request to a Notary that has the `serverID` attribute implies that the client
agrees with the terms and conditions in the contract.

Previously know as _server contract_.

## Elements and attributes
* Attribute `version`. String. Hard-coded to `2.0`.
* Element `entity`. The legal entity this contract is about.
  * Attribute `shortname`. String. An arbitrary name chosen upon creation of the
    entity.
  * Attribute `longname`. String. A, presumably longer, arbitrary name chosen
    upon creation of the entity.
  * Attribute `email`. String. The email address of the entity.
  * Attribute `serverURL`. String. The URL of the server with which the entity
    is registered.
* Element `notaryServer`. The technical details about the notary the entity is
  registered.
  * Attribute `hostname`. String. The hostname of the server.
  * Attribute `port`. Integer. The port the server is listening on.
  * Attribute `URL`. String. The URL of the server, which includes the hostname
    and is the same as the `serverURL`. TODO is that a requirement?

## Example
```xml
<notaryProviderContract version="2.0">
    <entity shortname="$shortname"
            longname="$longname"
            email="$email"
            serverURL="$serverURL">

    <notaryServer hostname="$hostname"
                  port="$port"
                  URL="$URL" />

    <!-- Other elements written by super class -->
</notaryProviderContract>
```

## References
[OTServerContract::CreateContents](https://github.com/Open-Transactions/opentxs/blob/7cf2be697c5dc4e06cd95d77787373c9285ecce3/src/core/OTServerContract.cpp#L204)

## Notes
Also the attributes `hostname` and `port` seems quite redundant since both can
be encoded in the `URL` attribute. Perhaps this was done for the benefit of the
client. If that is the case one should consider adding the attribute
`protocol`. These three attributes combine into an URL.
