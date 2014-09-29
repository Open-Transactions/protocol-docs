# Transport layer

1. `OT_Made_Easy` has a `theRequest` object (no idea what that is or where it is instantiated). (I think it's an instance of `OTAPI_Func`.
2. On that object `SendRequest` is called, which is defined in `OT_API`.
3. `SendRequest` calls `SendRequestOnce`.
4. `SendRequestOnce` calls `SendRequestLowLevel`.
5. `theRequest` is being passed around in steps 2-4 and eventually `Run` is called on it.
6. Based on whatever type the `theRequest` has a function in `OTAPI_Wrap` is called. For example `OTAPI_Wrap::createAssetAccount`.
7. All the while a couple of magically defined variables is accessable and passed as arguments. Eg: `serverID, nymID, assetID`.
8. `OTAPI_Wrap` delegates to `API_Exec`.
9. Which delegates to `OT_API`.
10. Goes to `ProcessMessageOut`.
