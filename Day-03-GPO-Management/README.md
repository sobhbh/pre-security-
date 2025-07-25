# Day 3: PowerShell & Group Policy (GPO) Management

## Objective
To use PowerShell for efficient user management in Active Directory and to create and apply a Group Policy Object (GPO) to enforce a security control.

## Tools Used
- PowerShell ISE
- Group Policy Management Console
- Active Directory Users and Computers (ADUC)

## Steps & Evidence
I used PowerShell cmdlets like `New-ADUser` and `Get-ADUser` to programmatically create and query user accounts, demonstrating a faster alternative to the GUI.

Next, I created a new GPO to enforce a password complexity policy. I linked this GPO to the "Sales" OU and tested its effect by attempting to set a non-compliant password for a user in that OU, which failed as expected.

![Screenshot of Server Manager ] 
<img width="1037" height="778" alt="Screenshot 2025-06-30 175337" src="https://github.com/user-attachments/assets/69479733-3f6c-4e54-a274-19f9f5c21a51" />
![Screenshot of Using nslookup ] 
<img width="529" height="329" alt="Screenshot 2025-07-03 171049" src="https://github.com/user-attachments/assets/ec9d6755-968a-43e2-88fd-a3525bdd2aac" />
![Screenshot of Entering the domain ] 
<img width="564" height="401" alt="Screenshot 2025-07-03 185243" src="https://github.com/user-attachments/assets/e523fa37-386e-4374-a2b1-72fa66797e32" />





## Challenges & Reflection
I had troubles at logging in the new user created and fixed it.
This lab showed me the scalability of command-line management. Creating one GPO and linking it to an OU is far more efficient than manually configuring settings on hundreds of individual computers, demonstrating the Principle of Least Privilege and centralized management.
