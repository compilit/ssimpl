# 1. SSIMPL - Specification

The SSIMPL protocol described in the following specification addresses a few problems that have existed since the early
days of the internet — certainly since Web
2.0:

**How do you keep the owner of the online identity in control of their identity data?**

There have been several iterations of OAuth in the past, one of which (Open ID Connect), was meant to add an
identification layer to a protocol that's otherwise solely used for delegated authorization. This identity layer,
however, has never been implemented in a way that anyone can truly rely on. It requires a certain amount of trust, which
is provided in the form of billion-dollar companies saying "this data belongs to the person who just logged in". But it
adds no guarantees about the validity of those claims. In order to minimize the amount of trust necessary for
identification, it is essential that individuals are capable of managing their digital identity themselves, this is
known as [SSI - Self-Sovereign-Identity](./concepts.md#13-self-sovereign-identity). But SSI is still worth as much as an
identity provided by a multinational if it is not verified.

**How do you verify someone’s digital identity?**

Online, everyone can claim to anyone. For some systems - like social media - that could be fine. But for professional
services, it is of the utmost importance that all parties of a transaction are actually who they say they are. This
issue has become even more critical with the rise of AI and bots capable of
impersonating individuals with minimal effort.

SSIMPL stands for Self-Sovereign Identity & Mondial Pseudonymous Ledger. Which tells us already quite a bit about its
components.
SSIMPL tries to find a balance between centralised and decentralised systems. It creates a bridge between the physical
and digital worlds through
authority-backed cryptographic proof.
Therefor, implementing SSIMPL requires a trusted central authority (such as a government) that supports some form of
cryptography-based identification for individuals. Read more
about [centralised authorities in a decentralised system](./concepts.md#16-authorities-in-decentralised-identity-systems).

## 1.1 The wallet

The wallet must be a decentralised [BIP32-compliant](./concepts.md#11-bip32) implementation backed-up up with
a [BIP39-compliant](./concepts.md#12-bip39) mnemonic
phrase (stored offline). This wallet contains both a root keypair that can be used to sign and verify data and to
authorize
the bearer online, and all claims of the owner. It also supports creating child keypairs for pseudonymous
authentication. The root public key is used
to generate a [DID](./concepts.md#111-did) (decentralised Identifier), which is then signed by the neutral third party.
Furthermore,
all encoded data, like the cryptographic keys, must be encoded
using [Multibase-encoding](./concepts.md#113-multibase-encoding) to
ensure all peers of all possible future implementations always know which encoding is used.

At this point, in the spirit of Open ID Connect, the user has all the components needed to authenticate themselves
online at multiple levels:

- Level 0: The DID belongs to a human being.
- Level 1: Level 0 + unverified claims attached to the DID (requested via scope).
- Level 2: Level 1 + verified claims attached to the DID (requested via scope).

Level 0 is sufficient to prove the user is not an AI or bot.
Level 1 may be used when specific, unverified attributes are needed, like an address or a phone number.
Level 2 provides verified claims. The requestor of these claims merely needs to request a certain scope of claims.
You, as the owner then needs to approve this request and send them the requested claims, while signing them with your
wallet.

At the same time, the user will have the ability to cryptographically sign anything they would want to be associated
with. In other words: if they would like to be able to proof they were the original creator of some digital content, all
they would need to do is sign it. Other people could sign it as well, of course, but signature cannot be created using a
past date. So the first one to sign something, always can proof they were the original creator.

## 1.2 Authentication

All users of the SSIMPL protocol must possess a
modern [Standardised e-passport](./concepts.md#17-cryptographic-standards-enabling-european-e-passport-integrity). These
contain cryptographic
material used to verify the legitimacy of the passport, thereby delegating the authority of the identity represented to
the bearer—assuming basic checks are met (e.g., does the bearer match the photo?). This bearer token-like construct can
only be trusted if people take good care of their passports and also follow basic protocols in
case of a lost or stolen passport.

By reading the NFC chip, one will get access to so-called 'data groups'. Each group represents some other part of the
identity or the passport.

While reading the NFC chip, an implementation of SSIMPL is required to perform an Active-Authentication challenge, which
makes the passport sign a randomly generated challenge with its embedded private key, which can then be
verified by the key found in DG15. This challenge is there to proof that the passport was not in fact cloned or
otherwise tempered with. The result of the challenge will be stored in the id wallet.

DG11 contains the personal details of the owner which can be the first basic, verified claims, stored in the id wallet.

## 1.2 WebRTC

In order to be able to establish direct communication between peers through WebRTC, a Signal-, STUN- and TURN-server are
required. These are used for peers to locate each other, after which, further communication is truly p2p.

## 1.3 IPFS

Some details need to be stored in a way that anyone can access them, preferably without a centralised server.
The [IPFS](./concepts.md#112-ipfs)
is a good candidate for this. The IPFS needs to host a file that contains all registered, invalidated/expired DIDs (the
ledger).
Every time another version of this file is uploaded, a pointer-file also needs to be updated. This pointer-file is a
file
containing static data whose metadata is updated to point to the new version of the ledger. To register, one needs to
submit a "root claim", which ties the identity's DID to the document
they used to authenticate themselves. Similarly, this same ledger-entry needs to be updated for invalidations. Each
instance integrating with the IPFS needs to implement these business rules:

1. A user can only append entries if the entry doesn't exist yet, (composite key of DID + identityDocumentHash).
2. A user can only invalidate their own root claim if it exists.
3. An invalidated root claim cannot be altered again.

These rules must be implemented by checking the signature for each entry against the provided DID.

##### rootClaim:

```json
{
  "data": {
    "did": "",
    "identityDocumentHash": "",
    "createdAt": "2025-04-09T10:15:30+07:00.",
    "invalidatedAt": null
  },
  "signature": {
    "publicKey": "string",
    "algorithm": "string",
    "value": "string"
  },
  "signer": "string"
}
```

##### identityDocumentHash:

```js
const identityDocumentHash = sha256(
    JSON.stringify(
        {
            "nationality": "DUTCH",
            "dateOfBirth": "30051988",
            "firstNames": "JOHN HARVEY",
            "lastName": "DOE",
            "documentNumber": "ABC12345XYZ",
            "documentType": "P",
            "documentSubType": "P",
            "documentExpiryDate": "01012050"
        }
    )
)
```

---

# 2. DoaToa - a SSIMPL implementation

DoaToa (Decentralised Open Auth & Trusted Open Auth) is the first SSIMPL implementation created. It consists of a
client-side app (the id-wallet) and a few
stateless microservices necessary to help set up decentralised communication and sharing of data. These microservices,
while being centralised components, are meant to be 'the first of many' open-source interchangeable implementations. The
same goes for the id-wallet. As long as the specification is followed, multiple implementations of both the centralised
components and the wallet, should be able to co-exist.

## 2.1 Decentralisation

In order to fully implement SSI, a user needs to have full control over their own data. There are several ways to
accomplish this (using a hardware wallet for example), but DoaToa relies on a somewhat controversial take: most people
have a smartphone and treat it with more respect than their passport. Smartphones these days lack true HSMs (Hardware
Security Modules), which would be the best place for cryptographic material to exist. Due to this (hopefully temporary)
imperfection in smartphones, DoaToa uses the keystores that exist on devices: Keychain on iOS and the KeyStore
on Android. This introduces a few risks that need to be mitigated:

1. If the device is lost/stolen, the related wallet needs to be invalidated. - This is achieved by registering the DID
   on a decentralised ledger, stored on the IPFS. DoaToa provides this functionality. It would require you use the
   mnemonic phrase on a new device to re-create your wallet, then invalidate it. The user can then create a new wallet
   again.
2. If a user switches devices, the wallet needs to be re-created. - The mnemonic phrase can be used to re-create the old
   wallet. Always do take care to either delete the wallet from the device, or to factory-reset the device.
3. If the mnemonic phrase is lost/stolen. - The user will need to invalidate their current DID
   and create a new wallet (which will also mean the user gets a new mnemonic phrase)
4. A user loses both their mnemonic phrase and their phone. - **This scenario should be prevented at all costs**, since
   everything relies on that
   mnemonic phrase. It is advised to use a (maybe even redundant) cold storage to keep the mnemonic phrase safe.

## 2.2 Use-cases

Each wallet has the capability to share data to another wallet in the form of a data sharing event, or a subscription.

- Ad-hoc data sharing event: the receiver sends a request containing the scope of the requested data, and their
  DID. This request is used to establish a [WebRTC](./concepts.md#18-webrtc)
  connection between the two peers, which is then used to share the data. This scenario is also quite similar to how an
  authentication request could be fulfilled. Although the use of WebRTC for that is optional when backed by a backend
  server. See [UCAN](./concepts.md#19-ucan---user-controlled-authorization-networks).
- Subscriptions: the receiver sends a request containing the scope of the requested data, a public key (used for sharing
  a secret. E.g. Hybrid-Encryption or DHKE), the
  DID of the requestor and some optional metadata concerning the duration of the subscription.This request is used to
  establish a WebRTC. A symmetric key for encryption is either generated or resolved based on the provided
  public key. The data is gathered and encrypted with this symmetric key. In case of Hybrid-Encryption, the symmetric
  key is then encrypted using the public key. The encrypted data (and optionally the encrypted key) are then publicly
  stored on the IPFS.
  Finally, the location to this subscription is shared. While the IPFS does not support updating files, as it would
  require to change the CID used to retrieve the data, DoaToa stores a pointer to the file in question. The file itself
  never changes, but its metadata is updated to point to the latest version of the file thus allowing the file to be
  updated. The only implication is that the latest version of the file needs to be requested through the central DoaToa
  server. A direct WebRTC connection could also be used to update a subscription. However, a WebRTC connection requires
  both sides to actively set it up, which means that syncing between peers becomes occasionally at best when both peers
  happen to be online at the same time.

There are several options for sending the request to the client app. One is through a simple qr-code which is
either used to set up a WebRTC connection. Another could be by providing your e-mail address which you open on your
mobile device containing the wallet, which in term opens a deep link in the app to authentication yourself.

## 2.3 Centralised component

In order to establish WebRTC connections, some centralised components are required for peers to find each other. As soon
as the connection is created, all data will be moved directly from peer to peer. So there is no need to trust the
central server.

In order to store data on the IPFS while leveraging metadata to 'update' files, a central server is also required.

Finally, in order to ensure every SSIMPLE DID out there is actually from the ones they claim to be, DoaToa can be
presented with the cryptographic material coming from your passport along with the DID. If DoaToa can verify this
material, the DID will be signed by DoaToa. Verification of this material goes as follows:

1. The passport contains all the means for an Active Authentication challenge, which needs to be passed. This challenge
   makes sure the cryptographic material actually comes from the passport in the hands of the owner.
2. The passport contains a hash list of all the data on it. This hash list is signed by an authorised party and the
   related public key can be found in the public ICAO master list. DoaToa checks if this is the case and verifies the
   signature.

This limited set of features is provided by DoaToa, but could relatively easily be implemented by other enthusiasts. The
only requirements are that they are open-sourced. Each provider of these features will require a DID as well. You can
request DoaToa to register your DID as a provider.