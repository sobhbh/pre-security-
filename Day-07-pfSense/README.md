# Day 7: pfSense Firewall Basics

## Objective
To deploy and configure pfSense, a powerful open-source firewall, to act as the gateway for my virtual lab network, controlling both internal and external traffic flow. My goal was to establish a secure network perimeter, learn the fundamentals of firewall rule logic, and practice real-world network troubleshooting.

## Tools Used
- pfSense Community Edition (v2.8.0 and the more stable v2.7.2 for troubleshooting)
- VirtualBox
- Windows Server 2019 VM
- Kali Linux VM

## Steps & Evidence

1.  **VM Deployment:** I created a new pfSense VM in VirtualBox with a crucial two-adapter configuration to segment traffic:
    *   **Adapter 1 (WAN):** Configured as a `Bridged Adapter` to connect directly to my host's network, simulating an internet connection and receiving an IP address via DHCP.
    *   **Adapter 2 (LAN):** Configured as a `Host-only Adapter`, connecting it to the isolated virtual switch shared by my Windows Server and Kali VMs.

2.  **Console Configuration:** After booting the pfSense VM, I used the console menu to:
    *   Assign the correct network interfaces (`em0` to WAN, `em1` to LAN).
    *   Set a static IP address for the LAN interface to `192.168.56.1`, making it the permanent gateway for my lab network.
    *   Enable the DHCP server on the LAN interface for future devices.

3.  **Client Reconfiguration:** I updated the TCP/IP settings on both my Kali and Windows Server VMs to use the pfSense LAN IP (`192.168.56.1`) as their **Default Gateway** and primary **DNS Server**. This successfully routed all their traffic through the firewall.

4.  **Firewall Rule Implementation:**
    *   **GUI Access:** I successfully logged into the pfSense WebGUI from a client VM and completed the initial setup wizard, configuring the hostname, domain (`bootcamp.local`), and a new admin password.
    *   **WAN Rule (Hardening):** I created a rule on the WAN interface to `Block` all inbound `ICMP` traffic. This demonstrates the best practice of making the network perimeter unresponsive to external ping scans, reducing its attack surface.
    *   **LAN Rule (Troubleshooting):** To solve a challenging connectivity issue, I created an explicit `Pass` rule on the LAN interface to allow TCP traffic from any source on the `LAN net` to the firewall itself on port `443` (HTTPS).

### Evidence

*My pfSense firewall ruleset, clearly showing the `Block` rule on the WAN interface and the `Pass` rule on the LAN.*
![Firewall Rules]
<img width="1919" height="1079" alt="Screenshot 2025-08-01 154611" src="https://github.com/user-attachments/assets/7db5f486-d6f4-41a2-9cf5-e90db443b549" />

*The pfSense dashboard, confirming that both WAN and LAN interfaces are up, with distinct IP addresses from different subnets.*
![pfSense Dashboard]
<img width="1919" height="1038" alt="Screenshot 2025-08-01 155040" src="https://github.com/user-attachments/assets/e1ddf584-ebb3-40e4-af59-00626b31d900" />

## Challenges & Reflection

This lab was the most challenging yet and an incredible lesson in network troubleshooting and persistence. I faced several critical installation errors that required methodical debugging:

- **Corrupted VM Profile (`CPU doesn't support long mode`):** I learned that a VM's configuration file (`.vbox`) can become corrupt. I solved this by detaching the virtual disk, recreating the VM profile from scratch, and reattaching the disk.
- **Unstable Installer (`pager read error`, `installer daemon error`):** The release candidate of the pfSense installer proved unstable in my environment. This taught me the importance of falling back to a known, stable software release (`v2.7.2`), which ultimately worked.
- **Asymmetric Connectivity (Kali Failure):** The most insightful problem was the web GUI working from Windows Server but not from Kali, despite all IP and routing configurations appearing correct. Since `ping` worked, I deduced the issue was with TCP traffic. This led me to the final solution: creating an explicit `Pass` rule on the LAN interface, which overrode a subtle, non-obvious default behavior.

This lab was a fantastic introduction to real firewall administration. It clearly demonstrated stateful inspection, the "default deny" security posture, and the necessity of layer-by-layer troubleshooting—from the virtual hardware up to the application-level firewall rules.
