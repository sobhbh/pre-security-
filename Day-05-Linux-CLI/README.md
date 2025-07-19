# Day 5: Linux Command Line & Permissions

## Objective
To practice fundamental Linux command line navigation and file system management, and to understand the Linux permissions model and privileged access via `sudo`.

## Tools Used
- Kali Linux Terminal
- `cd`, `ls -la`, `chmod`, `sudo`, `locate`

## Steps & Evidence
I navigated the Linux File System Hierarchy using `cd` and `ls`. I practiced creating and modifying files and directories. A key part of the lab was using `chmod` with both symbolic (e.g., `u+x`) and octal (e.g., `755`) notation to change file permissions.

I also created a new standard user and added them to the `sudoers` group, allowing them to execute commands as root. Finally, I located key configuration and log files like `/etc/ssh/sshd_config` and system logs in `/var/log`.

![Screenshot of using touch to create a file and ls -l to get more info about the file and chmod to remove permission from others ]
<img width="647" height="332" alt="Screenshot 2025-07-10 151303" src="https://github.com/user-attachments/assets/183108c6-8ac2-4e13-94a4-78a0859a4c31" />

![Screenshot of trying to access log file]

<img width="643" height="112" alt="1fdc63c6-34af-4454-887d-b6f5778beb12" src="https://github.com/user-attachments/assets/ff40c62f-02e7-42d0-bf08-a854e38d62ea" />


## Challenges & Reflection
[I had a trouble with auth.log file but i knew it was for past versions of kali and modern use journalctl, "Initially, I found the Linux file hierarchy less intuitive than Windows, but it started to make sense with practice."] This lab was essential for building CLI fluency. Understanding `sudo` and the `sudoers` file is fundamental to Linux security, as it controls the gateway to administrative privilege.
