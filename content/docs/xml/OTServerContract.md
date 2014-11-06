# Document type `<notaryProviderContract>`
## Elements and attributes
* Element `entity`: The legal entity this contract is about.
  * Attribute `shortname`: An arbitrary name chosen upon creation of the entity.
  * Attribute `longname`: A, presumably longer, arbitrary name chosen upon creation of the entity.
  * Attribute `email`: The email address of the entity.
  * Attribute `serverURL`: The URL of the server with which the entity is registered.
* Element `notaryServer`: The technical details about the notary the entity is registered.
  * Attribute `hostname`: The hostname of the server.
  * Attribute `port`: The port the server is listening on.
  * Attribute `URL`: The URL of the server, which includes the hostname and is the same as the `serverURL`.

