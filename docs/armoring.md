# Armoring

Base64 armoring is applied at three different locations in OT:
* Encrypted blob armoring just before transmitting
* Whole contract armoring
* XML content armoring

## Just before transmitting

The entire message is armored just before it is being transmitted to the client or
server.

One might wonder if this is necessary. Since everything is first encrypted, I assume that
this is already resilient against corruption during transit.

### Headers

No headers present and none are required or mandated.

## Contract armoring

[Ricardian contracts](http://iang.org/papers/ricardian_contract.html) form the foundation
of OT. A Ricardian contract is a human and machine readable text and signed with OpenPGP
in clear text form (see section 3.1 of aforementioned document). The OpenPGP
[RFC](http://tools.ietf.org/html/rfc4880) states that in the case where there is a risk of
damage to the data due to transport, data conversions or character set translations the
content may be armored. OT assumes this is the case and always armors the content.

Each contract can have multiple nested contracts embedded in them. It is entirely possible
that one needs to run the de-armor operation on a message several times before it is
fully human readable.

### Headers

There _should_ be headers (as per section
[6.2](http://tools.ietf.org/html/rfc4880#section-6.2) of the RFC). But there appears to be
none.

## XML content

Some XML fields are also base64
[encoded](https://github.com/monetas/opentxs-protocol/blob/master/docs/OTContract.md#xml-content-section).
This is presumably done in order to make the XML parser not choke when special characters
appear in the field.

If the fields are armored for the benefit of the parser then we should consider moving to
[CDATA](http://en.wikipedia.org/wiki/CDATA) instead.


