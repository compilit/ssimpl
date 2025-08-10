# WebRTC

**WebRTC (Web Real-Time Communication)** is an open-source project and API standard that enables **peer-to-peer communication** directly between browsers or mobile apps, without requiring intermediate servers for media routing.

## Key Features

- **Real-time audio and video** streaming.
- **Data channels** for arbitrary peer-to-peer data transfer.
- **Low latency**, ideal for voice/video calls, gaming, and file sharing.

## How It Works

1. **Signaling (out of scope for the WebRTC-spec):** Exchanging connection info (e.g., IPs, codecs) between peers using a separate method (like WebSocket).
2. **ICE (Interactive Connectivity Establishment):** Finds the best path through NATs/firewalls.
3. **DTLS/SRTP:** Ensures **encryption** and **secure transport**.
4. **STUN/TURN servers:** Help peers connect when direct connections are blocked.

## Common Use Cases

- Video conferencing
- Voice calls
- Screen sharing
- Real-time multiplayer games
- Decentralized applications

WebRTC is supported by all major browsers and is a core building block for modern real-time web applications.