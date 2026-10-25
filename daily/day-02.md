# Day 02 – Networking II: Transport Layer, Encapsulation, DNS, HTTP

**Date:** 2026-10-12 · **Status:** ✅ Done

## Sources
- Practical Networking – *Networking Fundamentals*, OSI Model – A Practical Perspective (Part 2)
- Practical Networking – *Networking Fundamentals*, Network Protocols (ARP, FTP, SMTP, HTTP, SSL, TLS, HTTPS, DNS, DHCP)
- Practical Networking – HTTP vs HTML: Unveiling Network Protocols using Telnet
- TryHackMe – DNS in Detail
- TryHackMe – HTTP in Detail

## What I learned

### Layer 4 – Transport
- Layer 4 delivers data **service to service**: it decides **which program/service** on a host receives the data. This is done with **port numbers**.
- On top of the MAC (L2) and IP (L3) addresses, every connection also needs a **port number**.
- **Server side:** listens on a **fixed, well-known port** so clients know where to connect (e.g., HTTPS `443`, DNS `53`, SSH `22`).
- **Client side:** the client's OS picks a **random temporary port** (**ephemeral port**) for each connection. When the reply comes back, this number tells the OS which app/tab it belongs to, so different conversations never mix.
  *Example from Day 01:* my Mac used port `59776` to talk to Wikipedia on port `443`.

| | TCP | UDP |
| --- | --- | --- |
| Handshake | Yes (SYN → SYN-ACK → ACK) | No |
| Reliability | Resends lost data, keeps order | Send and forget |
| Speed | Slower | Faster |
| Examples | HTTPS (443), SSH (22) | DNS (53), voice/video calls |

### Layers 5, 6, 7
- Unlike layers 1–4, the top three layers are **not sharply separated** in practice, so they are usually studied together.
- The **TCP/IP model** merges them into a single **Application layer**.

### Encapsulation
Each layer wraps the data from the layer above with its own **header** (a label with the information that layer needs):

| Layer | Added | Name of the unit |
| --- | --- | --- |
| L7–L5 (Application) | – | **Data** |
| L4 (Transport) | Source & destination **ports** | **Segment** (TCP) / Datagram (UDP) |
| L3 (Network) | Source & destination **IP addresses** | **Packet** |
| L2 (Data Link) | Source & destination **MAC addresses** | **Frame** |
| L1 (Physical) | – | **Bits** (1s and 0s on the wire / radio) |

- The receiver unwraps it in reverse order: **decapsulation**.
- This is exactly what Wireshark's detail pane shows: `Frame → Ethernet II → Internet Protocol → TCP/UDP`.

### Network Protocols
- A **protocol** is a set of agreed rules for one job. Different jobs use different protocols.
- A **server** is still a host: it is a host running **server software** (e.g., install FTP server software → it becomes an FTP server).
- Each protocol listens on a **well-known port**:

| Protocol | Job | Port |
| --- | --- | --- |
| FTP | File transfer | 21 |
| SSH | Encrypted remote access | 22 |
| SMTP | Sending email | 25 |
| DNS | Domain name → IP address | 53 |
| DHCP | Automatically hands out network settings | 67 / 68 |
| HTTP / HTTPS | Web (plain / encrypted with TLS) | 80 / 443 |

- **ARP:** finds the **MAC address of an IP** on the local network ("Who has 192.168.1.1? Tell me your MAC").
- **DNS:** translates **domain names into IP addresses** (the Internet's phone book).
- **DHCP:** when a host joins a network, DHCP gives it everything it needs: **IP address, subnet mask, default gateway, DNS server**.
  - Settings are **leased**, not permanent; the lease is renewed or the IP is reused by another device later.
  - See it on macOS: `ipconfig getpacket en0` → `yiaddr` (my IP), `subnet_mask`, `router`, `domain_name_server`, `lease_time`.

### Subnet Mask
- An IP address has two parts: the **network part** (which neighborhood) and the **host part** (which house).
- The **subnet mask** marks which part is which.

```
IP:          192.168.168.240
Subnet mask: 255.255.255.0   (= /24)
Network:     192.168.168.x   → everyone on this network starts with 192.168.168.
Host:        .240            → my device
```

- With `/24`, hosts can be `.1`–`.254` → **254 devices** (`.0` = the network itself, `.255` = broadcast).
- **CIDR notation:** `/24` = 24 bits of 1s in the mask (`255.255.255.0`). `/16` = `255.255.0.0` (~65,000 hosts), e.g., `10.25.0.0/16` for the Tokyo office example.
- **Why it matters:** the host uses the mask to decide **"same network → send directly"** or **"different network → send to the gateway"**.
  - `192.168.168.15` → same network → direct.
  - `8.8.8.8` → different network → gateway.

### HTTP vs HTML
- **HTTP** = the **protocol** (the rules for how browser and server talk). **HTML** = the **content** (the page's code). HTTP is the courier, HTML is the item in the box.
- Flow: browser sends an **HTTP GET** request → server replies with a **status code + HTML** → browser **renders** the HTML into the page I see.

- Methods, status codes and headers in detail → see **HTTP in Detail** below.
- See a raw HTTP exchange on macOS: `curl -v http://example.com` (`>` = my request, `<` = server response).

### Telnet
- An old protocol for **remote command-line access** to another machine, on **port 23**.
- In the video it was used as a raw TCP client to type an HTTP request by hand, proving that **HTTP is plain text**.
- **Sends everything unencrypted**, including usernames and passwords → replaced by **SSH (port 22)**.

### DNS in Detail
**Domain structure – read right to left:**
```
www . sdu . edu . tr
 │     │     │     └─ ccTLD (Türkiye)
 │     │     └─────── second-level: education institutions under .tr
 │     └───────────── Süleyman Demirel University
 └─────────────────── subdomain
```
- **TLD** (Top-Level Domain) = the last part. Two kinds:
  - **gTLD** (generic): `.com`, `.org`, `.edu`, `.net`
  - **ccTLD** (country code): `.tr`, `.de`, `.uk`
- **Subdomain ≠ subpage:**
  - Subpage = a **path** on the same site: `sdu.edu.tr/about`
  - Subdomain = a separate name **to the left** of the domain: `mail.google.com`, `drive.google.com`. Each can point to a **completely different server**.

**DNS record types** – each record answers a different question about a domain:

| Record | Question it answers | Example |
| --- | --- | --- |
| **A** | What is this name's **IPv4** address? | `example.com → 93.184.215.14` |
| **AAAA** | What is this name's **IPv6** address? | `example.com → 2606:2800:…` |
| **CNAME** | Is this name an **alias for another name**? | `www.github.com → github.com` |
| **MX** | Which server **receives email** for this domain? | `gmail.com → gmail-smtp-in.l.google.com` |
| **TXT** | Free-text information | Verification codes, email security rules (SPF, DKIM, DMARC) |

- **CNAME** gives no IP; it points to **another name**, whose A record is then looked up. Useful when a service is hosted elsewhere (e.g., a shop subdomain pointing to Shopify): if the provider's IPs change, nothing needs updating.
- **MX** records have a **priority number**: lower = preferred; higher ones are backups.
- Query records on macOS with `dig`: `dig gmail.com MX +short`

### HTTP in Detail
**Every HTTP message has the same structure:**
1. **Start line** – what I want / what happened
2. **Headers** – extra info as `Name: Value`
3. **Blank line** – "headers are over"
4. **Body** – the actual content (optional)

```
REQUEST (client → server)                RESPONSE (server → client)
GET / HTTP/1.1                           HTTP/1.1 200 OK
Host: tryhackme.com                      Server: nginx/1.15.8
User-Agent: Mozilla/5.0 Firefox/87.0     Content-Type: text/html
Referer: https://tryhackme.com/          Content-Length: 98
                                         
                                         <html>...</html>
```

| Header | Direction | Meaning |
| --- | --- | --- |
| `Host` | Request | Which website I want (one IP can host many sites) |
| `User-Agent` | Request | My browser / tool and version |
| `Referer` | Request | The page that linked me here |
| `Cookie` | Request | "Here is my ticket" |
| `Server` | Response | Web server software and version |
| `Content-Type` | Both | What kind of data is in the body (HTML, image, form data) |
| `Content-Length` | Both | Body size in bytes, so the receiver knows nothing is missing |
| `Set-Cookie` | Response | "Store this ticket" |

**Methods**

| Method | Action | Repeat 3× = ? |
| --- | --- | --- |
| **GET** | Read something | Nothing changes |
| **POST** | **Create** something new (form, upload) | 3 new records (not idempotent) |
| **PUT** | **Update/replace** a specific resource (`/users/42`) | Same result as once (**idempotent**) |
| **DELETE** | Delete a resource | Same result as once |

**Status codes – the first digit is the family**

| Family | Meaning | Common codes |
| --- | --- | --- |
| **1xx** | Info | 100 Continue |
| **2xx** | Success | **200** OK · **201** Created |
| **3xx** | Redirect | **301** Moved permanently · **302** Temporary redirect |
| **4xx** | **Client** error | **400** Bad request · **401** Not logged in · **403** Forbidden · **404** Not found · **405** Method not allowed |
| **5xx** | **Server** error | **500** Internal error · **503** Service unavailable |

- **401 vs 403:** 401 = "I don't know who you are, log in." 403 = "I know who you are, but you're not allowed."

### Cookies
- HTTP is **stateless**: the server treats every request as brand new and remembers nothing.
- **Cookies** add memory. *Analogy:* a cloakroom ticket. The attendant doesn't recognize you; they just check your ticket.

| Step | What happens | Key line |
| --- | --- | --- |
| 1 | Browser requests the page | `GET /` |
| 2 | Server sends a form asking for a name | `200 OK` + HTML |
| 3 | User submits the form; data travels in the **body** | `POST /` → body `name=adam` (`Content-Length: 9`) |
| 4 | Server tells the browser to store it | **`Set-Cookie: name=adam`** |
| 5 | Browser sends it back on **every** later request | **`Cookie: name=adam`** |
| 6 | Server recognizes the user | `Welcome back adam` |

- Real sites store a random **session ID** (`session=8f3a9c2e…`), not a name. Whoever holds it **is logged in as you**.
- **Session hijacking** = stealing that cookie → access without the password.
- Protection flags: **`Secure`** (only sent over HTTPS) and **`HttpOnly`** (JavaScript can't read it).

### Ports to memorize
| Port | Protocol | Service | SOC note |
| --- | --- | --- | --- |
| 21 | TCP | FTP | Clear-text file transfer; credentials visible on the wire |
| 22 | TCP | SSH | Encrypted remote shell; brute-force target |
| 23 | TCP | Telnet | Clear-text remote shell; should not exist on a modern network |
| 25 | TCP | SMTP | Mail sending; spam/phishing relays |
| 53 | UDP/TCP | DNS | Name resolution; DNS tunneling abuses it |
| 67/68 | UDP | DHCP | Server / client sides of address leasing |
| 80 | TCP | HTTP | Clear-text web |
| 110 | TCP | POP3 | Mail download (old, clear-text) |
| 135 | TCP | MS RPC | Windows remote procedure calls; lateral movement |
| 139 | TCP | NetBIOS | Legacy Windows file sharing |
| 143 | TCP | IMAP | Mail access |
| 443 | TCP | HTTPS | Encrypted web |
| 445 | TCP | SMB | Windows file sharing; ransomware & worms (WannaCry) spread here |
| 3389 | TCP | RDP | Remote Desktop; top brute-force and ransomware entry point |

## Hands-on: querying DNS records with `dig`

| Query | Result | What it shows |
| --- | --- | --- |
| `dig sdu.edu.tr A +short` | `193.255.208.208` | My university's web server IPv4 (likely on ULAKNET, Türkiye's academic network) |
| `dig gmail.com MX +short` | priority `5` `gmail-smtp-in.l.google.com` + `10/20/30/40` `alt1–4…` | Main mail server and 4 backups. Order in the output doesn't matter; **lowest priority number wins**. |
| `dig www.github.com CNAME +short` | `github.com.` | `www.github.com` is an **alias** of `github.com` |
| `dig google.com TXT +short` | ~20 records | See below |

**Google's TXT records**
- Many `…-domain-verification=…` entries (Apple, Microsoft, Facebook, DocuSign, Cisco…): services asked Google to **prove domain ownership** by publishing a code in DNS. Only the owner can edit DNS, so this counts as proof.
- **SPF:** `v=spf1 include:_spf.google.com ~all`
  - `v=spf1` → this is an SPF rule
  - `include:_spf.google.com` → servers allowed to send mail as google.com are listed there
  - `~all` → mail from any other server = **soft fail** (treat as suspicious). `-all` would mean **hard fail** (reject).
- `"Z29vZ2xl"` is **Base64** for `google`. Attackers often hide commands with Base64 (e.g., encoded PowerShell).

## Hands-on: reading my DHCP lease (café Wi-Fi)
Command: `ipconfig getpacket en0`

| Field | Value | Meaning |
| --- | --- | --- |
| `dhcp_message_type` | ACK (5) | Last step of the DHCP exchange: "These settings are yours." |
| `subnet_mask` | `255.255.255.0` | `/24` → everyone on the café network is `192.168.168.x` |
| `router` | `192.168.168.1` | **Default gateway** (the Zyxel router seen in Wireshark on Day 01) |
| `domain_name_server` | `193.192.98.8`, `212.154.100.18` | Primary and backup DNS. The first one matches the DNS server in my Day 01 capture. |
| `lease_time` | `0x12c` | Hex → 1×256 + 2×16 + 12 = **300 s = 5 minutes** |
| `server_identifier` | `10.50.48.94` | The **DHCP server**, a different device from the router |

**Observations**
- **5-minute lease:** much shorter than a typical home network (often 24 h). Café clients come and go, so IPs are recycled quickly.
- **DHCP server ≠ router:** the server sits on a separate `10.x` network, so the router most likely forwards my request to a central server (**DHCP relay**), the same design used in large corporate networks.

**DHCP in 4 steps (DORA):**
1. **Discover** – client broadcasts: "Is there a DHCP server here?"
2. **Offer** – server: "You can have 192.168.168.240."
3. **Request** – client: "I'll take it."
4. **ACK** – server: "Confirmed, it's yours for 5 minutes."

## SOC takeaway
- A connection is identified by **source IP, source port, destination IP, destination port and protocol** (the *5-tuple*). Firewall and network logs record exactly these fields.
- **Read domains right to left** to spot phishing: in `google.com.account-verify.xyz` the real domain is `account-verify.xyz`; `google.com` is just a fake subdomain.
- **DNS is public OSINT:** with one `dig ... TXT` anyone can see which SaaS tools a company uses (Microsoft 365, DocuSign...) and tailor phishing emails to match.
- **TXT records (SPF, DKIM, DMARC)** are how analysts check whether an email really came from the domain it claims.
- **Telnet traffic on a network is a red flag.** The **Mirai botnet** (2016) took over hundreds of thousands of cameras and routers by logging in over telnet with factory-default passwords. Brute-force attempts on port 23 still show up in firewall logs every day.
- **Web server logs** record method, URL, status code and User-Agent:
  - Hundreds of **404**s from one IP → directory scanning for hidden pages.
  - Many **401**s on a login page followed by one **200** → password guessing that **may have succeeded**. The most dangerous pattern.
  - A burst of **500**s → someone sending malformed input (e.g., SQL injection attempts).
  - User-Agents like `sqlmap`, `nikto`, `python-requests` → automated tools, not humans.
  - Unexpected **PUT/DELETE** from outside → possible attempt to upload a web shell.
- A `Server: nginx/1.15.8` header is **information disclosure**: attackers look up known vulnerabilities for that exact version.
- A stolen **session cookie** = account takeover without a password; cookies over plain HTTP are visible in a packet capture.
- **DHCP logs** answer *"Which device had IP 192.168.1.50 yesterday at 14:00?"* — essential because IPs change over time.
- CIDR ranges like `10.0.0.0/8` appear constantly in firewall rules and SIEM searches to separate internal from external traffic.
- The **destination port** usually tells you the **service**: `22` = SSH, `3389` = RDP, `445` = SMB. A workstation suddenly receiving connections on these ports is worth a look.

## Questions / next
- Wireshark exercise: open `http.cap` from the Wireshark sample captures, filter `http`, use *Follow → TCP Stream* to read a full clear-text request/response (method, Host, User-Agent, status code).
