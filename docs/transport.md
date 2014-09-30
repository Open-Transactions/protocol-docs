# Transport layer

1. `OT_Made_Easy` has a `theRequest` object (no idea what that is or where it is instantiated). (I think it's an instance of `OTAPI_Func`).
2. On that object `SendRequest` is called, which is defined in `OT_API`.
3. `SendRequest` calls `SendRequestOnce`.
4. `SendRequestOnce` calls `SendRequestLowLevel`.
5. `theRequest` is being passed around in steps 2-4 and eventually `Run` is called on it.
6. Based on whatever type the `theRequest` has a function in `OTAPI_Wrap` is called. For example `OTAPI_Wrap::createAssetAccount`.
7. All the while a couple of magically defined variables is accessable and passed as arguments. Eg: `serverID, nymID, assetID`.
8. In the createAssetAccount method `theMessage` is constructed, fields are being populated (like `m_strCommand` and `m_strAssetID`).
9. `theMessage` is saved (to disk?) with `SaveContract`.
10. `OTAPI_Wrap` delegates to `API_Exec`.
11. Which delegates to `OT_API`.
12. Goes to `ProcessMessageOut` in `OTClient` which stringyfies `theMessage` (In what order? That seems to matter a few steps down.).
  1. Stringyfying goes but making a new OTString object and passing `theMessage` (which is an OTMessage, which is a subclass of OTContract) to the constructor.
  2. The constructor calls `SaveContractRaw` with the message. And does some casting dance.
  3. Which calls `Get` on the `m_strRawfile` (not sure how that got populated or why this is a file all of the sudden).
  4. This just casts to a char pointer (from already a char pointer?).
13. Which then delegates to `ProcessMessageOut` in `OTServerConnection`.
14. The first character in the character buffer which was once `theMessage` determines how with function is passed into ProcessUserCommand, which populates a new `theMessage`.
  1. The buffer seems really small. Only a [couple of characters](https://github.com/Open-Transactions/opentxs/blob/a29d030669b87308509ee0c29a6016f39c7fa6e0/src/client/OTServerConnection.cpp#L833) long.
15. Flow is then directed to yet an other `ProcessMessageOut` this time _only_ accepting an `theMessage`.
16. `theEnvelope` is filled with the content of `theMessage` with the `SaveContractRaw` method.
17. A header is constructed.
  1. The header contains how long a message is and what the type of the message is.
  2. Type of the message is defined by two integers. Presumably the server knows.
  3. I don't think the header is actually used.
18. The member field containing `Callback` is called with `ServerContract` and `theEnvelope`.
  1. `ServerContract` is a member field of the `OTServerConnection` class.
  2. Presumably this sends the message.
19. `Callback` is set in the `SetFocus` method and is of type `TransportCallback`.
20. `SetFocus` is called in `OTClient` in `SetFocusToServerAndNym`.
21. Eventually it boils down to the `GetTransportCallback` method in `OpenTransactions.cpp`, which is a glorified getter.
22. The corresponding setter `SetTransportCallback` is called in `OTAPI_Exec`.
23. This is fed a new instance of `TransportCallback` in which the only constructor argument is a new instance of `OT_API` (defined in `OpenTransactions.cpp`) constructed without any arguments.
  1. `TransportCallback` is just a wrapper, doing nothing really.
  2. It does define an `operator` function which takes a server contract and envelop.
24. I believe the `operator` function is called at some point. This delegates to `TransportFunction` on `OT_API`.
25. After a whole bunch of checks the envelope is armoured and the server address is cobbled together.
26. These two are then passed to the `Send` method of the `OTSocket` class.
27. Which really is implemented in the `OTSocket_ZMQ_4` class.
28. After again a bunch of checks it _finally_ [creates](https://github.com/Open-Transactions/opentxs/blob/aea45331aa5b567fdee8500f90629e1a0046be8e/src/ext/Socket_ZMQ4.cpp#L422) a ZMQ message.
