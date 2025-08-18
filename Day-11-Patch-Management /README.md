# Day 11: Patch Management with WSUS

## Objective
To complete the vulnerability management lifecycle by deploying an enterprise-grade patch management solution, Windows Server Update Services (WSUS), to remediate vulnerabilities discovered in Day 10. The goal was to install and configure WSUS, use Group Policy to manage clients, approve and deploy a targeted security update, and verify the patch's effectiveness with a follow-up vulnerability scan.

## Tools Used
-   Windows Server 2019 (as both the WSUS host and the client)
-   Windows Server Update Services (WSUS) Role
-   Windows Internal Database (WID)
-   Group Policy Management
-   Nessus Essentials Vulnerability Scanner

## Steps & Evidence

The lab began with a significant real-world challenge: recovering access to the server.

1.  **Domain Controller Password Recovery:** My server's administrator password had expired due to a GPO I created on Day 3. After a failed attempt using a standard `ntpasswd` utility (which I learned only works on the SAM file of non-domain controllers), I successfully performed a low-level password reset. I booted the DC with a Linux rescue disk (`SystemRescue`), mounted the Windows file system, and used command-line tools (`mv`, `cp`) to replace the "Ease of Access" utility (`Utilman.exe`) with the Command Prompt (`cmd.exe`). This classic technique allowed me to gain a SYSTEM-level command prompt at the login screen and reset the domain administrator password.

2.  **WSUS Role Installation & Configuration:** I successfully installed the WSUS role on my Windows Server (`DC1`), configuring it to use a WID database and storing updates locally in `C:\WSUS_Updates`. I completed the post-installation configuration and initiated the first synchronization with Microsoft's update servers.

3.  **Client Management via Group Policy:** I created and linked a new GPO named `WSUS Configuration Policy`. Within this GPO, I enabled and configured two critical policies:
    *   `Configure Automatic Updates`: Set to auto-download and notify for installation.
    *   `Specify intranet Microsoft update service location`: Set both the update and statistics server to `http://DC1.bootcamp.local:8530`.

4.  **Patch Approval and Deployment:** After forcing a client check-in using `gpupdate /force` and `wuauclt /reportnow`, my server (`DC1`) successfully appeared in the WSUS console. From the list of "Needed" updates, I approved a key "Security Monthly Quality Rollup" for the computer group. I then used the Windows Update client on the server to pull this approved patch from my local WSUS server, install it, and reboot.

5.  **Verification Scan:** I performed a new Nessus scan targeted directly at my patched Windows Server (`192.168.56.10`). The scan results confirmed that the server maintained a strong security posture from an unauthenticated perspective, with no critical, high, or medium vulnerabilities detected.

### Evidence

*My WSUS console after the `DC1` server successfully registered, showing it needs 67 updates. This proves the Group Policy Object and client-server communication are working correctly.*
![<img width="956" height="1079" alt="Screenshot 2025-08-18 195706" src="https://github.com/user-attachments/assets/c8cfdf54-2373-476e-952d-3906c244a992" />
](wsus-computer-reported.png)

*The successful verification scan from Nessus after patching, showing a clean report with zero Critical, High, or Medium severity findings.*
![<img width="1919" height="1040" alt="Screenshot 2025-08-18 203618" src="https://github.com/user-attachments/assets/9449b24f-a7ba-44c3-a3a3-a6d4c50615e6" />
](nessus-verification-scan.png)

## Challenges & Reflection

This lab was a deep dive into the realities of enterprise systems management. The most significant challenge was the initial **WSUS console instability**. The console would repeatedly crash and throw connection errors. I diagnosed this as a resource exhaustion problem, specifically with the default memory limit of the `WsusPool` in IIS. I resolved this by going into the IIS Manager's advanced settings and changing the "Private Memory Limit" to `0` (unlimited), a common best practice that immediately stabilized the console.

This day perfectly illustrated the professional **vulnerability management lifecycle:**

1.  **Identify:** Find vulnerabilities (Day 10).
2.  **Remediate:** Deploy patches from a central authority (Day 11).
3.  **Verify:** Re-scan to confirm the fix (Day 11).

I also learned that a clean external scan doesn't mean a machine is fully patched; it often means the host firewall is doing its job well. This highlights the importance of authenticated scans in a real-world internal audit and reinforces the principle of defense-in-depth. Managing WSUS and GPO felt like wielding the real tools of a system administrator, controlling an entire network's update policy from a single point of truth.
