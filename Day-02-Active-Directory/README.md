# Day 2: Active Directory Deployment

## Objective
To promote a Windows Server to a Domain Controller (DC) and build out a basic organizational structure within Active Directory to mimic a corporate environment.

## Tools Used
- Windows Server 2019
- Active Directory Users and Computers (ADUC)
- DNS Manager

## Steps & Evidence
I configured the Windows Server with a static IP and then ran the Active Directory Domain Services (AD DS) installation wizard to promote it to a DC for a new forest. A key step was ensuring DNS was configured correctly.

Once the DC was active, I launched ADUC and created a hierarchy of Organizational Units (OUs) for IT, Sales, and Servers. Inside these OUs, I created sample user accounts to simulate employees in different departments.

I started thinking of this repository at day 6 so screenshots aren't available till then.

## Challenges & Reflection
This lab provided a concrete understanding of how central AD is to Windows network management. I now see how OUs are crucial for delegating permissions and applying specific policies (GPOs) to different user groups.
