# 04 - HTTP / HTTPS

## Goal
Compare a cleartext HTTP request against an encrypted HTTPS/TLS session.

## Environment
- Interface: `Wi-Fi` (connected via mobile hotspot)

## Part A — HTTP (cleartext)
1. Visited `http://neverssl.com` while capturing on Wi-Fi.
2. Filtered `http.request or http.response`, used **Follow → HTTP Stream** on the `GET /` request.
3. Result: the full request and response — headers and body — completely readable in plaintext.

## Part B — HTTPS (TLS)
1. Visited an HTTPS site while capturing fresh on Wi-Fi.
2. Filtered `tls.handshake`.
3. **Client Hello** — visible in cleartext, includes the SNI (`server_name` extension) showing the target domain even though the session is about to be encrypted.
4. **Server Hello** — visible in cleartext, includes the chosen cipher suite.
5. **Certificate** — this site negotiated **TLS 1.2** (confirmed via the `supported_versions` extension in the Server Hello), so the certificate is sent in cleartext right after the Server Hello. At first glance the certificate data looked like encrypted binary noise — this is actually just DER encoding, not encryption. Drilling down through `Certificates → Certificate → signedCertificate` exposed the readable fields: `subject`, `issuer`, `validity` (notBefore/notAfter).
6. Re-ran `http.request` on this HTTPS capture — returned **zero results**, directly proving the payload is encrypted and invisible to Wireshark once TLS is active, in contrast to Part A.
7. Saved both captures.

## Filters used
```
http.request or http.response
tls.handshake
http.request     (on the HTTPS capture — returns nothing)
```

## Finding
TLS 1.2 exposes the certificate in cleartext (readable once you expand deep enough into the DER structure), unlike TLS 1.3 where the certificate exchange itself is encrypted. Either way, HTTP content becomes completely invisible to a network observer once TLS is in use — only the SNI domain and cipher suite remain visible.

## Files
- `04a-http.pcapng`
- `04b-https.pcapng`
- `screenshots/` — HTTP stream (plaintext), Client Hello (SNI), Server Hello (cipher suite), Certificate (subject/issuer)

## Security / networking takeaway
HTTP exposes everything — headers, cookies, form data — to anyone on the path. HTTPS hides the content but still leaks the domain being visited via SNI, which is why encrypted SNI (ECH) is an active area of development.
