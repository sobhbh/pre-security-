# Day 15: Centralized Logging with a Wazuh SIEM

## Objective
To deploy a fully functional Security Information and Event Management (SIEM) solution, Wazuh, to act as the central nervous system for my lab's security monitoring. The goal was to establish a centralized logging pipeline, integrate a Windows Server endpoint via an agent, simulate a brute-force attack, and use the SIEM's dashboard to investigate the resulting security alerts. This lab simulates the core function of a Security Operations Center (SOC) analyst.

## Tools Used
-   Wazuh OVA (Virtual Appliance)
-   VirtualBox
-   Windows Server 2019 VM
-   Linux Command Line (for network configuration)

## Steps & Evidence

This lab was an advanced exercise in both deployment and deep-level network troubleshooting.

1.  **Deployment & Network Troubleshooting:** The initial deployment of the Wazuh OVA presented a major challenge: the VM failed to acquire an IPv4 address on the lab network.
    *   **Diagnosis:** Using the `ip a` command, I confirmed the `eth0` interface lacked an `inet` address. I pivoted from DHCP to static IP configuration.
    *   **Adaptation:** The standard `netplan` utility was not present. I adapted by exploring the filesystem, locating the correct configuration file at `/etc/netplan/50-cloud-init.yaml`, and manually writing a static IP configuration.
    *   **Further Diagnosis:** Even with a correct IP configuration, the web UI remained inaccessible. I used a process of elimination—verifying the service was listening on port `443` with `ss -tlpn` and confirming the absence of `iptables` or `firewall-cmd`—to prove the issue was not with the Wazuh server itself, but with the underlying VirtualBox network.
    *   **Resolution:** I resolved the connectivity black hole by completely rebuilding the VirtualBox Host-Only network adapter, a solution that finally allowed the VMs to communicate correctly.

2.  **Agent Deployment:** With the SIEM online and accessible at its new static IP (`192.168.56.101`), I logged into the Wazuh Web UI. I used the "Deploy new agent" feature to generate a custom PowerShell command for my Windows Server (`DC1`). I then executed this command on the server, which successfully installed, configured, and started the Wazuh agent.

3.  **Simulated Attack (Brute Force):** To test the detection capabilities, I performed a common attack simulation. I locked my Windows Server and intentionally failed the administrator login 5-6 times with an incorrect password.

4.  **Detection and Investigation:** I returned to the Wazuh Web UI and navigated to the `Discover` tab (the event viewer in my version). After applying a filter for `rule.groups: authentication_failed`, the dashboard immediately isolated the specific alerts related to my attack, showing a count of 6 failed login attempts. This successfully validated the entire monitoring pipeline from endpoint to SIEM.

### Evidence

*The successful agent deployment summary, showing 1 Active agent (`DC1`) reporting into the Wazuh SIEM.*
<img width="1919" height="1024" alt="Screenshot 2025-08-25 172446" src="https://github.com/user-attachments/assets/632ec577-fdd0-4098-82bd-d9d7448476af" />


*The final result of the investigation. The Wazuh dashboard, filtered for "authentication_failed" rule groups, clearly visualizing the 6 brute-force attempts from my simulated attack.*
<img width="960" height="1079" alt="Screenshot 2025-08-25 175119" src="https://github.com/user-attachments/assets/9048a501-3243-4f7f-aa77-dad742a0a37a" />


## Challenges & Reflection

This was one of the most challenging and rewarding labs, transitioning from theory to the complex reality of enterprise tool deployment.

-   **Troubleshooting Is the Skill:** The biggest lesson was that simply following a guide is not enough. The initial network failure forced me to go "off-script" and rely on a pure troubleshooting methodology. The process of diagnosing the issue—from IP configuration, to service status, to the underlying hypervisor network—was an incredibly valuable, real-world experience. It taught me to trust my diagnostic process and to not be afraid of rebuilding a component (the virtual network) when it's proven to be faulty.

-   **The Power of a SIEM:** The contrast between this lab and previous days is stark. Before, an attack like a failed login attempt would have been invisible unless I was manually watching the Event Viewer on that specific server. With Wazuh, the event was collected, analyzed, alerted on, and presented on a "single pane of glass." It's immediately clear how a SIEM is the only feasible way to monitor a network of any size, turning a sea of chaotic log data into actionable security intelligence.

-   **From Admin to Analyst:** This lab was the true culmination of my infrastructure build. It connected all the pieces: the domain controller (the log source), the firewall (the network), and now the SIEM (the observer). It was my first real taste of a day in the life of a SOC analyst, and it was a powerful demonstration of the Blue Team's capabilities.
