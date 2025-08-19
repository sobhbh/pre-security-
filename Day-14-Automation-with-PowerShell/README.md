# Day 14: Automation with PowerShell Scripting

## Objective
To transition from manual, single-command administration to scalable automation by writing a comprehensive PowerShell script. The goal was to simulate a real-world employee onboarding process by creating multiple Active Directory users in bulk, reading their data from a CSV file, and using logic to assign them to the correct Organizational Units (OUs) and security groups.

## Tools Used
-   Windows PowerShell ISE (Integrated Scripting Environment)
-   Active Directory (AD) PowerShell Module (`New-ADUser`, `Add-ADGroupMember`)
-   Active Directory Users and Computers (for verification)
-   Notepad (for creating the CSV data source)

## Steps & Evidence

This lab was a practical exercise in scripting and a demonstration of the power of Infrastructure as Code.

1.  **Environment Setup:** I began by working within the PowerShell ISE, the ideal environment for developing and debugging scripts. I created a simple data source, `NewUsers.csv`, containing the first names, last names, and departments for a batch of new "employees".

2.  **Script Development & Logic:** I developed a multi-step PowerShell script designed to be both robust and reusable:
    *   **Data Import:** The script begins by using the `Import-Csv` cmdlet to ingest the user data from the `NewUsers.csv` file into a PowerShell object.
    *   **Iteration:** A `foreach` loop was implemented to process each user (each row in the CSV) one by one.
    *   **Dynamic Variable Creation:** Inside the loop, I dynamically created variables for each user's properties, such as concatenating the first initial and last name to create a standardized username (`SamAccountName`).
    *   **Secure Password Generation:** For each user, the script generates a secure, randomized temporary password using `ConvertTo-SecureString` and `Get-Random`, displaying it in the console for the administrator.
    *   **Conditional Logic:** The core of the automation is a powerful `if/else` block that inspects the user's `Department` field. Based on this value, it dynamically sets the target OU path and the name of the security group the user should be added to. This allows the single script to correctly place Sales and IT staff without manual intervention.
    *   **AD Object Creation:** Finally, the script executes the `New-ADUser` cmdlet to create the user account in the correct OU and the `Add-ADGroupMember` cmdlet to assign them to their departmental group.

3.  **Execution and Verification:** I ran the complete script from the ISE. It executed flawlessly on the first attempt, processing the entire CSV file in seconds and providing clear, color-coded status updates for each user it created. I then verified the results in "Active Directory Users and Computers," confirming that all new users were present in their correct OUs and security groups.

### Evidence

*The complete, final version of my PowerShell user-creation script, written and executed in the PowerShell ISE.*
![PowerShell User Creation Script](powershell-user-creation-script.png)
<img width="1919" height="999" alt="image" src="https://github.com/user-attachments/assets/2f7ff2dc-3d90-4fe0-a196-42311c407588" />

*The successful output of the script in the PowerShell console, showing each user being created and added to the correct group, followed by the "Script Finished" message.*
![Script Execution Success](script-execution-success.png)
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/5744ffac-88be-4269-afa5-87f401d9ae87" />
<img width="1919" height="1028" alt="image" src="https://github.com/user-attachments/assets/4e9d4610-74b1-457b-875a-710c9b91edff" />

## Challenges & Reflection

This lab was a transformative experience. The initial challenges involved learning the nuances of PowerShell syntax, such as the difference between member access (`.`) and comparison operators, and the correct structure of loops (`foreach {}`). However, the real success was creating the final, complex script and having it run perfectly on the first try.

-   **The Power of Automation:** The contrast between this scripted approach and the manual, click-by-click process is staggering. A task that would take many minutes and be prone to human error (typos, putting a user in the wrong group) was completed in under two seconds. Scaling this to onboard 100 or 1000 users would require zero extra effort beyond adding more lines to the CSV file.

-   **Infrastructure as Code (IaC):** This was my first practical application of IaC. The PowerShell script is now a reusable, versionable, and auditable asset. It is a documented "recipe" for creating users that ensures absolute consistency and compliance with organizational standards. This is a far more robust and professional approach than manual administration.

-   **The Object-Oriented Shell:** Using PowerShell felt fundamentally different from using Bash. The ability to import a CSV and have it instantly become an array of objects, with each column available as a property (e.g., `$user.FirstName`), is incredibly powerful and intuitive. It makes processing structured data seamless.

Day 14 was a capstone project for the administrative phase of my training. I've moved from managing single objects to managing an entire system through code, a critical skill for any modern IT or cybersecurity professional.
