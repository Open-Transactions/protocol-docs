# Document Type "NotaryRequest"

This document type is the base type for all requests written by the Client and
read by the Notary.

It has some default attributes that are shared by deriving document types (see
[UserCommands/](UserCommands/))

Element name:
* specified by deriving document. Specifies the command to be executed.

Default Attributes:
* `nymID`: Identifier. The Nym which makes the request.
* `notaryID`: Identifier. The Notary which should process the request.
* `requestNum`: Integer. The index of the current request being sent.
  See [RequestNumbers.md](RequestNumbers.md) for more information.


# Document Type "NotaryReply"

This is the base type for messages sent by the Notary in response to a
_NotaryRequest_ by the Client.

Element name:
* specified by the deriving document. Matches the command name of the request
  and ends with `Reply`.

Default Attributes:
* `nymID`: Identifier. The Nym which made the request.
* `notaryID`: Identifier. The id of the notary
* `requestNum`: Integer. Same as in the request.
* `success`: Boolean. Indicates whether request was successful.

Default Elements:

* `inResponseTo`: Identifier. Hash of request message.

# Notes

* The old protocol had the attribute `serverID` instead of `notaryID`.
* The old protocol version has an `@` prefix for Notary responses instead of a
  `Reply` suffix.
* A few User Commands do not inherit all of the attributes. These exceptions are
  documented for the inheriting document type.
