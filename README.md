![1](images/networking.foundations.banner.jpg)

## Operational Relevance 

This project demonstrates foundational network administration supporting endpoint connectivity and identity services in a simulated small-office environment. Proficiency with Tier-1 and Tier-2 IT support tasks is demonstrated with screenshots (click the > dropdowns to view). Troubleshooting activities are documented in a separate repository modeled after ITSM ticket-based professional suport methodologies.    

(see: [Troubleshooting Journal](https://github.com/robohlstrom24/troubleshooting-journal))


## Lab Set-Up

<details>
<summary>Switch Set-Up and Baseline Connectivity</summary>

- Connected a **TP-Link 8-port Gigabit desktop switch** to the router and lab endpoints:

  - **Port 1** (uplink): Router  
  - **Port 2:** OptiPlex desktop  
  - **Port 3:** ThinkPad laptop  

- Observed green link/activity LEDs on all three ports, indicating active **1 Gbps Ethernet connections**.

- Ran `ipconfig /all` on both endpoints to document host identity, IP addressing, and DHCP lease details.

**Desktop**
- Hostname: `Rob-Desktop`
- IPv4 Address: `192.168.0.158/24`
- Default Gateway: `192.168.0.1`
- DHCP Server: `192.168.0.1`
- MAC Address: `54-BF-64-7B-CE-20`

**Laptop**
- Hostname: `Rob-Laptop`
- IPv4 Address: `192.168.0.63/24`
- Default Gateway: `192.168.0.1`
- DHCP Server: `192.168.0.1`
- MAC Address: `8C-16-45-3E-DA-64`

- Confirmed both endpoints were assigned addresses within the same subnet (`192.168.0.0/24`), validating correct DHCP scope configuration.

**Connectivity Testing**
- Enabled Windows Defender Firewall rules for ICMP echo requests
- Performed bidirectional `ping` tests between endpoints
- Verified successful ICMP responses with no packet loss

**Outcome:**  
- Established baseline Layer 2 and Layer 3 connectivity, validated DHCP functionality, and documented core lab endpoints.  
- Baseline connectivity validation supports downstream services including domain authentication, DHCP reservations, and server role deployment.

</details>

<details>
<summary>DHCP Addressing Plan & Basic DNS</summary>

- Updated router default credentials and applied latest firmware (network infrastructure hardening)
- Configured DHCP address pool:  
  `192.168.0.100–192.168.0.199`
- Reserved static address block outside DHCP scope:  
  `192.168.0.200–192.168.0.254` (future appliances and ad-hoc lab testing)

**DHCP Reservations**
Configured DHCP reservations within the active scope for key lab endpoints:

- **Desktop:**  
  MAC `54-BF-64-7B-CE-20` → `192.168.0.198`
- **Laptop:**  
  MAC `8C-16-45-3E-DA-64` → `192.168.0.101`

Released and renewed DHCP leases on both hosts and confirmed that each endpoint obtained its reserved IP address.

**DNS Configuration & Validation**
- Configured upstream DNS resolvers to **Cloudflare** (`1.1.1.1` / `1.0.0.1`) for performance and privacy
- Client systems use the router (`192.168.0.1`) as their DNS server, which forwards queries upstream
- Verified external name resolution using:  
  `nslookup www.google.com 1.1.1.1`

**Outcome:**  
- Endpoints have predictable, reserved IP addressing with verified DNS resolution.
- Addressing plan supports stable identity services, server roles, and future lab growth.

</details>

<details>
<summary>Lightweight Asset Register</summary>

| Device  | Model                  | Hostname     | CPU                | RAM        | Storage            | Network                               | Notes                          |
|--------|------------------------|--------------|--------------------|------------|--------------------|----------------------------------------|--------------------------------|
| Desktop | Dell Optiplex 5060 SFF | rob-desktop  | Intel Core i5-8500 | 32 GB DDR4 | 1 TB M.2 NVMe SSD  | TP-Link WiFi 6E Intel AX210 PCIe       | Primary lab workstation             |
| Laptop  | Lenovo ThinkPad T480  | rob-laptop   | Intel Core i7-8550U | 16 GB DDR4 | 256 GB Samsung MZ7LN | Intel Dual Band AC 8265               | Portable lab endpoint          |
| Switch  | TP-Link TL-SG108      | –            | –                  | –          | –                  | 8 Gigabit Ethernet ports              | Layer 2 access switch (unmanaged) |
| Router  | TP-Link Archer AX1800 | –            | –                  | –          | –                  | Gigabit WAN, WiFi 6                   | NAT, stateful firewall, DHCP server, DNS forwarder |

</details>

<details>
  <summary>IP Addressing Scheme</summary>

### IP Addressing Scheme

| Device / Role     | Hostname        | IP Address          | MAC Address           | Subnet Mask       | Gateway       | Operating System | Notes                                      |
|-------------------|-----------------|---------------------|-----------------------|-------------------|---------------|------------------|--------------------------------------------|
| Network Address   | –               | 192.168.0.0         | –                     | 255.255.255.0     | –             | –                | Defines subnet boundary                    |
| Desktop           | rob-desktop     | 192.168.0.158       | 54-BF-64-7B-CE-20     | 255.255.255.0     | 192.168.0.1   | Windows 11 Pro   | DHCP reservation                           |
| Laptop            | rob-laptop      | 192.168.0.63        | 8C-16-45-3E-DA-64     | 255.255.255.0     | 192.168.0.1   | Windows 11 Home  | DHCP reservation                           |
| Router            | edge-gateway    | 192.168.0.1         | 74-FE-CE-37-46-01     | 255.255.255.0     | 192.168.0.1   | TP-Link Firmware | Default gateway, DHCP/DNS server           |
| Switch            | –               | –                   | –                     | 255.255.255.0     | 192.168.0.1   | Unmanaged        | Unmanaged Layer-2 switch (no management IP) |
| DHCP Scope        | –               | 192.168.0.100–199   | –                     | 255.255.255.0     | 192.168.0.1   | –                | Reserved for dynamic clients               |
| Broadcast         | –               | 192.168.0.255       | –                     | 255.255.255.0     | –             | –                | Sends to all hosts on subnet               |

</details>

## Tier-1 IT Support Tasks

<details>
  <summary> No Internet Connectivity - Misconfigured Static IP </summary>

Scenario: Newly deployed or reconfigured workstation loses Internet connectivity due to static IP address assignment instead of DHCP.

![1](images/NoInternet1/(1)no.internet.png)
____________________________________________
![2](images/NoInternet1/(2)root.cause.png)
_____________________________________________
![3](images/NoInternet1/(3)remediation.png)
____________________________________________
![4](images/NoInternet1/(4)resolution.png)
______________________________________________
![5](images/NoInternet1/(5)success.png)

</details>

## Tier-2 IT Support Tasks

<details> 
<summary> Troubleshooting IP Address Conflict</summary>

Scenario: A newly installed conference room PC was configured with a static IP address that falls within the office's DHCP scope. DHCP later assigned the same address to another endpoint from the available IP pool, creating a duplicate IP conflict that disrupted Internet connectivity. 

![1](images/address-conflict/(2)no.internet.VM2.png)
____________________________________________________
![2](images/address-conflict/(3)VM.ping.fail.2.png)
_____________________________________________________
![3](images/address-conflict/(4)event.viewer2.png)
_____________________________________________________
![4](images/address-conflict/(5)duplicate.identified.png)
______________________________________________________
![5](images/address-conflict/(6)root.cause.png)

See ITSM-style troubleshooting ticket T-0010 in [Troubleshooting Journal](https://github.com/robohlstrom24/troubleshooting-journal)

</details>

## Future Enhancements

- Introduce a virtualized lab server to demonstrate server hosting concepts and support additional infrastructure services  
- Implement a VPN solution to enable secure remote access and simulate off-site connectivity scenarios  
- Add network monitoring and health checks to increase visibility into device availability and performance (PowerShell)  
