# Day 4: Windows Permissions & Firewall Troubleshooting

## Objective
To configure and differentiate between Share and NTFS permissions for a shared folder and to troubleshoot connectivity by managing the Windows Defender Firewall.

## Tools Used
- File Explorer (Permissions)
- Windows Defender Firewall with Advanced Security

## Steps & Evidence
I created a shared folder for the "Sales" department. I configured the **Share permissions** to be permissive (e.g., "Authenticated Users - Full Control") and then applied granular **NTFS permissions** to grant the Sales user group "Read/Write" access while denying access to others.

I then troubleshooted a connectivity issue by temporarily disabling the firewall service. Realizing this was insecure, I re-enabled it and created a custom inbound rule to allow ICMP (ping) traffic, demonstrating a more secure and precise solution.

![Screenshots of trying to shutdown firewall with servers and cmd ]
<img width="398" height="459" alt="Screenshot 2025-07-09 154234" src="https://github.com/user-attachments/assets/d0b4da12-48c2-419e-869e-0fc9c6a343ae" />
<img width="444" height="198" alt="Screenshot 2025-07-09 154400" src="https://github.com/user-attachments/assets/5d67beb2-dae1-43fe-b30b-5d378df65edc" />


## Challenges & Reflection
I had a hard time trying to turn off firewall but i turned it off evantually using cmd + run as admin
This lab highlighted the critical difference between sharing a folder and securing it. It also proved that disabling a firewall is never the right long-term solution; creating specific rules is the professional standard.
