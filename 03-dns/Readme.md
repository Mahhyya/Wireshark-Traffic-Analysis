# 03 - DNS

## Goal
Capture and inspect a DNS query/response, and test the UDP-to-TCP fallback behavior.

## Environment
- Interface: `Wi-Fi` (connected via mobile hotspot)

## Steps performed
1. Flushed the DNS cache (`ipconfig /flushdns`) to force a fresh lookup.
2. Ran `nslookup github.com` while capturing on Wi-Fi.
3. Filtered with `dns` — inspected the query (`github.com, type A`) and the response (resolved IP + TTL).
4. Attempted to trigger a DNS-over-TCP fallback with `nslookup -type=TXT google.com`, filtered `dns && tcp` — got no results.
5. Confirmed the original query/response pair runs over UDP with `dns && udp`.
6. Saved the capture.

## Filters used
```
dns
dns && tcp     (no results — confirms no fallback needed)
dns && udp     (confirms normal DNS uses UDP)
```

## Finding
The TXT query's response fit within UDP's 512-byte limit, so no TCP fallback occurred. This confirms UDP is DNS's default transport, with TCP reserved for responses too large for a single UDP packet (or zone transfers).

## Files
- `03-dns.pcapng`
- `screenshots/` — query packet (Questions section), response packet (Answers section + TTL)

## Security / networking takeaway
DNS responses aren't authenticated by default — anyone on the path could spoof a response before the real one arrives (DNS spoofing/cache poisoning). This is why DNSSEC and encrypted DNS (DoH/DoT) exist.
