# 01 - Packet Capture
 
## Goal
Learn the difference between capture filters and display filters, and get comfortable with the basic Wireshark workflow.
 
## Environment
- Interface: `Wi-Fi` (connected via mobile hotspot)
## Steps performed
1. Captured live traffic on the `Wi-Fi` interface.
2. Tested a **capture filter** (`host 8.8.8.8`) while running `ping 8.8.8.8` — confirmed only traffic to/from that host was captured; everything else was excluded before it even hit the packet list.
3. Tested a **display filter** on a full, unfiltered capture. Since the site resolved over **IPv6**, used `ipv6.addr == <address>` instead of `ip.addr` (which only matches IPv4).
4. Saved the capture.
## Filters used
```
host 8.8.8.8              (capture filter)
ipv6.addr == <address>    (display filter)
```
 
## Finding
My hotspot resolves at least some sites over IPv6 rather than IPv4 — worth checking the address family in the packet list before assuming `ip.addr` will match anything.
 
## Files
- `01-packet-capture.pcapng`
- `screenshots/` — capture filter result, display filter result
## Security / networking takeaway
Capture filters discard non-matching packets before they're ever saved (efficient, but you can't change your mind after capturing). Display filters just hide/show from an already-saved capture (flexible, but you need the full capture available).
