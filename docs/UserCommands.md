# User Commands

The creation, serialization, deserialization and execution of RPC-calls,
particularly of those that use `OTMessage`. Instances have a `command` and can
either be a request or reply.

The encoding of different command types and their corresponding replies is done
inside very, very long `switch`-blocks.  The switch happens on the `commandType`
attribute of `OTMessage`. We should group the required attributes,
serialization, and deserialization and execution and response by the command
type, not by processing stage.

This is rough guide on how to analyze the RPC system and the message format from
the protocol perspective.

Each command type sent by the client has a corresponding reply message with a
`@` character prefixed to the command type identifier.


We will focus on the most important command types first and expand to the less
used ones later:

* `createUserAccount` (aka RegisterNym)
* `getTransactionNum`
* `createAccount`
* `issueAssetType`
* `notarizeTransfer`, `notarizeWithdrawal`, ...

## Creation

The command-specific message creation on the client side happens in
[`OTClient::ProcessUserCommand(command, message, nym)`][ProcessUserCommand].
The switch happens on the enum [`OT_CLIENT_CMD_TYPE`][EnumCmdType]. Each `case`
block contains these types of actions:

* Prepare necessary attributes (initialize nym, get balance)
* Set attributes
* Set request number (TODO explain)
* Add signature

The message creation on the server side happens in TODO

## Serialization

After the attributes and the command type is set, the message is serialized in
side [`OTMessage::UpdateContents()`][UpdateContents].

The method consists of (what is equivalent to) a very, very long switch on the
`strCommand` attribute that was set previously. If the attribute starts with the
`@` character, a server reply is being serialized that matches the request.

The serialization outputs a single XML element with the `strCommand` as the
element name and the other class attributes as XML element attributes. Common
class attributes are

* `requestNum`
* `nymId`
* `serverId`

Server replies usually contain the attribute `success` and an element
`messagePayload` that is conditional on the successful execution of the request.


## Deserialization

The deserialization happens in [`OTMessage::ProcessXMLNode`][ProcessXMLNode] and
mirrors the serialization process. There is a switch on the XML element name
being read. The command string and other class attributes are set accordingly
when there is a match.

Note that XML elements are processed in the orderas they are found in the
document. A later definition of a command overrides a previous one (but doesn't
clear the attributes).

## Execution

The server processes received client commands in
[`UserCommandProcessor::ProcessUserCommand`][UCP_ProcessUserCommand]

# Example: analysis of `createUserAccount`

### Construction

In `case (OTClient::createUserAccount)`[Construction_createUserAccount]:

* Initialize Nym that is to be registered
* Set attributes `nymId` and `serverId`
* Set request number
* Sign with Nym

### Serialization

In [`if (strNode.Compare("createUserAccount"))`][Serialization_createUserAccount]:

Creates this XML:

```
<createUserAccount
    requestNum="$requestNum"
    nymId="$nymId"
    serverId="$serverId">

    <credentialList>
        $credentialList (ascii-armored)
    </credentialList>

    <credentials>
        $credentials (ascii-armored)
    </credentials>

</credentials>
```

### Deserialization

Once the message is received on the server side, it is deserialized in
`OTMessage::ProcessXMLNode()`:

* Reads attributes `requestNum`, `nymId` and `serverId`.

* Reads `credentialList` element into field `ascPayload` and `credentials`
  element into field `ascPayload2`.

### Execution

The re-assembled `OTMessage` object is processed in the conditional block
`if(command.Compare("createUserAccount"))`.

1. Check permissions
1. Check existence and integrity of attributes
1. Create all the necessary data on the server
1. Assemble response `OTMessage` with command `@createUserAccount`

(Incomplete)


# Notes

* `ProcessUserCommand` relies on `stdin` for some messages
* OTMessage is too generic to handle all the specific subtypes.
* There should only be one command type element allowed in a OTMessage.


[EnumCmdType]: https://github.com/Open-Transactions/opentxs/blob/e16f3449df27f75d658b0d071c8caf47fbef5db1/include/opentxs/client/OTClient.hpp#L200

[ProcessUserCommand]: https://github.com/Open-Transactions/opentxs/blob/e16f3449df27f75d658b0d071c8caf47fbef5db1/src/client/OTClient.cpp#L8942

[UCP_ProcessUserCommand]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/server/UserCommandProcessor.cpp#L166

[UpdateContents]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/core/OTMessage.cpp#L310

[ProcessXMLNode]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/core/OTMessage.cpp#L1676


