# Day 12: Introduction to Metasploit & The Attacker's Mindset

## Objective
To understand and execute the fundamentals of exploitation using the Metasploit Framework. The initial goal was to gain remote code execution on a Windows Server VM using a known vulnerability. The actual lab evolved into a real-world demonstration of an attacker's need to pivot when faced with a hardened target, culminating in a successful compromise via a client-side attack vector.

## Tools Used
-   Metasploit Framework (`msfconsole`)
-   Msfvenom (Payload Generator)
-   Python `http.server` (for payload delivery)
-   Windows Server 2019 VM
-   Kali Linux VM

## Steps & Evidence

This engagement simulated a real penetration test where the initial attack plan had to adapt to the target's defenses.

1.  **Initial Reconnaissance & Exploitation Attempts (Failure):** My first step was to attempt exploitation using well-known, high-impact public exploits.
    *   **BlueKeep (CVE-2019-0708):** I attempted the `exploit/windows/rdp/cve_2019_0708_bluekeep_rce` module. Metasploit's built-in check correctly determined that my modern Windows Server 2019 installation was patched against this vulnerability, and the exploit aborted.
    *   **EternalBlue (MS17-010):** After enabling the legacy `SMBv1` protocol on the server, I attempted the `exploit/windows/smb/ms17_010_eternalblue` module. Again, Metasploit correctly determined the target was not vulnerable, as the underlying flaw had been patched in the base OS.

2.  **Pivoting to a Client-Side Attack:** With direct remote exploitation failing due to a hardened target, I pivoted my strategy. Instead of attacking a listening service, I simulated a phishing scenario where a user is tricked into running a malicious file.
    *   **Payload Generation:** I used `msfvenom` to create a custom payload. Specifically, a `windows/x64/meterpreter/reverse_tcp` payload was generated as an `.exe` file. This payload is designed to be executed on the target and connect *back* to the attacker.
    *   **Payload Delivery:** I staged the payload on a simple web server hosted on my Kali VM using `python3 -m http.server`.

3.  **Bypassing Antivirus (Blue Team Interaction):** When attempting to download the payload on the Windows Server, Windows Defender's real-time protection immediately identified the signature of the Meterpreter payload and blocked the download, labeling it as a virus. This was a perfect demonstration of a host-based security control working as intended. For the purpose of the lab, I temporarily disabled real-time protection to simulate an environment with a less effective AV solution or a more advanced, obfuscated payload.

4.  **Setting up the Listener:** Back in `msfconsole`, I configured the `exploit/multi/handler` module. This generic module acts as a listener, waiting to "catch" the connection from the payload. I configured it with the same payload, `LHOST` (my Kali IP), and `LPORT` that I used to generate the `.exe` file.

5.  **Execution and Shell:** I executed the payload (`update.exe`) on the Windows Server. Immediately, it connected back to my listener in Metasploit, opening a Meterpreter session and giving me full `NT AUTHORITY\SYSTEM` level control over the machine. I verified this access using post-exploitation commands like `sysinfo` and `getuid`.

### Evidence

*The initial reconnaissance phase showing both the BlueKeep and EternalBlue exploits failing, as Metasploit correctly identifies the Windows Server target as not vulnerable.*
![Metasploit Recon Failures](metasploit-recon-failures.png)
<img width="958" height="1079" alt="Screenshot 2025-08-18 210925" src="https://github.com/user-attachments/assets/b117aa00-63e9-4b56-995e-35e6589dd216" />
<img width="960" height="1079" alt="Screenshot 2025-08-18 211640" src="https://github.com/user-attachments/assets/f25fde21-86f1-458b-b967-e1d961097383" />


*The successful establishment of a Meterpreter session. The `exploit/multi/handler` catches the reverse TCP connection from the payload executed on the Windows Server.*
![Meterpreter Success](meterpreter-success.png)
![Uploading Screenshot 2025-08-18 214646.png…]()


## Challenges & Reflection

This lab was a profound lesson in the "cat and mouse" game of cybersecurity.

-   **The Hardened Target:** My biggest challenge was also my biggest learning moment: the target was too secure for the "easy" exploits. This is a testament to the effective default security of modern operating systems like Windows Server 2019. It proves that prompt patching forces attackers to work harder.
-   **The Importance of Pivoting:** I learned that when one attack vector fails, a skilled attacker doesn't give up; they pivot. Shifting from remote service exploits to a client-side attack is a fundamental Tactic, Technique, and Procedure (TTP).
-   **Defense-in-Depth:** Seeing Windows Defender block my payload was a fantastic demonstration of layered security. The network firewall (pfSense) is one layer, the patched OS is another, and the host-based antivirus is a third. An attacker must bypass all of them to succeed.
-   **The Power of Meterpreter:** Getting the Meterpreter shell made the abstract concept of "remote control" tangible. Seeing the `NT AUTHORITY\SYSTEM` privilege level firsthand was a stark reminder of the devastating impact a successful exploit can have, granting an attacker more power over the system than a regular administrator.
