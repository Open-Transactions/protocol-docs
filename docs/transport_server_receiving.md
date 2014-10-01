# Incoming transport layer

This document examines the program flow from the point a message is received till the action is performced.

## Exhaustive trace

1. [(`MessageProcessor::run`,`MessageProcessor`)](https://github.com/Open-Transactions/opentxs/blob/85e265ec79d47badc4ea005cc78d82725becbdcd/src/server/MessageProcessor.cpp#L214) In an infinite loop read from a socket.
2. [(`MessageProcessor::processMessage`,`MessageProcessor`)](https://github.com/Open-Transactions/opentxs/blob/85e265ec79d47badc4ea005cc78d82725becbdcd/src/server/MessageProcessor.cpp#L320) The message is processed.
  1. The base64 message is decoded into binary form (with the method `SetAsciiArmoredData` which doesn't set armored data but accepts it and decodes it).
  2. The envelope is opened/decrypted. This requires a) the server nym and b) the de-armoured contents.
  3. The [`Open`](https://github.com/Open-Transactions/opentxs/blob/85bdcb545969a5024db7656ee6f9c7082eaa925c/src/core/crypto/OTCrypto.cpp#L3007) method does all the unpacking and what not. Pretty scary stuff there.
3. [(`MessageProcessor::processMessage`,`MessageProcessor`)](https://github.com/Open-Transactions/opentxs/blob/85e265ec79d47badc4ea005cc78d82725becbdcd/src/server/MessageProcessor.cpp#L358) A reply is immediately prepared. This is what the '@' comes from!
4. [(`MessageProcessor::processMessage`,`MessageProcessor`)](https://github.com/Open-Transactions/opentxs/blob/85e265ec79d47badc4ea005cc78d82725becbdcd/src/server/MessageProcessor.cpp#L372) The original message, the reply, a socket and a nym are passed to the `ProcessUserCommand` method.
  1. [(`UserCommandProcessor::ProcessUserCommand`, `UserCommandProcessor`)](https://github.com/Open-Transactions/opentxs/blob/85bdcb545969a5024db7656ee6f9c7082eaa925c/src/server/UserCommandProcessor.cpp#L166) The request number is copied from the incoming message to the outgoing message.
  2. Some other validation checks are performed such as checking if the receiving server is the server in the message.
  3. Then the `m_strCommand` field of the incoming message is compared against a whole bunch of strings to determine what the user wants (eg. 'createAccount', which really should be called 'createAssetAccount').
  4. All the command handlers are passed the incoming message and the reply.
5. The reply message is further processed, sealed and send back to the client.
1. [(`Receive`, `Socket_ZMQ4`)](https://github.com/Open-Transactions/opentxs/blob/aea45331aa5b567fdee8500f90629e1a0046be8e/src/ext/Socket_ZMQ4.cpp#L422) A message is received and copied to the passed buffer.
