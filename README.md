# Wireshark Traffic Analysis

A hands-on project where I used Wireshark to capture and study real network traffic on Windows — covering the basics of packet capturing, the TCP handshake, DNS lookups, HTTP vs HTTPS, ICMP (ping), FTP, and analyzing traffic from simulated attacks like port scans, brute-force login attempts, and SQL injection.

## Why I built this

I wanted to actually *see* what's happening on the network instead of just reading about protocols — how a connection gets set up, what a DNS lookup looks like on the wire, why HTTPS matters compared to HTTP, and what an attack (like a port scan or brute-force attempt) looks like when you're watching the traffic.

## Environment

- **OS:** Windows
- **Tool:** Wireshark (with Npcap)
- **Internet:** Mobile hotspot (not a LAN/WiFi router)
- **Capture interfaces used:**
  - `Wi-Fi` — for real internet-facing traffic (DNS, HTTP/HTTPS, ICMP, TCP handshake)
  - `Npcap Loopback Adapter` — for local traffic (FTP, attack simulations), since a mobile hotspot isolates connected devices from each other and doesn't allow sniffing other devices' traffic

## Tools used

- Wireshark
- Nmap (reused from my [Nmap-Network-Reconnaissance](https://github.com/Mahhyya/Nmap-Network-Reconnaissance) project)
- FileZilla Server (local FTP server)
- XAMPP + DVWA (local vulnerable web app, reused concepts from my DVWA pentesting project)

## Project structure

| Folder | Topic | What it shows |
|---|---|---|
| `01-packet-capture` | Packet Capture basics | Capture filters vs display filters, saving captures |
| `02-tcp-handshake` | TCP Handshake | 3-way handshake (SYN, SYN-ACK, ACK) and connection teardown (FIN/ACK) |
| `03-dns` | DNS | Query/response, resolved IP, TTL |
| `04-http-https` | HTTP vs HTTPS | Cleartext HTTP request vs encrypted TLS handshake |
| `05-icmp` | ICMP | Ping request/reply, traceroute hops |
| `06-ftp` | FTP | Cleartext username/password capture, file transfer |
| `07-attack-analysis` | Attack Analysis | Port scan pattern, brute-force login pattern, SQL injection request |

Each folder has its own README with the exact commands I ran, the Wireshark filters I used, screenshots, and what I took away from it security-wise.

## How to reproduce this

1. Install Wireshark (includes Npcap).
2. For topics 1–5: connect to the internet (I used a mobile hotspot) and follow the steps in each folder's README.
3. For topic 6: install FileZilla Server locally, set up a test user, and capture on the Npcap Loopback Adapter.
4. For topic 7: install XAMPP + DVWA locally, and capture on the Npcap Loopback Adapter while running Nmap scans and DVWA attack payloads against `127.0.0.1`.

## Key takeaways

- Cleartext protocols (HTTP, FTP) expose credentials and data to anyone who can see the traffic — encrypted versions (HTTPS, FTPS/SFTP) hide the content but the ones I tested still leak metadata like the domain (SNI).
- A mobile hotspot's client isolation means you can't casually sniff other devices' traffic the way you could on an open LAN — I worked around this by generating and capturing my own traffic instead, including a self-contained attack lab over loopback.
- Watching an actual port scan or brute-force attempt in Wireshark makes the "attack pattern" much more concrete than reading about it — repeated SYNs to sequential ports, or repeated POSTs with different passwords, have a clear visual signature.

## Related projects

- [Nmap-Network-Reconnaissance](https://github.com/Mahhyya/Nmap-Network-Reconnaissance) — port scanning and service detection
- DVWA Web App Pentesting — SQLi, brute force, XSS across difficulty levels
- Splunk SIEM project — detecting brute-force patterns from logs
