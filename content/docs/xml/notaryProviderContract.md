# Document type `<notaryProviderContract>`

Previously know as _server contract_.

Defines the terms and conditions of a Notary, as well as methods to connect to
that notary.

The hash of the `notaryProviderContract` yields the `serverID`.

A request to a Notary that has the `serverID` attribute implies that the client
agrees with the terms and conditions in the contract.

Inherits from base document type OTContract. TODO

## Structure

* Element `entity`: The legal entity this contract is about.
  * Attribute `shortname`: An arbitrary name chosen upon creation of the entity.
  * Attribute `longname`: A, presumably longer, arbitrary name chosen upon creation of the entity.
  * Attribute `email`: The email address of the entity.
  * Attribute `serverURL`: The URL of the server with which the entity is registered.
* Element `notaryServer`: The technical details about the notary the entity is registered.
  * Attribute `hostname`: The hostname of the server.
  * Attribute `port`: The port the server is listening on.
  * Attribute `URL`: The URL of the server, which includes the hostname and is the same as the `serverURL`.
