# 07 - Attack Analysis

> Status: not yet performed — fill in the sections below once completed.

## Goal
Capture and identify the traffic signatures of a port scan, a brute-force login attempt, and a SQL injection request.

## Environment
- Interface: `Npcap Loopback Adapter` (`127.0.0.1`) — used instead of Wi-Fi for the same reason as FTP: the mobile hotspot isolates connected devices, so the target is a locally hosted vulnerable app instead.
- Target: XAMPP + DVWA running on `127.0.0.1`
- Tools: Nmap (reused from the Nmap-Network-Reconnaissance project), Burp Suite (or a simple script) for brute-force, DVWA's SQLi module

## Setup (one-time)
1. Install XAMPP, start Apache + MySQL.
2. Install DVWA into `htdocs/dvwa`, configure it, create/reset the database, log in.
3. Set DVWA security level to Low for the first pass.

## 7a. Port scan visibility (Nmap)
1. Start a capture on the Npcap Loopback Adapter.
2. Run `nmap -sS -p 1-1000 127.0.0.1` (as Administrator).
3. Stop capture, filter `tcp.flags.syn==1 && tcp.flags.ack==0`.
4. Screenshot the burst of SYN packets — note which ports responded SYN,ACK (open) vs RST,ACK (closed) vs no response (filtered).

## 7b. Brute-force pattern (DVWA login)
1. Start a capture on the Npcap Loopback Adapter.
2. Send repeated POST requests to DVWA's login form with different passwords (Burp Intruder or a simple script).
3. Stop capture, filter `http.request.method == "POST" && http.request.uri contains "login"`.
4. Screenshot the repeated POSTs — same URI, different `password=` values, fired in quick succession.

## 7c. SQL injection request
1. Start a capture on the Npcap Loopback Adapter.
2. Submit a SQLi payload (e.g. `' OR '1'='1`) into DVWA's SQLi module.
3. Stop capture, filter `http.request`.
4. Follow the HTTP stream — screenshot the raw request showing the injected payload and the response showing the dumped data.

## Filters used
```
tcp.flags.syn==1 && tcp.flags.ack==0
http.request.method == "POST" && http.request.uri contains "login"
http.request
```

## Findings
_(fill in after testing — port scan pattern, brute-force pattern, SQLi request/response)_

## Files
- `07a-port-scan.pcapng`
- `07b-brute-force.pcapng`
- `07c-sql-injection.pcapng`
- `screenshots/`

## Security / networking takeaway
Each of these attacks has a distinct, visible pattern in raw traffic — repeated SYNs to sequential ports, repeated POSTs with varying credentials, or an obvious injected payload in a request. This is the basis for how IDS/IPS and SIEM tools (like Splunk, used in another of my projects) detect attacks from network traffic.
