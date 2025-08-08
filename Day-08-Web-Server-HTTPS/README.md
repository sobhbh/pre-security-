# Day 8: Web Server Deployment & HTTPS

## Objective
To deploy a functional web server within a private, firewalled network and securely expose it to external traffic. This lab focused on configuring NAT Port Forwarding, diagnosing firewall and service issues, and understanding the principles of SSL/TLS encryption by generating and deploying a self-signed certificate.

## Tools Used
- pfSense Firewall
- Apache2 Web Server
- OpenSSL

## Steps & Evidence

After a significant troubleshooting phase (detailed below), I proceeded with a fresh Kali Linux VM.

1.  **Apache Deployment:** I installed the Apache2 web server on the Kali VM using `apt`. I then started and enabled the service via `systemctl` and created a custom `index.html` file to act as a clear success indicator.

2.  **HTTP Port Forwarding:** In pfSense, I created a NAT Port Forwarding rule to direct incoming traffic on the `WAN` interface on `TCP/80 (HTTP)` to the internal IP of my Kali web server (`192.168.56.20`). Crucially, I ensured the "Filter rule association" was set to automatically create the corresponding firewall pass rule. This successfully made the web server accessible from my host computer.

3.  **SSL/TLS Certificate Generation:** I used the `openssl` command-line tool on Kali to generate a 2048-bit RSA private key and a corresponding self-signed x.509 certificate, valid for one year.

4.  **Apache SSL Configuration & Debugging:**
    *   I enabled the `ssl` module and the `default-ssl.conf` site in Apache.
    *   I edited the `default-ssl.conf` file to point to my newly generated certificate and key.
    *   Initially, the Apache service failed to restart due to a syntax error. Using the `journalctl -xeu apache2.service` command, I diagnosed the exact error—a typo in the certificate file path.
    *   After correcting the typo, the Apache service restarted successfully.

5.  **HTTPS Port Forwarding & Final Test:** I created a second NAT Port Forward rule for `TCP/443 (HTTPS)`. Testing the `https://` address from my host browser resulted in the expected security warning (due to the self-signed certificate) and, after proceeding, a successful connection to my encrypted site.

### Evidence

*My Kali terminal showing the process of creating the SSL certificate, enabling Apache modules, debugging the configuration file error, and the final successful service restart.*
![<img width="650" height="513" alt="Screenshot 2025-08-08 170537" src="https://github.com/user-attachments/assets/a4ee9e3c-3c30-4114-864f-a6f30662d207" />)

*My final pfSense NAT Port Forward rules, showing both HTTP and HTTPS traffic being correctly redirected to the Kali web server.*
![<img width="959" height="1079" alt="image" src="https://github.com/user-attachments/assets/8a3f314e-89cc-4b46-8979-e5d3986e4fab" />)

*The successful end result: accessing the web server from my host PC over an encrypted HTTPS connection, displaying the custom page and the browser's "Not secure" warning for the self-signed certificate.*
![<img width="960" height="489" alt="Screenshot 2025-08-08 170500" src="https://github.com/user-attachments/assets/1013822c-b05c-421a-8c27-882848f42e04" />)

## Challenges & Reflection

This day was a testament to the importance of methodical troubleshooting and the resilience required in IT and cybersecurity. The primary challenge was not the lab's intended steps, but a deeply-rooted, unrecoverable bug in the networking stack of the original Kali VM.

-   **Initial Failure:** Despite a provably correct network configuration on both Kali (`ip a`, `ip route`) and pfSense (firewall rules), all traffic from the Kali VM was being dropped before it even reached the pfSense firewall. The Windows VM, on the same virtual network, worked flawlessly.
-   **Troubleshooting & Isolation:** I systematically tested every layer: client OS configuration, firewall rules, pfSense state tables, and even re-created the entire VirtualBox Host-Only network adapter. This process proved that the pfSense and Windows VMs were healthy and that the fault was isolated entirely within the Kali VM at a level below user configuration (likely a kernel/driver issue).
-   **The Solution - Rebuilding:** The final, successful strategy was to accept that the VM was un-salvageable and to rebuild it from scratch. I chose to download the **pre-built `.ova` appliance** instead of using the installer ISO. This was a strategic decision that saved significant time and bypassed any potential installation bugs. I also had to diagnose and resolve several VirtualBox errors during the import process (`VERR_FILE_NOT_FOUND` and duplicate UUIDs), which involved using the Virtual Media Manager to clean up "ghost" disk registrations.

**What I Learned:** Sometimes, the most efficient solution is a full rebuild. Knowing when to stop debugging a broken component and instead replace it with a known-good one is a critical skill. This lab also provided invaluable, real-world experience in reading system logs (`journalctl`) to pinpoint application-level configuration errors. Ultimately, succeeding after such a difficult struggle was incredibly rewarding and solidified my understanding of network traffic flow from end to end.
