# Wireshark & Network Analysis

Hands-on network traffic analysis using Wireshark, run against a self-created VM to simulate real traffic and capture it firsthand.

| | |
|---|---|
| **Tool** | Wireshark (free, open source) |
| **Environment** | Local machine or Azure VM |
| **Certification alignment** | CompTIA Network+ · Security+ · CySA+ |
| **Time to complete** | 2–4 hours |
| **Cost** | $0 |
| **Career relevance** | Network Engineer · SOC Analyst · Cloud Security Engineer · Incident Responder |

## The Problem This Lab Solves

Networks carry everything an organization produces: emails, login credentials, API calls, database queries. When something breaks or looks suspicious, the network is almost always involved, and the only way to know what's actually happening is to look at the packets.

Wireshark captures the raw data moving across a network interface and lets you inspect it at every layer, from the physical frame up to the application payload. The mental model built here transfers directly to reading Azure Network Watcher logs, VPC flow logs, and any cloud-native traffic analysis tool.

## Core Concepts

**Packet**: a small chunk of data that travels across the network. When you send a text or load a webpage, that data gets broken into many packets, each with its own header (source IP, destination IP, port numbers, protocol flags) and a fragment of the payload (the actual content).

**Protocols**: DNS, HTTP, and TCP each handle a different job. DNS translates domain names into IP addresses (`google.com` becomes `142.250.80.46`) before almost every network connection. HTTP delivers webpage content in cleartext; HTTPS adds TLS encryption on top. ICMP handles ping and diagnostics. TCP ensures packets are delivered reliably and in order.

**TCP three-way handshake**: before two machines exchange data over TCP, they complete a three-step setup. `SYN` means "I want to connect," `SYN-ACK` means "request received, acknowledged," and `ACK` means "connection accepted, ready to send data." A SYN with no SYN-ACK reply means the connection was refused or the host is unreachable, which is one of the fastest ways to tell whether a connectivity problem is client-side, network-side, or server-side.

## What This Lab Covers

- Capturing live network traffic on an active interface
- Applying display filters (`dns`, `tcp`, `tcp.flags.syn == 1`, `ip.addr ==`, `http.request`) to isolate relevant traffic in a noisy capture
- Reading TCP handshakes to distinguish successful vs. failed connections
- Identifying DNS queries and responses
- Spotting cleartext credentials in HTTP traffic
- Following a full TCP stream to reconstruct a conversation

## Exercises

### A. DNS Lookup
Started a capture, ran `nslookup google.com` from a separate terminal to trigger a DNS query on demand, then stopped the capture and filtered on `dns`. Located the query packet ("standard query A google.com") and the response packet, and confirmed the IP address in the response matched what the terminal returned.

**Why it matters:** Unexpected DNS queries to unusual domains are often the first sign of malware calling home. Knowing what normal DNS traffic looks like is step one to spotting abnormal traffic.

### B. TCP Three-Way Handshake
Captured traffic while browsing to `http://example.com`, then filtered on `tcp and ip.addr == [IP]` to isolate the connection. Identified the SYN, SYN-ACK, and ACK packets in sequence.

**Why it matters:** This is the fastest way to diagnose a "can't connect" ticket. A missing SYN-ACK or a RST tells you immediately where the failure is.

### C. Cleartext Credentials over HTTP
*Performed only against a local or permitted test environment.* Submitted a test login over plain HTTP, filtered on `http.request.method == POST`, and inspected the form-encoded data. The username and password were visible in plaintext.

**Why it matters:** This is exactly how security teams demonstrate the real-world risk of skipping HTTPS to developers who push back on adding encryption.

### D. Follow a TCP Stream
Right-clicked an HTTP packet, then chose Follow, then TCP Stream, to reassemble the full client/server conversation from individual packets.

**Why it matters:** This is how incident responders reconstruct what happened during a network event, not from isolated packets, but from the full exchange.

## Captures Included

| File | Shows |
|---|---|
| `dns_lookup.pcapng` | A DNS query for `google.com` and its response |
| `tcp_handshake.pcapng` | A complete SYN, SYN-ACK, ACK sequence |
| `tcp_stream.pcapng` | A reassembled HTTP request/response conversation |

## Key Takeaways

- Packet capture is the ground-truth source for diagnosing connectivity and security issues. Logs and dashboards are summaries; packets are the raw evidence.
- Display filters turn an unusable flood of packets into a targeted investigation.
- The same skills used here (filtering, handshake analysis, stream reconstruction) apply directly to cloud-native traffic logs like Azure Network Watcher and AWS VPC Flow Logs.

## Tools

- [Wireshark](https://www.wireshark.org/): packet capture and analysis
- `nslookup`: manual DNS queries
- `tshark`: command-line packet capture (bundled with Wireshark)
