# Captive Portal Bypass through MAC Randomization (Case Study: AeroMexico-WiFi)

This repository documents an inherent vulnerability in the authentication and authorization mechanisms of captive portals deployed on public Wi-Fi networks. The analysis uses the AeroMexico airline network infrastructure as a case study. The objective of this project is to provide a Proof of Concept (PoC) for strictly educational purposes regarding the shortcomings of access control mechanisms based on Media Access Control (MAC) address filtering.

## Case Study Context

The analyzed network infrastructure corresponds to the **Aeromexico-WiFi** Service Set Identifier (SSID), configured as an open network (without Layer 2 encryption such as WPA2 or WPA3). The implemented topology uses a captive portal that intercepts the initial HTTP traffic and redirects the client to a web authentication form.

The service provisioning model of this portal follows the logic below:

- Assignment of a **15-minute** lease time for Internet access at Layer 3.
- After the lease expires, the system blocks packet forwarding and requires a **$5** payment to restore Wide Area Network (WAN) connectivity.

## The Vulnerability: Session Identity Management Weakness

The vulnerability lies in the methodology used by the access control system to track client identity. Captive portals on free networks typically register the network interface's MAC address to associate it with a session and enforce usage time limits. Once the **15-minute** interval expires, that MAC address is added to a restrictive Access Control List (ACL). This approach incorrectly assumes that the MAC address is a static and immutable identifier of the user within the Wireless Local Area Network (WLAN) environment.

## The Solution: Layer 2 Bypass through MAC Randomization

Since session validation depends exclusively on the physical identifier of the data link layer, bypassing the restriction is achieved by modifying that identifier. Rotating the MAC address using the operating system's native configuration causes the router to identify the device as an entirely new client. After re-establishing the 802.11 association with a new MAC address generated through operating system-level randomization algorithms, the DHCP server and the captive portal controller register a new client. Since the network perceives it as a phone that has just connected for the first time, the portal resets the timer and grants another **15 minutes**.

## Documentation Index

Below is the technical architecture of this vulnerability and the configuration procedures for MAC randomization across different execution environments.

### Technical Foundation

- **The Captive Portal Flaw** (`docs/theory/01-the-captive-portal-flaw.md`): Analysis of the network session lifecycle and the ineffectiveness of MAC filtering.
- **What Is MAC Randomization?** (`docs/theory/02-what-is-mac-randomization.md`): Implementation of Layer 2 privacy protocols and tracking mitigation mechanisms in modern operating systems.

### Implementation Procedures

- Android Configuration (`docs/tutoriales/01-android.en.md`)
- iOS Configuration (`docs/tutoriales/02-ios.en.md`)

## Legal Notice and Disclaimer

The information disclosed in this repository is intended exclusively for educational purposes and cybersecurity research. The author assumes no responsibility for the use of this information to bypass access controls or violate the terms of service of network infrastructure providers.