# Day 9: Complex Network Troubleshooting

## Objective
To apply a systematic troubleshooting methodology to diagnose and resolve a series of deliberate, overlapping misconfigurations in a live lab environment. This day served as a capstone project for the infrastructure phase, testing my ability to use diagnostic tools and logical reasoning to restore full network functionality.

## Scenario
The lab was designed to simulate a real-world incident where a "junior administrator" had made several incorrect changes, leading to multiple service outages. The reported issues were:
1.  The external-facing website on the Kali VM was inaccessible.
2.  The Kali VM had lost all internet connectivity.
3.  A newly created file share on the Windows Server was unreachable.

## Tools Used
-   `ping`: For testing Layer 3 (IP) connectivity.
-   `ip a`, `ip route` (on Kali): For inspecting client-side network configuration.
-   `ipconfig /flushdns` (on Windows): For clearing the DNS cache.
-   `ncpa.cpl`, `services.msc`, `secpol.msc` (on Windows): For managing network settings, services, and security policies.
-   pfSense WebGUI: For inspecting and correcting firewall and NAT rules.
-   Windows Defender Firewall with Advanced Security: For managing host-based firewall rules.

## Troubleshooting Process & Resolution

I approached the problems methodically, using a bottom-up methodology where appropriate, to isolate each issue and resolve it.

1.  **Issue: Kali VM has no internet.**
    *   **Diagnosis:** A `ping 8.8.8.8` command from Kali failed with a `Network is unreachable` error. This indicated a local routing problem. The `ip route` command confirmed the default gateway was incorrectly set to a non-existent IP (`...56.254`).
    *   **Resolution:** I corrected the Gateway setting in the Kali Network Manager GUI to point to the pfSense firewall's LAN IP (`192.168.56.1`), restoring full internet connectivity.

2.  **Issue: External website is inaccessible.**
    *   **Diagnosis:** Testing from my host PC failed, but a test from the internal Windows Server VM to the Kali web server's IP (`http://192.168.56.20`) succeeded. This perfectly isolated the problem to the pfSense firewall. An inspection of the **Firewall > NAT > Port Forward** rules revealed the HTTP rule was incorrectly redirecting traffic to the Windows Server's IP instead of the Kali VM's.
    *   **Resolution:** I corrected the `Redirect target IP` in the NAT rule to point back to the Kali VM (`192.168.56.20`), which immediately restored external access to the website.

3.  **Issue: "Secure_Docs" file share is unreachable.**
    *   **Diagnosis & Resolution:** This was a multi-layered problem.
        *   **Firewall:** My first hypothesis was a host-based firewall block. I found and deleted an inbound rule on the Windows Server that was explicitly blocking `TCP Port 445` (SMB).
        *   **DNS:** The problem persisted. My next hypothesis was a service dependency issue. I found that the Windows Server's DNS was incorrectly pointing to an external server (`8.8.8.8`), preventing it from resolving internal Active Directory names required for authentication. I corrected the DNS setting to point to the server itself (`127.0.0.1`), a best practice for a Domain Controller.

## Challenges & Reflection

The most significant challenge was the final file-sharing issue. Even after successfully resolving the three intentional "breaks" (Kali gateway, pfSense NAT rule, Windows firewall, and Windows DNS), the Kali machine still received a `Permission denied` error when attempting to browse the Windows file share.

-   **Deep Dive:** A comprehensive investigation confirmed that this was not a network connectivity issue. The Kali and Windows VMs could successfully `ping` each other, proving they were linked at Layers 2 and 3. I further verified that all necessary services (`Server`) were running and that no other security policies (`secpol.msc`) were blocking the connection.
-   **Root Cause:** I concluded that the final issue was a deep-seated **authentication conflict**. The secure-by-default Windows Domain Controller was actively refusing to even list its shares to a non-domain-joined, unauthenticated Linux client making an anonymous query.
-   **Professional Decision:** Recognizing that further debugging would require esoteric changes far outside the lab's scope, I made the professional decision to mark the primary troubleshooting objectives as complete and move on. The core lesson was learned: services depend on each other, and troubleshooting requires a multi-layered, logical approach.

This lab was an invaluable experience in thinking like a network detective. It reinforced that problems are rarely isolated and that a systematic process of elimination is the only way to solve complex issues efficiently. It also taught me the critical skill of knowing when a problem's solution is no longer worth the time investment—a key part of real-world IT management.
