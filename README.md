# 🔍 Wireshark Traffic Analysis

A hands-on project where I used Wireshark to capture and study real network traffic on Windows — from everyday protocols like DNS and HTTP, to what a TCP handshake actually looks like on the wire, to spotting attack patterns like port scans, brute-force login attempts, and SQL injection.

> **Status:** All 7 topics captured, documented, and screenshotted ✅

---

## 💡 Why I built this

I wanted to actually *see* what's happening on the network instead of just reading about protocols — how a connection gets set up, what a DNS lookup looks like on the wire, why HTTPS matters compared to HTTP, and what an attack looks like when you're watching the traffic happen in real time.

## 🖥️ Environment

| | |
|---|---|
| **OS** | Windows |
| **Tool** | Wireshark (with Npcap) |
| **Internet** | Mobile hotspot (not a LAN/WiFi router) |
| **Capture interfaces** | `Wi-Fi` for internet-facing traffic · `Adapter for loopback traffic capture` for local traffic |

Since a mobile hotspot isolates connected devices from each other (no sniffing other people's traffic like on an open LAN), topics needing an "attacker vs target" setup were built entirely locally — a local FTP server and a local DVWA instance — captured over the loopback adapter instead.

## 🧰 Tools used

- Wireshark + Npcap
- Nmap (reused from [Nmap-Network-Reconnaissance](https://github.com/Mahhyya/Nmap-Network-Reconnaissance))
- FileZilla Server (local FTP server)
- XAMPP + DVWA (local vulnerable web app, ties into my DVWA pentesting project)

---

## 📂 Project structure & key findings

| # | Topic | What it shows | Key finding |
|---|---|---|---|
| 01 | **Packet Capture** | Capture filters vs display filters | Hotspot traffic resolved over IPv6; Wi-Fi IP was a private `10.x.x.x` address from hotspot NAT |
| 02 | **TCP Handshake** | SYN → SYN,ACK → ACK, plus teardown | A flags-only filter can't isolate the final ACK — `tcp.stream` is the reliable way to view a handshake in order |
| 03 | **DNS** | Query/response, TTL, UDP vs TCP | Confirmed DNS defaults to UDP; a TXT query stayed under the 512-byte limit, so no TCP fallback occurred |
| 04 | **HTTP / HTTPS** | Cleartext vs encrypted traffic | HTTP is fully readable; HTTPS (TLS 1.2) still exposes the domain via SNI and the certificate in cleartext, but hides all HTTP content |
| 05 | **ICMP** | Ping and traceroute | Echo Request/Reply matched via Identifier + Sequence Number; traceroute hops observed through carrier NAT |
| 06 | **FTP** | Cleartext credentials | FileZilla Server enforces Implicit TLS by default, blocking plain clients — switching to "Explicit + plain FTP" revealed `USER`/`PASS` fully in cleartext |
| 07 | **Attack Analysis** | Port scan, brute-force, SQLi | A scan run before/after starting Apache showed real-time service state; brute-force attempts showed a clear repeated-POST signature; SQLi payload and dumped data were both fully visible in plaintext |

Each folder above has its own detailed `README.md` with exact commands, filters, and screenshots.

## 🔎 Quick filter reference

```
tcp.flags.syn==1 && tcp.flags.ack==0        → SYN only (scan/handshake start)
tcp.flags.fin==1                             → connection teardown
tcp.stream eq N                              → full conversation, in order
dns                                          → all DNS traffic
http.request or http.response                → HTTP traffic
tls.handshake                                → TLS handshake messages
icmp.type==8 / icmp.type==0                  → ICMP Echo Request / Reply
icmp.type==11                                → ICMP TTL Exceeded (traceroute hops)
ftp / ftp-data                               → FTP control / data channel
http.request.method == "POST"                → form submissions (brute-force/injection)
```

## 🛠️ How to reproduce this

1. Install Wireshark (includes Npcap).
2. **Topics 1–5:** connect to the internet (I used a mobile hotspot) and follow each folder's README.
3. **Topic 6:** install FileZilla Server locally, create a test user, disable Implicit TLS enforcement if it's on by default, capture on the loopback adapter.
4. **Topic 7:** install XAMPP + DVWA locally, capture on the loopback adapter while running Nmap scans and DVWA attack payloads against `127.0.0.1`.

## 🧠 What I took away from this

- **Cleartext protocols leak everything.** HTTP and FTP both exposed full content or credentials the moment I looked — no special tools needed, just a filter.
- **Encryption hides content, not existence.** Even over HTTPS, the domain (SNI) and connection metadata are visible — encryption protects *what* you're saying, not *that* you're saying something to someone.
- **A mobile hotspot changes the whole approach.** Client isolation meant I couldn't sniff other devices — so the "attack lab" had to move onto the same machine, over loopback, which turned into its own useful lesson in network interfaces.
- **Attack traffic has a visual signature.** A port scan, a brute-force attempt, and an SQL injection all look distinctly different from normal traffic once you know what pattern to filter for — this is the same principle IDS/IPS and SIEM tools are built on.

## 🔗 Related projects

- [Nmap-Network-Reconnaissance](https://github.com/Mahhyya/Nmap-Network-Reconnaissance) — port scanning and service detection
- DVWA Web App Pentesting — SQLi, brute force, XSS across difficulty levels
- Splunk SIEM project — detecting brute-force patterns from logs
