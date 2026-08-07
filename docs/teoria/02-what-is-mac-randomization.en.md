**Languages:** [Spanish](02-que-es-la-aleatorizacion-mac.md) | [English]

---

# Technical Foundations: MAC Address Randomization

## Introduction to Layer 2 Addressing

In the network architecture defined by the OSI model, the Media Access Control (MAC) address is a 48-bit identifier assigned to network interfaces for communication within the physical network segment (Layer 2). Traditionally, this identifier was programmed into the hardware by the manufacturer and remained static throughout the device's lifetime.

A standard MAC address consists of two parts:

1. **OUI:** The first 24 bits, assigned by the IEEE, which identify the hardware manufacturer.
2. **UAA:** The last 24 bits, assigned sequentially by the manufacturer.

## The Tracking Vector and the Need for Privacy

The static nature of the MAC address introduced a significant privacy vulnerability for users on IEEE 802.11 wireless networks. Because mobile devices continuously transmit probe frames to discover known networks, passive receivers, access points, and data analytics companies could record the static MAC address to profile users' movement patterns, dwell times, and geospatial locations without their consent or network association.

## Randomization Architecture

To mitigate this pervasive tracking, modern operating systems implemented obfuscation mechanisms at the kernel and network supplicant level (for example, `wpa_supplicant`).

The randomization process consists of masking the hardware MAC address by replacing it with a software-generated Locally Administered Address (LAA).

To allow the network infrastructure to recognize that the address is logically generated rather than an original physical address, the operating system modifies a specific bit:

- In the first octet of the generated MAC address, the operating system sets the **second least significant bit to 1**. This formally defines the address as a "Locally Administered" address according to IEEE standards, preventing network collisions with factory-assigned addresses.

## Implementation Modes in Operating Systems

The generation of these logical addresses is generally implemented in two operating modes:

### 1. Persistent Randomization

This is the default behavior in most modern operating systems. The system's cryptographic engine generates a unique MAC address based on the SSID and security credentials while keeping that address consistent every time the device connects to the same network.

- **Purpose:** Prevent tracking across multiple different networks while maintaining session stability within a specific network.

### 2. Non-Persistent Randomization

In this configuration, the operating system generates a new logical MAC address at regular intervals or whenever the wireless interface disassociates from and reassociates with the Access Point, regardless of whether it is the same SSID.

- **Purpose:** Maximum privacy. Prevents the administrator of a specific network from tracking multiple sessions of the same device over time.

## Implications for Captive Portals

The implementation of Non-Persistent Randomization is the technical mechanism that neutralizes Layer 2-based session controls. By forcing a reconnection, the mobile device's networking subsystem presents a completely new LAA to the access point.

As a result, the routing tables, DHCP server, and captive portal controller process the request as a previously unseen hardware node, causing the assignment of a new network lease and the reset of the session counters.