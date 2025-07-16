# Day 1: Virtualization & Troubleshooting Method

## Objective
To set up a foundational virtual lab environment using VirtualBox, install Kali Linux and Windows Server VMs, and apply a structured troubleshooting methodology to solve a common network issue.

## Tools Used
- VirtualBox
- Kali Linux
- Windows Server 2019
- `ipconfig` (Windows) & `ip a` (Linux)

## Steps & Evidence
I began by installing VirtualBox and configuring the networking to allow communication between VMs. I then installed both Kali Linux and Windows Server as guest operating systems. I verified connectivity using basic network commands.

To practice problem-solving, I deliberately broke a network setting. Using the 6-step troubleshooting methodology, I was able to systematically identify and resolve the issue.

- **Step 1: Identify the problem.** VMs could not ping each other.
- **Step 2: Establish a theory.** The VM was likely attached to the wrong virtual network adapter.
- **[...Finish listing the steps and what you found...]**

I started thinking of this repository at day 6 so screenshots aren't available till then.

## Challenges & Reflection
This lab reinforced that a structured troubleshooting process saves significant time compared to randomly changing settings. It is a critical skill for any IT/security role.
