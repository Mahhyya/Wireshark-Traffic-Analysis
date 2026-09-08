# 06 - FTP

> Status: not yet performed — fill in the sections below once completed.

## Goal
Capture an FTP login and file transfer to show that credentials are sent in cleartext.

## Environment
- Interface: `Npcap Loopback Adapter` (`127.0.0.1`) — used instead of Wi-Fi since the mobile hotspot isolates connected devices from each other, so a local FTP server is used instead.
- Local FTP server: FileZilla Server (or Windows' built-in IIS FTP feature)

## Setup (one-time)
1. Install FileZilla Server, create a test user (e.g. `testuser` / `testpass123`), set a shared folder, enable read/write.
2. Confirm the server is listening on port 21.

## Steps to perform
1. Start a capture on the **Npcap Loopback Adapter**.
2. In a terminal: `ftp 127.0.0.1`
3. Log in with the test credentials, run `dir`, then `get <a test file>`.
4. Stop capture, filter with `ftp`.
5. Screenshot the login sequence — `USER testuser` and `PASS testpass123` should be visible in cleartext.
6. Filter `ftp-data` to show the file transfer packets on the data channel.
7. Save the capture as `06-ftp.pcapng`.

## Filters to use
```
ftp        (control channel — login, commands)
ftp-data   (data channel — actual file transfer)
```

## Finding
_(fill in after testing)_

## Files
- `06-ftp.pcapng`
- `screenshots/` — login (cleartext credentials), file transfer

## Security / networking takeaway
FTP sends usernames and passwords unencrypted — anyone who can see the traffic can capture credentials directly, exactly as demonstrated here. This is why FTPS/SFTP (which wrap the exchange in encryption) are the recommended alternatives.
