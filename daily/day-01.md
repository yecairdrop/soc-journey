# Day 01 – Networking I: How Networks Work

**Date:** 2026-10-09 · **Status:** 🟡 In progress

## Sources
- Practical Networking – *Networking Fundamentals*, Lesson 1a: Network Devices – Hosts, IP Addresses, Networks
- Practical Networking – *Networking Fundamentals*, Lesson 1b: Hub, Bridge, Switch, Router
- Practical Networking – *Networking Fundamentals*, Lesson 2: OSI Model – A Practical Perspective (Part 1)

## What I learned

### Hosts
- A **host** is any device that **sends or receives traffic**: computers, phones, servers, cloud instances, etc.
- Hosts are the *end points* of communication.

### Client vs. Server
- **Client:** the side that **makes a request** (e.g., my browser asking for a website).
- **Server:** the side that **responds to the request**.
- Client and server are **roles, not device types**: the same machine can be both.
  *Example:* my laptop runs Splunk (server) and I open it from my browser on the same laptop (client).

### IP Addresses
- An **IP address** is a host's **address on a network**, like a postal address. It is used for all network communication, not only the web, and it can change (e.g., moving from home Wi-Fi to a café).
- Every packet carries a **source IP** and a **destination IP**.
- An **IPv4** address is **32 bits**, split into **4 octets** (8 bits each), written in decimal: `192.168.1.10`.
- **IPv6** addresses are 128 bits (to be covered later).

### IP Hierarchy & Subnetting
- IP addresses are assigned **hierarchically**, which is the idea behind **subnetting**.
  *Example:* Company X uses `10.x.x.x` → Tokyo office `10.25.x.x`, London office `10.22.x.x`.
- `10.0.0.0/8` is a **private IP range**, used inside company networks (not reachable directly from the Internet).

### Network Devices
| Device | What it does | Key limitation / note |
| --- | --- | --- |
| **Repeater** | Regenerates the signal so data can travel **longer distances** without degrading. | Only 2 ports; no intelligence. |
| **Hub** | Connects many hosts in one place instead of wiring every host to every other host. | A "multi-port repeater": sends **every** packet to **all** connected hosts. |
| **Bridge** | Sits **between two hub segments** and learns which hosts are on which side. | Forwards traffic to the other side **only when needed**, cutting unnecessary traffic. |
| **Switch** | Hub + bridge combined (a multi-port bridge). Sends data **only to the destination host**. | Identifies hosts by **MAC address** (Layer 2). Hosts connected through a switch form **one network** and share the same network prefix. |
| **Router** | Connects **different networks** to each other. | Has an IP address in each network it connects; uses a **routing table** to decide where packets go. |

### Default Gateway
- The **default gateway** is a network's **exit door**: the router's IP address on my local network.
- If the destination is **in my network** → the host sends directly (no router needed).
- If the destination is **outside my network** (e.g., google.com) → the host sends the packet to the **gateway**, and the router forwards it onward.
- *Example:* my Mac `192.168.1.34` → gateway (home router) `192.168.1.1` → Internet.
- Check it on macOS: `route -n get default | grep gateway`

### Ping & ICMP
- **Ping** checks whether a **specific host is reachable** by sending **ICMP Echo Request** packets and waiting for **ICMP Echo Reply** packets.
- It also shows **latency** (round-trip time, in ms) and **packet loss**.
- **ICMP** is a **Layer 3** protocol: it has **no ports** (unlike TCP/UDP).
- **No reply ≠ host is down**: many firewalls and servers block ICMP on purpose.
- Try it on macOS: `ping -c 4 8.8.8.8` (sends 4 packets to Google's DNS server). In Wireshark, filter with `icmp`.

### OSI Model (Layers 1–3)
- The **OSI model** is a **conceptual model** that splits network communication into **7 layers**. The actual rules at each layer are **protocols** (Ethernet, IP, TCP...).
- The real Internet runs on the simpler **TCP/IP model**; OSI is the common language engineers use to describe and troubleshoot networks.

| Layer | Name | Job | Address / Examples |
| --- | --- | --- | --- |
| 1 | **Physical** | Moves raw **bits (1s and 0s)** between devices. | Ethernet cable, Wi-Fi radio, fiber |
| 2 | **Data Link** | Delivers data **hop to hop** (from one device to the next one). | **MAC address**; switches, network cards (NIC) |
| 3 | **Network** | Delivers data **end to end** (from the source host to the final destination host). | **IP address**; routers |

### MAC Addresses
- **48 bits**, written as **12 hexadecimal (base-16) characters**.
- Same number, different notation per system: macOS `a4:83:e7:2b:1c:9f` · Windows `A4-83-E7-2B-1C-9F` · Cisco `a483.e72b.1c9f`
- Burned into the network card at the factory; used for communication **inside the local network**.

### Why do we need both MAC and IP?
- **IP = final destination** (like the address label on a parcel): stays the **same for the whole trip**.
- **MAC = next hop** (like the hand-off slip at each courier depot): is **rewritten at every router**.
- MAC addresses are **flat** (no hierarchy), so routers cannot route billions of them. IP addresses are **hierarchical** (e.g., "everything starting with `10.25` goes to Tokyo"), so one routing rule can cover millions of hosts.
- **In short: IP tells where to go, MAC tells the next step.**

| Packet from my Mac to Google | Source | Destination | Changes on the way? |
| --- | --- | --- | --- |
| IP (Layer 3) | My IP | Google's IP | No |
| MAC (Layer 2) | My MAC | My gateway's MAC | Yes, at every router |

- See it on macOS: `arp -a` → shows the gateway's IP and its MAC address.

## Hands-on: Wireshark on café Wi-Fi (my own traffic only)
Captured my own traffic on `en0`, generated some with `ping -c 4 8.8.8.8` and by opening Wikipedia.

| Item | Value | Meaning |
| --- | --- | --- |
| My IP | `192.168.168.240` | Private IP on the café network |
| My MAC | `c2:07:…` (redacted) | macOS **Private Wi-Fi Address** (randomized MAC) |
| Gateway MAC | `Zyxel…:dd:cb:f0` | Café router; Wireshark resolves the vendor from the first 3 bytes |
| DNS server | `193.192.98.8` | ISP's DNS server |
| Wikipedia | `185.15.58.224` | Wikimedia IP range |

**Filter `icmp`:** 4 Echo request/reply pairs (`seq=0..3`). Round-trip ≈ **35 ms**.
TTL out = 64 (macOS default), TTL back = 111 → reply likely started at 128 and crossed ~17 routers.
*TTL = a packet's "lives": each router subtracts 1, so lost packets don't loop forever.*

**Filter `dns`:** Queries for Spotify, Google, WhatsApp Web over **UDP port 53**. `A` = "give me the IPv4 address", `HTTPS` = "how should I connect?". Query and response are matched by the same ID (e.g., `0x4bfc`).

**Filter `tcp.flags.syn==1` → TCP 3-way handshake:**

| Step | Packet | Meaning |
| --- | --- | --- |
| 1 | `192.168.168.240:59776 → 185.15.58.224:443` **[SYN]** | "Can you hear me?" (my Seq = 0) |
| 2 | `185.15.58.224:443 → 192.168.168.240:59776` **[SYN, ACK]** | "I hear you, can you hear me?" (Ack = 1) |
| 3 | **[ACK]** | "Yes, let's start." (seen with filter `tcp.port==59776`) |

- **443** = HTTPS server port; **59776** = random **ephemeral** client port, new for every connection.
- Both sides confirm sending *and* receiving work, and agree on **sequence numbers** so lost or out-of-order data can be detected. UDP (DNS) has no handshake.
- Browsers open several connections in parallel (two SYNs to Google at once).

**Proof of today's theory:** in the ping packet, the **destination IP is Google** (`8.8.8.8`) but the **destination MAC is the café router**. IP = final destination, MAC = next hop.

## SOC takeaway
- In every log, the first questions are: **who** (source IP) talked to **whom** (destination IP)?
- Knowing private ranges (like `10.x.x.x`) helps tell **internal** traffic from **external** traffic at a glance.
- **MAC addresses never leave the local network**, so logs from outside only show IPs. To find *which physical device* an internal IP belonged to, an analyst checks local sources like **DHCP logs** or ARP tables.
- **DNS logs** show which domains a machine looked up; malware usually resolves its attacker's server name first.
- One IP sending **SYNs to many ports** with no completed handshakes = **port scan (SYN scan)**.
- Many pings from one host to **many IPs** in a short time = a **ping sweep**, a common reconnaissance step (MITRE ATT&CK **T1018 – Remote System Discovery**).
- Traffic leaving the network always passes through the **gateway/router**, which is why firewalls and network sensors sit there: it is the best place to watch traffic.

## Questions / next
- Day 2: OSI Model Part 2 (Layers 4–7), Network Protocols, HTTP vs HTML.
