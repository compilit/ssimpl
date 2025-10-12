# Concepts

## 1. BIP

BIP stands for Bitcoin Improvement Proposal — it’s a formal design document used to propose changes or additions to
Bitcoin’s protocol, processes, or tools.

It’s essentially the “RFC” (Request for Comments) system for Bitcoin.

Each BIP has:

- A number (e.g., BIP-39)
- A type (Standards Track, Informational, or Process)
- A status (Draft, Proposed, Final, etc.)
- A detailed specification of the proposed change.

For example:

- BIP-32 defines hierarchical deterministic (HD) wallets.
- BIP-39 defines mnemonic phrases (seed words).
- BIP-44 defines a multi-account wallet structure.

While SSIMPL has nothing to do with Bitcoin, or even blockchains, it does rely upon some of the same concepts.

## 1.1 BIP32

BIP32 (Bitcoin Improvement Proposal 32) defines Hierarchical Deterministic (HD) wallets, which allow you to derive a
tree of key pairs from a single root seed.

Key features:

- One backup = all keys: Backing up the seed gives access to all derived keys.
- Key separation: You can derive independent identities (child keys) without compromising the master.
- Public derivation: Given a parent public key, you can derive child public keys (but not private keys).
- Hardened derivation: A safer variant where child keys can’t be derived from public keys, but requires the parent
  private key.

How it works:

You start with a single seed (often from a [BIP39](#12-bip39) mnemonic).
From that seed, it deterministically derives a master keypair (private + public key).
Then you can derive child key pairs from the master, and children of those, forming a tree structure.

## 1.2 BIP39

BIP39 (Bitcoin Improvement Proposal 39) defines a way to represent a deterministic wallet’s private key using a
human-readable set of words, called a mnemonic phrase.

Key properties:

- Mnemonic = backup: If you lose your device, you can recover your entire wallet using the phrase.
- Language-agnostic: Wordlists exist for multiple languages.
- Deterministic: The same phrase will always regenerate the same wallet.

How it works:

First, a cryptographically strong random number (entropy) is generated. Then that number is converted into a sequence of
12–24 words chosen from a predefined list of 2048 words. Finally, this phrase can be used to derive a seed, which in
turn can generate a hierarchy of keys (e.g., via BIP32).

---

# 2. Self-Sovereign-Identity

Self-Sovereign Identity (SSI) is a Decentralised identity model where individuals control their own digital identities
without relying on central authorities.

The main issue which the concept SSI usually tends to ignore is 'truth'. As long an individual is in complete control of
their own digital identity, who is to say this individual is not impersonating some other individual, or worse,
conjuring up a completely new non-existing identity.

Cryptography can verify who issued a claim and whether it was tampered with, but it cannot verify the truthfulness of
the claim itself. The validity of claims like a name, nationality, or birthdate ultimately depends on who the issuer is
and whether they are trusted. However, since this requires authorities to be fully aligned with Web3 standards, we hit a
dead end. Currently, most (if not all) authorities don't have any way to issue a [DID](#3-did). So what DoaToa does, is
reversing
the flow yet again: an individual can create their own DID along with the related cryptographic keys. Which is actually
in line with the entire idea of decentralisation. But 'claims' an individual makes are divided into 2 categories: formal
and informal.

## 2.1 Informal Claims

Informal claims can be made within certain scopes, where the user provides their own data along with their signature. By
nature, these claims cannot be verified by an authority. However, other identities can attest to a claim by signing it,
effectively endorsing its validity. This attestation carries moral responsibility, as the signer’s DID and signature
become publicly associated with the claim.

## 2.2 Formal Claims

Formal claims cannot be self-issued; they must come from a recognized authority. At this point in time, the only formal
claims available to a DoaToa user, are the ones coming from their passport. These can be used to chain other claims of
course. For example: by taking your full name and birthdate, you can match then with the names and birthdate written on
an official
diploma.

---

## 3. DID

Decentralised IDentifier. A DID is a globally unique identifier that is not controlled by a central authority. Unlike
traditional identifiers (usernames, emails, etc.), a DID is linked to a cryptographic keypair, allowing users to prove
ownership without relying on a centralised system.

A DID follows this format:

    did:<method>:<identifier>

In DoaToa, primarily the 'key' method is used. This means that each 'identifier' part is actually the public part of a
specific cryptographic public/private keypair. Each user is responsible for their own private key, which is used to sign
data. The public key can always be used to verify the signature. The only small deviation from the spec is that
SSIMPL promotes the use of [Multibase encoding](#9-multibase-encoding), which means that even the public key part of the
DID is multibase-encoded.

The 'key' method also means there is no real need for a DID document, which is part of the DID spec.

# 4. European e-Passports

A European e-passport relies on a set of international standards to prove its authenticity and integrity using
cryptographic techniques. These standards work together to ensure the data on the passport is both verifiable and
resistant to tampering or cloning.

## 4.1 ICAO Doc 9303

The foundational specification for Machine Readable Travel Documents (MRTDs). It defines:

- The structure and contents of the e-passport chip.
- Cryptographic protocols such as:
    - **Passive Authentication (PA):** Verifies the integrity and authenticity of the stored data using digital
      signatures from the issuing authority.
    - **Active Authentication (AA):** Ensures the chip hasn’t been cloned by using a private key challenge/response
      protocol.

## 4.2  ISO/IEC 7816 (Parts 4, 8, 9)

Standards for electronic identification cards with contacts, adapted for contactless chips in e-passports:

- **Part 4:** File system structure and command set (APDUs).
- **Part 8:** Security-related functions including authentication.
- **Part 9:** Access control and secure messaging.

These standards allow secure and standardized access to the data groups (DG1–DG15) on the chip.

## 4.3 ISO/IEC 14443 (Parts 1–4)

Defines the physical and communication characteristics for contactless smartcards:

- Enables NFC communication between passport chips and readers.
- Standard across all ICAO-compliant e-passports.

## 4.4 ISO/IEC 7501-1

Specifies the physical format and layout of machine-readable passports, including:

- Dimensions
- Data placement
- The Machine Readable Zone (MRZ), which is used to derive keys for secure access (e.g., Basic Access Control).

## 4.5 CSCA and DSC Certificates

Each country operates a **Country Signing Certificate Authority (CSCA)** and issues **Document Signer Certificates (
DSCs)**:

- The CSCA’s public key is distributed globally.
- The DSC signs the data on each passport.
- Verifiers use the CSCA chain to validate the DSC signature and ensure the data’s authenticity.

---

Together, these standards form the cryptographic backbone that allows e-passports to be trusted across borders.

# 5. WebRTC

**WebRTC (Web Real-Time Communication)** is an open-source project and API standard that enables **peer-to-peer
communication** directly between browsers or mobile apps, without requiring intermediate servers for media routing.

Key Features:

- **Real-time audio and video** streaming.
- **Data channels** for arbitrary peer-to-peer data transfer.
- **Low latency**, ideal for voice/video calls, gaming, and file sharing.

How It Works:

1. **Signaling (out of scope for the WebRTC-spec):** Exchanging connection info (e.g., IPs, codecs) between peers using
   a separate method (like WebSocket).
2. **ICE (Interactive Connectivity Establishment):** Finds the best path through NATs/firewalls.
3. **DTLS/SRTP:** Ensures **encryption** and **secure transport**.
4. **STUN/TURN servers:** Help peers connect when direct connections are blocked.

WebRTC is supported by all major browsers and is a core building block for modern real-time web applications.

---

# 6. UCAN - User Controlled Authorization Networks

To align with Web3 standards, DoaToa has adopted UCAN. UCAN challenges the traditional client-server model, shifting
control to the client.

Clients generate their own JWTs, specifying the request and resource they intend to access. Each JWT is signed with the
user’s private key, allowing the server to verify the signature and determine whether access should be granted. This
essentially reverses the OAuth 2.0 flow, where clients request a server-issued JWT with specific scopes.

It should be noted that UCAN was originally designed for "delegated authorization". Meaning, an issuer creates a token
that allows the bearer of the token to perform certain actions on certain resources. For authentication, an addition is
required.

## 6.1 'UCAN Connect'

Like Open ID Connect adds an identification layer to OAuth2, UCAN Connect also adds an identification layer to UCAN.
By embedding (verified) claims within UCAN JWTs, authentication systems eliminate the need to store user data, further
decentralizing trust. The receiver of the token notifies the issuer of the token which scope(s) they would like to
receive, and the issuer adds the related claims to the token. The receiver also should specify where the JWT should be
sent.

## 6.2 Implementation of the UCAN Connect flow

So how would all of this work in a real-world example? Let's set up a scenario. We start with three parties:

- Party A. The owner of the identity, aka the issuer of the UCAN token.
- Party B. The id-wallet of the owner (like DoaToa).
- Party C. A website, let's say a webshop called "foo-bar.baz".
- Party D. The server of "foo-bar.baz".

Pre-requisite: both Party A/B, and Party C/D have a DID, signed by DoaToa. For a non-natural person, this means a DID
from someone inside the legal entity, willing to represent the legal entity.

Typically, A will visit C, which at some point requires A to identify themselves (to complete an order, for example).

Party C has to provide (QR-code, WebRTC, etc.) Party B with all necessary information to authenticate Party A:

- The DID of Party C.
- The scopes of Party A that Party C requires.
- A connection proposal. Containing either a channel id - used to set up a WebRTC channel, or an authentication
  endpoint (Party D) - to which the token should be sent.

This payload MUST be a multibase-encoded message:

(The choice to use JSON is arbitrary)
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

# 6.3. Authentication through WebRTC

Party D SHOULD initiate the WebRTC connection using the specified channel id. Party C is also allowed to do so, but that
would expose the UCAN token to Party C, which is less secure. Party B will do so some seconds after
scanning the payload. After the connection is set up, Party B will send the UCAN Connect token. Afterward, the
connection is closed.

## 6.4 Authentication through an endpoint

The Party D connection endpoint MUST have the same domain as that of Party C. A subdomain is allowed.
The connection endpoint MUST be as follows:

```text
POST /ucan-connect/auth HTTP/2
Authorization: "Bearer ${theUCANConnectToken}"

(no body should be provided)
```

This endpoint MUST respond with either a 200 OK or a 401 UNAUTHENTICATED

- 200 OK: the tokens' signature matches the provided DID & the 'attenuations' array contains a with/can pair matching the current domain and action.
- 401 UNAUTHENTICATED: the tokens' signature does not match the provided DID. The DID will be in the 'claims' array of
  the UCAN token. Or the DID does not match the specified public key.

Then Party D SHOULD provide Party C with another token, specific for the current context, so Party C doesn't need to
know anything about Party A.

## 6.5 Delegated signing using UCAN

By creating a dedicated, short-lived and identity-bound token (by setting receivers' DID as the audience of the token),
a user can delegate signing authority towards another
entity, like the website they are currently on. The UCAN token is then embedded in the signature JWT in the prf-array.
This is relevant if the user is adding content to the website and the
website wants to have this content bound to a DID.

The implications are that all content on the internet HAS to be tied to an author, producer, etc. Which means that
fraudulent content, like non-consensual, AI-generated media, will have to signed as well.

---

# 7. Cryptography

Achieving true decentralization requires users to handle complex cryptographic operations. To make this accessible, all
cryptographic functionality should be encapsulated within a user-friendly interface — a ‘wallet’.

This wallet contains at least one cryptographic keypair:

1. A root AES key for encrypting and decrypting the user’s data.
2. An EC-based public/private key pair for signing and verifying data.

These algorithms are widely recognized standards, so I won’t delve into their inner workings—Wikipedia is your friend
for that.

With these keys, users can encrypt, decrypt, sign, and verify data entirely on the client side, ensuring full control
over their information.

In the subscription system, an additional key pair is introduced. This keypair facilitates sharing a derived
AES key—not the root AES key, but a deterministically generated AES key unique to each subscription.

## 7.1 Post-Quantum disclaimer

With the inevitable advent of quantum computing, these algorithms will change eventually. Currently, there are several
candidates which could potentially take over the aforementioned algorithms. DoaToa will adopt them as soon as they are
ripe.

The biggest concern lies in asymmetric encryption, which is used for signing and sharing of secrets. Through Shor's
algorithm, the asymmetric encryption algorithms we currently rely on will be cracked, meaning that private keys can
be derived based on public keys. This is why everything related to asymmetric encryption should have an expiration
time.

The other concern is in the symmetric encryption, for which exclusively AES is used. DoaToa is already post-quantum
proof in this area, since it used AES-256. Which is overkill in the current day and time, but will be equivalent to
AES-128 when quantum computing becomes more prevalent.


[//]: # (One of the candidates is CRYSTALS &#40;Kyber-512 & Dilithium&#41;)

---

# 8. IPFS

**IPFS (InterPlanetary File System)** is a **peer-to-peer distributed file system** that aims to make the web more *
*Decentralised**, **resilient**, and **permanent**.

Traditionally, large tech companies have been responsible for storing data. This centralised model comes with several
drawbacks, including single points of failure, high costs, and control over access to information.

Decentralised storage offers an alternative by distributing data across multiple nodes instead of relying on a central
server. This approach provides several advantages:

- Eliminates Single Points of Failure – No central server means no single point of failure, reducing the risk of data
  loss or downtime.
- Censorship Resistance – Data isn’t controlled by a single entity, making it harder for governments or organizations to
  censor or remove information.
- Improved Availability – Multiple copies of data are stored across a network, ensuring accessibility even if some nodes
  go offline.
- Lower Costs – Users can share and utilize spare storage from others, reducing infrastructure costs compared to
  traditional cloud providers.
- Tamper Resistance – Cryptographic hashing and content addressing ensure data integrity and prevent unauthorized
  modifications.
- Trustless Environment – No need to rely on a central authority; cryptographic verification ensures data is stored and
  retrieved correctly.

For DoaToa, a peer-to-peer approach aligns best with its principles. The InterPlanetary File System (IPFS) is a
Decentralised, peer-to-peer protocol for storing and sharing data. Unlike traditional location-based addressing (where
URLs point to specific servers), IPFS uses content addressing, identifying files by their cryptographic hash (CID).

## 8.1 Key Features of IPFS:

- Content Addressing – Files are identified by unique hashes, ensuring integrity and deduplication.
- Peer-to-Peer Distribution – Files are retrieved from multiple nodes rather than a central server.
- Versioning – IPFS can track changes, similar to Git.
- Caching – Popular content is cached across the network for faster access.
- Interoperability – Works well with blockchain and Decentralised applications (dApps).

## 8.2 Problems IPFS Solves:

- Redundancy & Availability – Data remains accessible without reliance on a single server.
- Censorship Resistance – Information remains online as long as at least one node hosts it.
- Efficient Content Delivery – Data is retrieved from the nearest available node, reducing latency.

However, IPFS alone does not guarantee permanent storage—data can disappear if no node continues to host it. To ensure
persistence, data must be pinned. This can be done either manually (free) or through a pinning service. Currently,
DoaToa uses a pinning service, but in the future, your wallet will evolve to support self-hosting and pinning, ensuring
full control over data storage

---

# 9. Multibase Encoding

On the internet, a lot of raw data (bytes) is transported using something called base encoding. These encodings
essentially turn the raw bytes into a piece of transportable and near-readable text. But when you wish to access the raw
bytes again on the receiving side, you need to know which encoding was used in order to decode it. But there are a lot
of different encodings to choose from. This means that encoding and decoding must be well-documented. With Multibase
encoding, a special reserved character for each specific encoding is appended to the encoded value which indicates which
encoding was used. This means that anyone receiving the encoded bytes can always decode it. More can be read
<a href="https://w3c-ccg.github.io/multibase/">here</a>.