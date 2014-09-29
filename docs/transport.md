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
14. The first character in the character buffer which was once `theMessage` determines how `theCMD`, which presumably goes to the server, is constructed.
15. 
