# DNS Tunneling Lab

## How Attackers Hide Data Inside DNS Traffic

A controlled cybersecurity lab demonstrating how DNS can be abused as a communication channel to carry encoded data inside DNS queries.

This project was created as a practical demonstration using Kali Linux, dnsmasq, dig, Base32 encoding, and Wireshark.

---

## 📌 Overview

The Domain Name System (DNS) is normally used to translate domain names into IP addresses.

However, DNS can also be abused to carry encoded information through DNS queries or responses. This technique is commonly known as **DNS tunneling**.

In this lab, a harmless test message was encoded using Base32, divided into multiple chunks, and sent as DNS query labels to a locally configured DNS server.

The DNS traffic was then captured and analyzed using Wireshark, after which the chunks were reassembled and decoded to recover the original message.

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
- Learn basic prevention and mitigation techniques.

---

## 🛠️ Tools & Technologies

- Kali Linux
- dnsmasq
- dig
- Base32
- Wireshark
- Linux Command Line

---

## 🏗️ Lab Architecture

The lab was created locally on Kali Linux.

```text
                    Kali Linux
                        |
                        |
                      dig
                  DNS Client
                        |
                    DNS Query
                        |
                        ▼
              +------------------+
              |     dnsmasq      |
              |   Local DNS      |
              |  127.0.0.1:5354  |
              +------------------+
                        |
                        |
                 DNS Traffic
                        |
                        ▼
                  Wireshark
                   Analysis





Lab Components
  Kali Linux — Host system used for the laboratory.
  dig — DNS client used to generate DNS queries.
  dnsmasq — Lightweight local DNS server.
  Base32 — Used to encode the demonstration message.
  Wireshark — Used to capture and analyze DNS traffic.




              
