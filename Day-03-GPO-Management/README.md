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

![Screenshot of Server Manager ]<img width="1037" height="778" alt="Screenshot 2025-06-30 175337" src="https://github.com/user-attachments/assets/69479733-3f6c-4e54-a274-19f9f5c21a51" />
nshot.png)


## Challenges & Reflection
This lab showed me the scalability of command-line management. Creating one GPO and linking it to an OU is far more efficient than manually configuring settings on hundreds of individual computers, demonstrating the Principle of Least Privilege and centralized management.
