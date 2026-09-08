# 05 - ICMP

> Status: not yet performed — fill in the sections below once completed.

## Goal
Capture and inspect ICMP Echo Request/Reply (ping), and optionally observe traceroute hops.

## Environment
- Interface: `Wi-Fi` (connected via mobile hotspot)

## Steps to perform
1. Start a capture on Wi-Fi.
2. Run `ping 8.8.8.8` in a terminal.
3. Stop capture, filter with `icmp`.
4. Identify and screenshot an **Echo Request** (Type 8) and its matching **Echo Reply** (Type 0) — note the `Identifier` and `Sequence Number` fields match between request and reply.
5. Optional: run `tracert 8.8.8.8`, filter `icmp`, look for **Type 11 (Time-to-Live Exceeded)** packets from intermediate hops. Some hops may not resolve due to the mobile hotspot's carrier-grade NAT — document this as an expected limitation, not an error.
6. Save the capture as `05-icmp.pcapng`.

## Filters to use
```
icmp
icmp.type==8    (Echo Request)
icmp.type==0    (Echo Reply)
icmp.type==11   (TTL Exceeded — traceroute hops)
```

## Finding
_(fill in after testing)_

## Files
- `05-icmp.pcapng`
- `screenshots/`

## Security / networking takeaway
ICMP is useful for diagnostics but is also used in reconnaissance (host discovery, OS fingerprinting via TTL) and some denial-of-service techniques (ICMP flood) — many networks rate-limit or block it at the perimeter for this reason.
