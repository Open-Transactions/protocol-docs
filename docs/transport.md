# Transport layer

This document examines the program flow from the point a command is issued till it is put on the ZMQ socket.

## Concepts
There are three differnt concepts which play a pivotal role in messaging.

### Request
The is a generic object of type `OTAPI_Func` which has embedded in it the _type of request_ and _all required parameters_ for that request. For example createing an account or doing a transaction. The variable containing this concept is always called `theRequest`.

### Message
Once the request is executed a message is constructed. This message holds not only the passed arguments but also some bookkeeping in the form of request numbers and signatures. The variable is called `theMessage` and of type `OTMessage`.

### Envelope
A message is eventually converted to `theEnvelope` (which is not an entirely accurate description). This is a serialized, armoured message. This is what is put on the wire. The variable is called `theEnvelope` and of type `OTEnvelope`.

## Exhaustive trace

1. (`MadeEasy`, `ot_made_easy_ot`) The methods defined in the class MadeEasy generally refer to a `theRequest` object which is an instance of `OTAPI_Func` (no idea where it is instantiated).
2. (`OTAPI_Func`, `ot_otapi_ot`) On that object `SendRequest` is called, which is defined in on the class `OTAPI_Func`.
3. (`OTAPI_Func`, `ot_otapi_ot`) `SendRequest` calls `SendRequestOnce`.
4. (`OTAPI_Func`, `ot_otapi_ot`) `SendRequestOnce` calls `SendRequestLowLevel`.
5. (`OTAPI_Func`, `ot_otapi_ot`) `theRequest` is being passed around in steps 2-4 and eventually `Run` is called on it.
6. (`OTAPI_Wrap`, `OTAPI`) Based on whatever type the `theRequest` object has a function defined on the class `OTAPI_Wrap` is called. For example `OTAPI_Wrap::createAssetAccount`.
7. (`OPAPI_Exec`, `OTAPI_Exec`) `OTAPI_Wrap` delegates to `OTAPI_Exec`.
8. (`OT_API`, `OpenTransactions`) Which delegates to `OT_API`.
9. (`OT_API`, `OpenTransactions`) In the createAssetAccount method `theMessage` is constructed, fields are being populated (like `m_strCommand` and `m_strAssetID`).
10. (`OT_API`, `OpenTransactions`) All the while a couple of magically defined variables is accessable and passed as arguments. Eg: `serverID, nymID, assetID`.
11. (`OT_API`, `OpenTransactions`) `theMessage` is saved (to disk?) with `SaveContract`.
12. (`OTClient`,`OTClient`) Goes to `ProcessMessageOut` in `OTClient` which stringyfies `theMessage` (In what order? That seems to matter a few steps down.).
  1. Stringyfying goes but making a new OTString object and passing `theMessage` (which is an OTMessage, which is a subclass of OTContract) to the constructor.
  2. The constructor calls `SaveContractRaw` with the message. And does some casting dance.
  3. Which calls `Get` on the `m_strRawfile` (not sure how that got populated or why this is a file all of the sudden).
  4. This just casts to a char pointer (from already a char pointer?).
13. (`OTClient`,`OTClient`) Which then delegates to `ProcessMessageOut` in `OTServerConnection`.
14. (`OTServerConnection`,`OTServerConnection`) The first character in the character buffer which was once `theMessage` determines how with function is passed into ProcessUserCommand, which populates a new `theMessage`.
  1. The buffer seems really small. Only a [couple of characters](https://github.com/Open-Transactions/opentxs/blob/a29d030669b87308509ee0c29a6016f39c7fa6e0/src/client/OTServerConnection.cpp#L833) long.
15. (`OTServerConnection`,`OTServerConnection`) Flow is then directed to yet an other `ProcessMessageOut` this time _only_ accepting an `theMessage`.
16. (`OTServerConnection`,`OTServerConnection`) `theEnvelope` is filled with the content of `theMessage` with the `SaveContractRaw` method.
17. (`OTServerConnection`,`OTServerConnection`) A header is constructed.
  1. The header contains how long a message is and what the type of the message is.
  2. Type of the message is defined by two integers. Presumably the server knows.
  3. I don't think the header is actually used (https://github.com/Open-Transactions/opentxs/blob/develop/src/client/OTServerConnection.cpp#L590).
18. (`OTServerConnection`,`OTServerConnection`) The member field containing `Callback` is called with `ServerContract` and `theEnvelope`.
  1. `ServerContract` is a member field of the `OTServerConnection` class.
  2. Presumably this sends the message.
19. (`OTServerConnection`,`OTServerConnection`) `Callback` is set in the `SetFocus` method and is of type `TransportCallback`.
20. (`OTClient`,`OTClient`) `SetFocus` is called in `OTClient` in `SetFocusToServerAndNym`.
21. (`OT_API`, `OpenTransactions`) Eventually it boils down to the `GetTransportCallback` method in `OpenTransactions.cpp`, which is a glorified getter.
22. (`OPAPI_Exec`, `OTAPI_Exec`) The corresponding setter `SetTransportCallback` is called in `OTAPI_Exec`.
23. (`TransportCallback`, `TransportCallback`) This is fed a new instance of `TransportCallback` in which the only constructor argument is a new instance of `OT_API` (defined in `OpenTransactions.cpp`) constructed without any arguments.
  1. `TransportCallback` is just a wrapper, doing nothing really.
  2. It does define an `operator` function which takes a server contract and envelop.
24. (`OT_API`, `OpenTransactions`) I believe the `operator` function is called at some point. This delegates to `TransportFunction` on `OT_API`.
25. (`OT_API`, `OpenTransactions`) After a whole bunch of checks the envelope is armoured and the server address is cobbled together.
26. (`OTSocket`,`OTSocket`) These two are then passed to the `Send` method of the `OTSocket` class.
27. (`OTSocket_ZMQ_4`,`Socket_ZMQ4`) Which really is implemented in the `OTSocket_ZMQ_4` class.
28. (`OTSocket_ZMQ_4`,`Socket_ZMQ4`) After again a bunch of checks it _finally_ [creates](https://github.com/Open-Transactions/opentxs/blob/aea45331aa5b567fdee8500f90629e1a0046be8e/src/ext/Socket_ZMQ4.cpp#L422) a ZMQ message.
