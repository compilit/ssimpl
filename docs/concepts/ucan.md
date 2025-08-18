# UCAN - User Controlled Authorization Networks

To align with Web3 standards, DoaToa has adopted UCAN. UCAN challenges the traditional client-server model, shifting
control to the client.

Clients generate their own JWTs, specifying the request and resource they intend to access. Each JWT is signed with the
user’s private key, allowing the server to verify the signature and determine whether access should be granted. This
essentially reverses the OAuth 2.0 flow, where clients request a server-issued JWT with specific scopes.

It should be noted that UCAN was originally designed for "delegated authorization". Meaning, an issuer creates a token
that allows the bearer of the token to perform certain actions on certain resources. For authentication, an addition is
required.

## 'UCAN Connect'

Like Open ID Connect adds an identification layer to OAuth2, UCAN Connect also adds an identification layer to UCAN.
By embedding (verified) claims within UCAN JWTs, authentication systems eliminate the need to store user data, further
decentralizing trust. The receiver of the token notifies the issuer of the token which scope(s) they would like to
receive, and the issuer adds the related claims to the token. The receiver also should specify where the JWT should be
sent.

## Implementation of the UCAN Connect flow

So how would all of this work in a real-world example? Let's set up a scenario. We start with three parties:

- Party A. The owner of the identity, aka the issuer of the UCAN token.
- Party B. The id-wallet of the owner (like DoaToa).
- Party C. A website, let's say a webshop called "foo-bar.baz".
- Party D. The server of "foo-bar.baz".

Pre-requisite: both Party A/B, and Party C/D have a DID, signed by DoaToa. For a non-natural person, this means a DID
from someone inside the legal entity, willing to represent the legal entity.

Typically, A will visit C, which at some point requires A to identify themselves (to complete an order, for example).

Party C has to create a scannable image (like a QR-code), which provides Party B all necessary information to
authenticate Party A:

- The DID of Party C.
- The scopes of Party A that Party C requires.
- A connection proposal. Containing either a channel id - used to set up a WebRTC channel, or an authentication
  endpoint (Party D) - to which the token should be sent.

This payload MUST be a multibase-encoded JSON message:

```json
{
  "requestor": "did:key:a1b2c3d4e5f6g7h8ij9k0",
  "scopes": [
    "did",
    "private-address"
  ],
  "destination": {
    "channelId": "someId",
    "authenticationEndpoint": "someEndpoint"
  }
}
```

### Authentication through WebRTC

Party D SHOULD initiate the WebRTC connection using the specified channel id. Party C is also allowed to do so, but that
would expose the UCAN token to Party C, which is less secure. Party B will do so some seconds after
scanning the payload. After the connection is set up, Party B will send the UCAN Connect token. Afterward, the
connection is closed.

### Authentication through an endpoint

The Party D connection endpoint MUST have the same domain as that of Party C. A subdomain is allowed.
The connection endpoint MUST be as follows:

```http request
POST /ucan-connect/auth HTTP/2
Authorization: "Bearer ${theUCANConnectToken}"

(no body should be provided)
```

This endpoint MUST respond with either a 200 OK or a 401 UNAUTHENTICATED

- 200 OK: the tokens' signature matches the provided DID
- 401 UNAUTHENTICATED: the tokens' signature does not match the provided DID. The DID will be in the 'claims' array of
  the UCAN token. Or the DID does not match the specified public key.

Then Party D SHOULD provide Party C with another token, specific for the current context, so Party C doesn't need to
know anything about Party A.

## Delegated signing using UCAN

By creating a dedicated, short-lived and identity-bound token (by setting receivers' DID as the audience of the token),
a user can delegate signing authority towards another
entity, like the website they are currently on. The UCAN token is then embedded in the signature JWT in the prf-array.
This is relevant if the user is adding content to the website and the
website wants to have this content bound to a DID.

The implications are that all content on the internet HAS to be tied to an author, producer, etc. Which means that
fraudulent content, like non-consensual, AI-generated media, will have to signed as well.




