# DNS Tunneling Lab

## How Attackers Hide Data Inside DNS Traffic

A controlled cybersecurity lab demonstrating how DNS can be abused as a communication channel to carry encoded data inside DNS queries.

This project was created as a practical demonstration using Kali Linux, dnsmasq, dig, Base32 encoding, and Wireshark.

---

## 📌 Overview

The Domain Name System (DNS) is normally used to translate human-readable domain names into IP addresses.

However, DNS can also be abused to carry encoded information through DNS queries or responses. This technique is commonly known as **DNS tunneling**.

In this lab, a harmless test message was encoded using Base32, divided into multiple chunks, and sent as DNS query labels to a locally configured DNS server.

The DNS traffic was captured and analyzed using Wireshark. The encoded chunks were then reassembled and decoded to recover the original message.

The entire demonstration was performed in a controlled local environment using `127.0.0.1`.

---

## 🎯 Objectives

- Understand how DNS tunneling works.
- Understand why DNS can be abused as a communication channel.
- Configure a local DNS server using dnsmasq.
- Encode data using Base32.
- Send encoded data through DNS queries.
- Capture and analyze DNS traffic using Wireshark.
- Reassemble and decode the transmitted data.
- Understand indicators that can help detect DNS tunneling.
- Understand basic prevention and mitigation techniques.

---

## 🛠️ Tools & Technologies

- **Kali Linux**
- **dnsmasq**
- **dig**
- **Base32**
- **Wireshark**
- **Linux Command Line**

---

## 🏗️ Lab Architecture

The lab was created locally on Kali Linux.

~~~text
                    Kali Linux
                        |
                       dig
                   DNS Client
                        |
                    DNS Query
                        |
                        ▼
              +------------------+
              |     dnsmasq      |
              |    Local DNS     |
              |  127.0.0.1:5354 |
              +------------------+
                        |
                   DNS Traffic
                        |
                        ▼
                  Wireshark
                   Analysis
~~~

### Lab Components

- **Kali Linux** — Host system used for the laboratory.
- **dig** — DNS client used to generate DNS queries.
- **dnsmasq** — Lightweight local DNS server.
- **Base32** — Used to encode the demonstration message.
- **Wireshark** — Used to capture and analyze DNS traffic.

---

## ⚙️ Local DNS Server Setup

A local DNS server was configured using `dnsmasq`.

### 1. Install dnsmasq

~~~bash
sudo apt install dnsmasq -y
~~~

### 2. Configure dnsmasq

The following configuration was used for the local laboratory:

~~~text
port=5354
listen-address=127.0.0.1
bind-interfaces
address=/lab.test/127.0.0.1
log-queries
~~~

The DNS server was isolated to the local machine and used port `5354` for the lab.

### 3. Start the DNS Server

~~~bash
sudo dnsmasq --no-daemon --conf-file=$HOME/DNS_Tunnel/dnsmasq.conf
~~~

The `--no-daemon` option keeps dnsmasq running in the foreground so that DNS queries can be observed in the terminal.

### 4. Test the DNS Server

~~~bash
dig @127.0.0.1 -p 5354 test.lab.test
~~~

The local DNS server returned:

~~~text
127.0.0.1
~~~

---

## 🔎 Normal DNS Baseline

Before demonstrating DNS tunneling, normal DNS traffic was captured using Wireshark.

Normal DNS traffic typically contains:

- Domain name to IP address resolution.
- Queries related to legitimate services.
- DNS request and response pairs.

This provides a baseline for comparing normal DNS traffic with the tunneling demonstration.

### Normal DNS Traffic

![Normal DNS](screenshots/01-normal-dns.png)

---

## 🔐 DNS Tunneling Demonstration

The demonstration used the following harmless test message:

~~~text
THIS IS A DNS TUNNEL DEMO
~~~

The message was encoded using Base32 before being placed into DNS query labels.

---

### 1. Base32 Encoding

The test message was encoded using Base32.

~~~text
THIS IS A DNS TUNNEL DEMO
            ↓
          Base32
            ↓
KREESUZAJFJSAQJAIRHFGICUKVHE4RKMEBCEKTKP
~~~

The encoded data was then divided into smaller chunks.

![Base32 Encoding](screenshots/03-base32-encoding.png)

---

### 2. Sending Data Through DNS Queries

The encoded data was divided into multiple chunks and sent as DNS query labels under the controlled `lab.test` domain.

The demonstration used the following chunks:

~~~text
01-KREESUZAJF.lab.test
02-JSAQJAIRHF.lab.test
03-GICUKVHE4R.lab.test
04-KMEBCEKTKP.lab.test
~~~

The queries were generated using `dig` and sent to the local DNS server.

![DNS Query Sending](screenshots/04-dns-query-sending.png)

---

### 3. DNS Server Logs

The local `dnsmasq` server received the DNS queries and logged them.

The server logs show the encoded query labels being received and resolved to the configured local address.

![dnsmasq Server](screenshots/02-dnsmasq-server.png)

---

### 4. Wireshark Capture

Wireshark was used to capture and analyze the DNS traffic generated during the demonstration.

The capture shows normal DNS queries as well as the encoded DNS query labels used in the demonstration.

![Wireshark DNS Capture](screenshots/05-wireshark-capture.png)

---

### 5. Reassembly and Decoding

The encoded chunks were reassembled into the original Base32 string.

The resulting Base32 data was decoded using:

~~~bash
echo "KREESUZAJFJSAQJAIRHFGICUKVHE4RKMEBCEKTKP" | base32 -d
~~~

The original message was successfully recovered:

~~~text
THIS IS A DNS TUNNEL DEMO
~~~

![Decoded Message](screenshots/06-decoded-message.png)

---

## 🔄 Demonstration Workflow

~~~text
Test Message
     |
     ▼
Base32 Encoding
     |
     ▼
Split Into Chunks
     |
     ▼
DNS Queries
     |
     ▼
Local dnsmasq Server
     |
     ▼
Wireshark Capture
     |
     ▼
Reassemble Chunks
     |
     ▼
Base32 Decode
     |
     ▼
Original Message
~~~

---

## 🚨 What Makes DNS Tunneling Suspicious?

One suspicious DNS query does not prove DNS tunneling.

Detection can be based on multiple indicators, including:

- Encoded-looking DNS labels.
- Many unique subdomains.
- Unusually long DNS queries.
- High DNS query frequency.
- Unusual or suspicious domains.
- Suspicious endpoint behavior.

Multiple indicators together can provide stronger evidence of possible DNS tunneling.

---

## 🛡️ How Can IDS/SOC Detect It?

A security monitoring workflow can be:

~~~text
DNS Traffic
     |
     ▼
DNS Monitoring
     |
     ▼
Behavioral Analysis
     |
     ▼
Alert
     |
     ▼
SOC Investigation
~~~

A SOC analyst may examine:

- Query length and frequency.
- Unique or encoded-looking subdomains.
- Domain reputation.
- Source host and process.
- Other endpoint activity.

DNS tunneling detection should rely on behavioral analysis rather than assuming that every encoded-looking DNS query is malicious.

---

## 🔒 Prevention & Mitigation

### 🔒 Control

- Force endpoints through approved DNS resolvers.
- Restrict unauthorized DNS traffic.

### 🔎 Monitor

- Use DNS filtering and behavioral monitoring.
- Integrate DNS monitoring with IDS/IPS and SIEM.

### 🚨 Respond

- Block confirmed malicious domains.
- Investigate suspicious hosts.
- Isolate compromised endpoints when necessary.

---

## 🌍 Real-World Example

### SolarWinds Compromise — SUNBURST

SUNBURST was malware associated with the SolarWinds supply-chain compromise.

DNS was used as part of the malware's communication mechanism, demonstrating how DNS traffic can be incorporated into command-and-control communication.

This example highlights why defenders should monitor DNS behavior rather than treating DNS traffic as automatically trustworthy.

---

## 📊 Results

The controlled lab successfully demonstrated:

- Local DNS server configuration using dnsmasq.
- Base32 encoding of a harmless message.
- Transmission of encoded data through DNS query labels.
- DNS traffic capture using Wireshark.
- Reassembly and decoding of the transmitted data.
- Recovery of the original test message.

---

## 📁 Repository Structure

~~~text
DNS-Tunneling-Lab/
│
├── lab/
│   └── dnsmasq.conf
│
├── screenshots/
│   ├── 01-normal-dns.png
│   ├── 02-dnsmasq-server.png
│   ├── 03-base32-encoding.png
│   ├── 04-dns-query-sending.png
│   ├── 05-wireshark-capture.png
│   └── 06-decoded-message.png
│
└── README.md
~~~

---

## 🔑 Key Takeaways

### 1️⃣ DNS Can Be Abused

DNS can be used as a communication channel for command-and-control or data transfer.

### 2️⃣ Behavior Matters

Encoded-looking labels, unusual subdomains, and abnormal query patterns can indicate possible tunneling.

### 3️⃣ Defense Requires Multiple Layers

DNS monitoring, IDS/IPS, SIEM, and endpoint investigation can work together to detect and respond to suspicious activity.

---

## ⚠️ Disclaimer

This project was created strictly for educational and cybersecurity training purposes.

The demonstration was performed in a controlled local environment using `127.0.0.1`, a locally configured DNS server, and a harmless test message.

It does not demonstrate unauthorized data exfiltration, malware deployment, or communication with external attacker-controlled infrastructure.
