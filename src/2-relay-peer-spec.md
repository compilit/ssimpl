# Relay Peer Specification

The Relay Peer is a special kind of Peer in the sense that it is static and reachable over the internet on a fixed
hostname. It acts like any other Peer, but it also adds a storage layer for the Ledger and serves as distribution point
for the Ledger. All Relay Peers together form a subnetwork on which all other (mobile) Peers 'ride'. This is purely
infrastructural to help communication between Peers. It doesn't give any special privileges or authorities to any of
the Relay Peers and does therefor still fulfill all requirements to be called decentralized.

### Relay Peer Responsibilities

* Receive, validate and store new LedgerEntries.
* Receive, validate and store new LedgerEntryRevocations.
* Providing and storing a list of known Relay Peers for discovery.
* Responding to registration attempts from new Relay Peers.
* Gossiping about newly registered Relay Peers.

## Relay Peer Bootstrap and Verification

To enable deterministic network formation and secure onboarding of Relay Peers, the SSIMPL protocol defines a bootstrap
and verification procedure.

The first time a new Relay Peer is bootstrapped, it MUST make itself known to a known (or a well-known Relay Peer,
e.g., `root.ssimpl.org`) to
obtain the first list of known Relay Peers. This is a hard-coded node that allows the network to kickstart itself and to
have zero knowledge upfront when adding a new Relay Peer.

### Relay Peer Registration

Upon first contact with the network, a new Relay Peer:

1. Contacts the Root Relay Peer (or another known Relay Peer) to announce its presence.
2. Provides its signed DID using the canonical **SignatureEnvelope** format.
3. Receives information about other Relay Peers in the network for further connections.
4. Requests delta's from one (or more) of the Relay Peers in the network in chunks based on the time between two epochs.
   Starting with 0 (Unix Time) if it has never been online and otherwise starting with the epoch it was last online.
