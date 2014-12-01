# Transport

The transport layer in OT uses [ZeroMQ](http://zeromq.org) between
the clients and the notary servers. ZeroMQ is not a neutral transport,
because it has its own framing. This means endpoints are enforced to
use compatible ZeroMQ versions.

The ZMQ socket types are REQ-REP (client-server), which means the
client is lock-stepped in communicating with the server. A few important
implications from this:

- the client must always wait a full round-trip, to and back from the
  server, before it can send again,
- if the client doesn't get a response it must destroy and re-create the
  socket.
- the server can never send to the client before getting a request,
  - the server can never heartbeat the clients,
  - the server can never send updates directly to connected clients -
    they have to poll the server.

The transport layer is not secured and everything is sent
in plain text. Each payload is secured using [electronic envelopes]
(http://en.wikipedia.org/wiki/Electronic_envelope).
This means that each payload is encrypted, using an encryption
key that is encrypted to the recipient's public key, and sent in
the envelope as well.

In OT sending works as follows:

- generate a shared secret,
- encrypt the payload with the shared secret using AES128,
- seal the generated shared secret to the recipients' public
  encryption key using RSA, and
- hash the payload and sign using RSA the resulting hash with own
  authentication key.

And receiving works as follows:

- verify the envelope's signature using the sender's public authentication
  key,
- decrypt the shared secret using the recipient's private encryption key, and
- decrypt the payload using the shared secret.

See `Credentials` for a description on keys and how they are used.

TODO: add link

## Used Algorithms

- RSA
  - seal / open
- AES128
  - encrypt / decrypt
- SAMY
  - hashing payload for signing
  - SHA256 XOR WHRLPOOL

## Future Improvements

- A future version of OT will use the secure session transport that
  ZeroMQ provides.
- ECC for sign / verify and seal / open instead of RSA.
- replace SAMY with double SHA256 to prevent *birthday attacks*.
