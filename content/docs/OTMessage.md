# OTMessage

The `OTMessage` class is used for creating certain remote procedure calls from
the client and creating a call reply on the server. It uses the XML
serialization and deserialization systems inherited from the parent class
[OTContract](OTContract.md).

The document type created by this class is documented in
[xml/OTMessage.md](xml/OTMessage.md).

## Commands

Each message has a command attribute (string), which is one of those:

* `getMarketList`
* `getMarketOffers`
* `getMarketRecentTrades`
* `getNym_MarketOffers`
* `checkServerID`
* `createUserAccount`
* `deleteUserAccount`
* `getRequest`
* `outmailMessage`
* `usageCredits`
* `checkUser`
* `issueAssetType`
* `queryAssetTypes`
* `createAccount`
* `getBoxReceipt`
* `deleteAssetAccount`
* `issueBasket`
* `getTransactionNum`
* `notarizeTransactions`
* `getInbox`
* `getNymbox`
* `getOutbox`
* `getAccount`
* `getAccountFiles`
* `getContract`
* `getMint`
* `triggerClause`
* `processInbox`
* `processNymbox`


## Structure

A remote procedure call is serialized to XML in this general shape

```xml
<commandName
    attribute="$value"
    other-attribute="$value" >

    <complexAttribute>
        $data
    </complexAttribute>
</commandName>
```

The server responds with a reply:

```xml
<!-- notice the leading @ (invalid xml) -->
<@commandName
    attribute="$value"
    other-attribute="$value"
    <!-- most server replies seem to have a success attribute -->
    success="$value">


    <!-- successful replies often have inner elements -->

    <responseData>
        $response
    </responseData>

    <otherResponseData>
        $otherResponseData
    </otherResponseData>
</@commandName>
```

## Implementation in Opentxs

### Execution

The server processes received client commands in
[`UserCommandProcessor::ProcessUserCommand`][UCP_ProcessUserCommand]. Here the
attributes of the `OTMessage` instance are read and used in a method call. A
server reply message is preparted. The reply message has the "command string" of
the request with a leading `@` sign.

# Example: analysis of `createUserAccount`

### Execution

The re-assembled `OTMessage` object is processed in the conditional block
[`if(command.Compare("createUserAccount"))`][Execution_createUserAccount]:

1. Check permissions
1. Check existence and integrity of attributes
1. Create all the necessary data on the server
1. Assemble response `OTMessage` with command `@createUserAccount` (omitted
   here)

# Notes


* Some command names can be improved. The `createUserAccount` name should match
    the command `registerNym` in the `opentxs` shell and API. See
* `ProcessUserCommand` relies on `stdin` for some messages
* OTMessage is too generic to handle all the specific subtypes.
* There should only be one command type element allowed in a OTMessage.


[EnumCmdType]: https://github.com/Open-Transactions/opentxs/blob/e16f3449df27f75d658b0d071c8caf47fbef5db1/include/opentxs/client/OTClient.hpp#L200

[ProcessUserCommand]: https://github.com/Open-Transactions/opentxs/blob/e16f3449df27f75d658b0d071c8caf47fbef5db1/src/client/OTClient.cpp#L8942

[UCP_ProcessUserCommand]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/server/UserCommandProcessor.cpp#L166

[Construction_createUserAccount]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/client/OTClient.cpp#L8985

[Serialization_createUserAccount]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/core/OTMessage.cpp#L510

[Deserialization_createUserAccount]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/core/OTMessage.cpp#L2082

[Execution_createUserAccount]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/server/UserCommandProcessor.cpp#L412

[UpdateContents]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/core/OTMessage.cpp#L310

[ProcessXMLNode]: https://github.com/Open-Transactions/opentxs/blob/e03d287573a4f69a7e3acc1346a5b32492d7c502/src/core/OTMessage.cpp#L1676


