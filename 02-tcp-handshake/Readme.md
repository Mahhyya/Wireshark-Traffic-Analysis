# 02 - TCP Handshake

## Goal
Capture and identify the 3-way handshake and connection teardown.

## Environment
- Interface: `Wi-Fi` (connected via mobile hotspot)
- Traffic generated with: `curl http://neverssl.com`

## Steps performed
1. Started a capture on Wi-Fi and ran `curl http://neverssl.com`.
2. Filtered with `tcp.flags.syn == 1 or tcp.flags.fin == 1` — this showed the SYN and FIN packets, but **not** the final ACK that completes the handshake, since a plain ACK (no SYN/FIN flag) can't be isolated by a flags-only filter — every later data packet also carries just the ACK flag.
3. Fixed this by filtering on the connection instead: `tcp.stream eq N` — showed the full connection in order, with SYN → SYN,ACK → ACK as the first three packets.
4. Used **Follow → TCP Stream** to view the whole exchange in one window.
5. Found the teardown: FIN,ACK from each side, each followed by an ACK.
6. Saved the capture.

## Filters used
```
tcp.flags.syn == 1 or tcp.flags.fin == 1
tcp.stream eq N
```

## Handshake sequence
```
Client                     Server
  |------ SYN ------------->|
  |<---- SYN, ACK -----------|
  |------ ACK -------------->|
     [connection established]
```

## Finding
A flags-only filter can't isolate the handshake-completing ACK, since it's indistinguishable by flags alone from any other ACK later in the conversation. Filtering by `tcp.stream` and reading packet order is the reliable way to isolate a handshake end-to-end.

## Files
- `02-tcp-handshake.pcapng`
- `screenshots/` — SYN, SYN-ACK, ACK, TCP stream follow view, FIN/ACK teardown

## Security / networking takeaway
The handshake establishes a reliable, ordered connection before any data flows — and the same visibility that makes it easy to study here is also what lets an attacker fingerprint open ports (see `07-attack-analysis`).
