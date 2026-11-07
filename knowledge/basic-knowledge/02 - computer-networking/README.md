
# 02 - Computer Networking Basics

## 1. Overview

This lesson introduces the fundamental concepts of computer networking and how devices communicate with each other.

### Learning Objectives
- Understand the purpose and types of computer networks.
- Understand the OSI and TCP/IP models.
- Learn about IP addresses, MAC addresses, and ports.
- Understand TCP and UDP.
- Understand DNS, DHCP, and ARP.
- Explain how data travels when accessing a website.

---

## 2. What is a Computer Network?

A **computer network** is a group of connected devices that communicate and share data or resources.

### Common Types of Networks

| Network | Full Name | Description | Example |
|---|---|---|---|
| PAN | Personal Area Network | A small network around one person. | Bluetooth devices |
| LAN | Local Area Network | A network in a limited area. | Home or school network |
| WLAN | Wireless Local Area Network | A LAN that uses wireless communication. | Wi-Fi network |
| MAN | Metropolitan Area Network | A network covering a city or large campus. | City-wide network |
| WAN | Wide Area Network | A network covering large geographical areas. | The Internet |

### Common Network Devices

| Device | Function |
|---|---|
| Router | Forwards packets between different networks. |
| Switch | Connects devices in a LAN and forwards frames using MAC addresses. |
| Access Point | Allows wireless devices to connect to a network. |
| Modem | Connects a local network to an Internet service provider. |
| Firewall | Allows or blocks network traffic according to security rules. |

---

## 3. OSI Model

The **OSI Model** is a conceptual model that describes network communication in seven layers.

| Layer | Name | Main Function | Example |
|---|---|---|---|
| 7 | Application | Provides network services to applications. | HTTP, DNS, SMTP |
| 6 | Presentation | Handles data representation, encoding, and encryption concepts. | Data formats, encryption |
| 5 | Session | Establishes and manages communication sessions. | Session management |
| 4 | Transport | Provides communication between processes. | TCP, UDP |
| 3 | Network | Handles logical addressing and routing. | IP, routers |
| 2 | Data Link | Transfers frames within a local network. | Ethernet, MAC addresses |
| 1 | Physical | Transmits raw bits through physical media. | Cables, radio signals |

### Data Encapsulation

When data is sent over a network, each layer adds information needed for communication.

**Application Data → Segment/Datagram → Packet → Frame → Bits**

At the receiving device, the process is reversed. This is called **decapsulation**.

---

## 4. TCP/IP Model

The **TCP/IP Model** describes the protocols used in real-world Internet communication.

| Layer | Main Function | Examples |
|---|---|---|
| Application | Provides network services to applications. | HTTP, HTTPS, DNS, DHCP |
| Transport | Handles communication between processes. | TCP, UDP |
| Internet | Handles logical addressing and routing. | IPv4, IPv6, ICMP |
| Network Access | Handles communication over the local network. | Ethernet, Wi-Fi, MAC |

### OSI and TCP/IP Comparison

| OSI Model | TCP/IP Model |
|---|---|
| Application | Application |
| Presentation | Application |
| Session | Application |
| Transport | Transport |
| Network | Internet |
| Data Link | Network Access |
| Physical | Network Access |

---

## 5. IP Address

An **IP address** is a logical address used to identify a network interface and help route packets across networks.

### IPv4

IPv4 uses a 32-bit address, usually written as four decimal numbers.

Example:

`192.168.1.10`

Each number ranges from 0 to 255.

### IPv6

IPv6 uses a 128-bit address and provides a much larger address space.

Example:

`2001:db8::1`

### Public and Private IP Addresses

| Type | Description | Example |
|---|---|---|
| Public IP | Used for communication across the public Internet. | An address assigned by an ISP |
| Private IP | Used inside private networks. | `192.168.1.10` |

Common private IPv4 ranges:

- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

### Subnet Mask and CIDR

A **subnet mask** identifies which part of an IPv4 address represents the network and which part represents the host.

Example:

- IP address: `192.168.1.10`
- Subnet mask: `255.255.255.0`
- CIDR notation: `/24`
- Network address: `192.168.1.0`

---

## 6. MAC Address and ARP

### MAC Address

A **MAC address** is a link-layer address used to identify a network interface within a local network.

Example:

`00:1A:2B:3C:4D:5E`

### ARP — Address Resolution Protocol

ARP is used in IPv4 local networks to discover the MAC address associated with a known IPv4 address.

### Basic ARP Process

1. A device needs to send data to another device on the local network.
2. It checks its ARP cache for the destination MAC address.
3. If the address is unknown, it broadcasts an ARP request.
4. The device with the matching IP address sends an ARP reply.
5. The sender stores the result in its ARP cache.

**Note:** IPv6 uses Neighbor Discovery Protocol (NDP) instead of ARP.

---

## 7. TCP and UDP

TCP and UDP are transport-layer protocols.

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless |
| Reliability | Reliable, ordered delivery | No guarantee of delivery or order |
| Error Recovery | Retransmits lost data | No built-in retransmission |
| Overhead | Higher | Lower |
| Common Uses | HTTPS, SSH, email | DNS, VoIP, streaming, online games |

### TCP Three-Way Handshake

TCP establishes a connection using three main steps:

1. **SYN:** The client requests a connection.
2. **SYN-ACK:** The server acknowledges and responds.
3. **ACK:** The client acknowledges the server's response.

After the handshake, data can be exchanged.

### Common TCP Flags

| Flag | Meaning |
|---|---|
| SYN | Initiates a connection. |
| ACK | Acknowledges received data or a connection message. |
| FIN | Closes a connection gracefully. |
| RST | Resets or immediately terminates a connection. |
| PSH | Requests that data be pushed to the receiving application. |

---

## 8. Ports and Common Protocols

A **port** identifies a specific service or application endpoint on a device.

| Port | Protocol | Common Use |
|---|---|---|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 67/68 | UDP | DHCP |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 123 | UDP | NTP |

**Important:** A port number alone does not prove which application is running. Services can use non-standard ports.

---

## 9. DNS, DHCP, and Default Gateway

### DNS — Domain Name System

DNS translates domain names into IP addresses.

Example:

`www.example.com → IP address`

Without DNS, users would need to remember IP addresses instead of domain names.

### DHCP — Dynamic Host Configuration Protocol

DHCP automatically provides network configuration to devices, such as:

- IP address
- Subnet mask
- Default gateway
- DNS server

### DHCP DORA Process

1. **Discover:** The client searches for a DHCP server.
2. **Offer:** The server offers an IP configuration.
3. **Request:** The client requests the offered configuration.
4. **Acknowledge:** The server confirms the assignment.

### Default Gateway

A **default gateway** is the device that forwards traffic from a local network to other networks.

For example, a home router commonly acts as the default gateway.

---

## 10. What Happens When You Visit a Website?

When you enter `https://www.example.com` in a browser:

1. The device checks whether it already knows the server's IP address.
2. If necessary, DNS resolves the domain name to an IP address.
3. The device determines whether the destination is local or on another network.
4. If the destination is outside the local network, the packet is sent to the default gateway.
5. TCP establishes a connection with the server, usually on port 443.
6. TLS helps establish an encrypted HTTPS connection.
7. The browser sends an HTTP request.
8. The server sends an HTTP response.
9. The browser displays the webpage.

---

## 11. Practical Exercises

### Exercise 1: OSI and TCP/IP

1. Which OSI layer is responsible for routing?
2. Which OSI layer uses TCP and UDP?
3. Which layer is associated with MAC addresses?
4. Which TCP/IP layer includes DNS and HTTP?

### Exercise 2: IP Addressing

Given:

- IP address: `192.168.10.25`
- Subnet mask: `255.255.255.0`

Identify:

1. The network address.
2. The host portion.
3. Whether this is a private or public IPv4 address.
4. The CIDR notation.

### Exercise 3: TCP and UDP

1. What is the main difference between TCP and UDP?
2. What are the three steps of the TCP handshake?
3. Which protocol is generally preferred when reliable, ordered delivery is required?
4. Why might an application choose UDP?

### Exercise 4: DNS, DHCP, and ARP

Explain the purpose of:

1. DNS
2. DHCP
3. ARP

Then briefly describe what happens when a device joins a Wi-Fi network and visits a website for the first time.

### Exercise 5: Ports

Match each port with its common protocol:

| Port | Protocol |
|---|---|
| 22 | ? |
| 53 | ? |
| 80 | ? |
| 443 | ? |
| 67/68 | ? |

---

## 12. Key Takeaways

- Networks allow devices to communicate and share resources.
- The OSI model has seven layers; TCP/IP is a practical model used for Internet communication.
- IP addresses support logical addressing and routing.
- MAC addresses are used for communication within a local network.
- TCP provides reliable, ordered delivery; UDP has lower overhead.
- DNS resolves domain names; DHCP assigns network configuration; ARP resolves local IPv4 addresses to MAC addresses.
- Ports identify application or service endpoints.
- The default gateway forwards traffic to other networks.

---

## 13. References

- [Cloudflare Learning - What is a computer network?](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-network/)
- [Cloudflare Learning - What is TCP/IP?](https://www.cloudflare.com/learning/ddos/glossary/tcp-ip/)
- [Cloudflare Learning - What is DNS?](https://www.cloudflare.com/learning/dns/what-is-dns/)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [RFC 9293 - Transmission Control Protocol (TCP)](https://www.rfc-editor.org/rfc/rfc9293)

---

## 14. Answers and Explanations

### Exercise 1: OSI and TCP/IP

1. **Network Layer (Layer 3):** Responsible for logical addressing and routing packets between networks.
2. **Transport Layer (Layer 4):** Uses TCP and UDP to provide communication between processes.
3. **Data Link Layer (Layer 2):** Uses MAC addresses to deliver frames within a local network.
4. **Application Layer:** The TCP/IP Application layer includes protocols such as DNS and HTTP.

### Exercise 2: IP Addressing

Given:

- IP address: `192.168.10.25`
- Subnet mask: `255.255.255.0`

Answers:

1. **Network address:** `192.168.10.0`
2. **Host portion:** `25`
3. **Address type:** Private IPv4 address
4. **CIDR notation:** `/24`

**Explanation:** The subnet mask `255.255.255.0` means the first 24 bits identify the network, while the remaining 8 bits identify the host. The address `192.168.10.25` belongs to the private range `192.168.0.0/16`.

### Exercise 3: TCP and UDP

1. **Main difference:** TCP provides reliable and ordered data delivery, while UDP does not guarantee delivery or order.
2. **TCP three-way handshake:** `SYN → SYN-ACK → ACK`
3. **Reliable, ordered delivery:** TCP
4. **Why use UDP?** UDP has lower overhead and can be suitable for applications that prioritize low latency, such as voice calls, streaming, and online games.

### Exercise 4: DNS, DHCP, and ARP

1. **DNS:** Translates domain names into IP addresses.
2. **DHCP:** Automatically assigns network configuration, such as an IP address, subnet mask, default gateway, and DNS server.
3. **ARP:** Finds the MAC address associated with a known IPv4 address on the local network.

**When a device joins Wi-Fi and visits a website:**

1. The device connects to the Wi-Fi network.
2. DHCP provides the device with its network configuration.
3. DNS resolves the website's domain name into an IP address.
4. The device determines whether the destination is on the local network.
5. If the destination is on another network, the device sends traffic to its default gateway.
6. ARP may be used to discover the gateway's MAC address.
7. The device establishes a TCP connection and, for HTTPS, a TLS connection.
8. The browser sends an HTTP request and receives the server's response.

### Exercise 5: Ports

| Port | Common Protocol |
|---|---|
| 22 | SSH |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 67/68 | DHCP |

**Explanation:** Port numbers identify service or application endpoints. These are common assignments, but a service can also use a non-standard port



**Category:** Basic Knowledge  
**Topic:** Computer Networking Basics
