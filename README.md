# File-and-Permission-drilling-In-Linux-distro.
A hands-on lab for practicing Linux file and permission drilling through simulated scenarios.
Master Lab Guide: File & Permission Drill
---------------------------------------------------------------------------
Step 1: Create the Directory and Test File
Detailed Breakdown:
sudo (SuperUser DO):
Why we use it: Linux restricts system directories (like /srv/) so standard users cannot create files there. sudo temporarily elevates your command to run with administrative (root) privileges.
mkdir (Make Directory):
Why we use it: Creates a new folder.
-p flag (Parents): Tells Linux to create any missing parent directories automatically and prevents throwing an error if the directory already exists.
touch:
Why we use it: Creates a blank file if it does not exist, or updates its timestamp if it already exists. Here, it creates budget_2026.txt so the directory has actual content to view.
ls (List):
------------------------------------------------------------------------------
Why we use it: Displays the contents of a directory[1].
-l flag (Long format): Displays detailed metadata, including permission bits, owner, group, file size, and creation date.
-a flag (All): Shows all files, including hidden files (files starting with a .).
-la combination: Combines both flags to show full detailed details for everything in /srv/.
--------------------------------------------------------------------------------
Open your terminal in Kali Linux.
Create the directory and a test file inside it:
sudo mkdir -p /srv/finance_docs
sudo touch /srv/finance_docs/budget_2026.txt
------------------------------------------------------------------------------
📸 SCREENSHOT 1: 
<img width="1122" height="950" alt="Screenshot 2026-10-18 185705" src="https://github.com/user-attachments/assets/0807e747-ec25-41ac-80c5-3b7e0e14b478" />


<img width="645" height="511" alt="Screenshot 2026-10-18 185921" src="https://github.com/user-attachments/assets/9bb9ac90-2a1e-49d4-9667-7bda73085523" />


Step 2: Create the Target User Account
--------------------------------------------------------
Detailed Breakdown:
useradd:
Why we use it: Adds a new user account to the system[1].
-m flag (Make home): Instructs Linux to build a home directory (/home/finance_user/) for the new account. Without -m, the user is created without a dedicated personal folder or configuration files.
finance_user: The specific username being created.
-------------------------------------------------------

passwd:
Why we use it: Assigns or changes a user account's password[1]. When prompted, type a password twice (characters will remain invisible on screen for security).

Add the new user account and set a password:
sudo useradd -m finance_user
sudo passwd finance_user
(Type a simple password twice when prompted).  Ej: user

📸 SCREENSHOT 2:

<img width="655" height="517" alt="Screenshot 2026-10-18 191308" src="https://github.com/user-attachments/assets/72a4b600-d896-4696-b1f4-5264d46b7534" />


Step 3: Restrict Folder Access & Demonstrate "Permission Denied"
Lock down the directory so only root can access it:
sudo chmod 700 /srv/finance_docs
Switch to finance_user:
su - finance_user
---------------------------------------------------------------------

 

(Type the password you set in Step 2—remember, no characters will show on screen while typing).
Attempt to list the directory contents:
ls /srv/finance_docs

📸 SCREENSHOT 3:

<img width="652" height="515" alt="Screenshot 2026-10-18 192220" src="https://github.com/user-attachments/assets/daa4b2a5-de64-4a79-9858-191b8da68c1c" />
-----------------------------------------------------------------------------------------


Step 4: Remediate Ownership & Permissions
Assign directory ownership to finance_user:
sudo chown -R finance_user:finance_user /srv/finance_docs
--------------------------------------------------------- 

Update permission bits to allow standard read and execute access:
sudo chmod 755 /srv/finance_docs
Detailed Breakdown:
chmod (Change Mode):
Why we use it: Modifies the access permission bits of a file or folder[1]. 
----------------------------------------------------------------
700 numeric notation: Linux permissions use three octal numbers corresponding to Owner, Group, and Others:
1st Digit ( 7 = Owner): 4 (read) + 2 (write) + 1 (execute) = 7 (rwx). Full control for root.

--------------------------------------------------------------- 
2nd Digit ( 0 = Group): 0 (---). Zero access for the group.
3rd Digit ( 0 = Others): 0 (---). Zero access for all other system users.
su (Substitute User / Switch User):
Why we use it: Switches your terminal session to another user account.
- flag (Login shell): Simulates a complete, clean login session. It loads finance_user's environment variables, shell configurations, and sets the working directory to /home/finance_user/.
ls /srv/finance_docs:
Why we run it: Since finance_user falls into the "Others" permission category and chmod 700 stripped all access for others, Linux blocks the request and outputs Permission denied.
exit:
Why we use it: Closes the finance_user session and returns you to your administrative account (kali)


--------------------------------------------------------------------------------------------------------------------------

📸 SCREENSHOT 4:


<img width="667" height="521" alt="Screenshot 2026-10-18 193300" src="https://github.com/user-attachments/assets/b91390f5-d824-4d01-ac55-b86af1e286f5" />

-------------------------------------------------------------------------------------------------------------------------------------------------------

Step 5: Verify Resolution & Inspect System Logs
Commands to run:
ls -la /srv/
su - finance_user
ls /srv/finance_docs
exit
sudo tail -n 10 /var/log/auth.log

--------------------------------------------------------------------------------------------------------------------------------------------------------

Detailed Breakdown:
ls -la /srv/:
Why we run it: Verifies that the ownership now reads finance_user finance_user and permission flags show drwxr-xr-x.
su - finance_user & ls /srv/finance_docs:
Why we run it: Verifies that finance_user can now successfully read the directory and view budget_2026.txt.

tail:
Why we use it: Prints the last lines (the "tail end") of a file.
-n 10 flag (Number of lines): Restricts output to only the last 10 lines of the file.
/var/log/auth.log:
Why we inspect it: System log file that records all user authentication events, su/sudo escalations, and session logins/logouts[2]. This proves that user switching events occurred during the ticket lifecycle.
📸 SCREENSHOT 5:



<img width="652" height="502" alt="Screenshot 2026-10-18 193710" src="https://github.com/user-attachments/assets/65feaea9-8826-4f5c-aa28-0d520846ab3d" />




------------------------------------------------------------------------------------------------------------------------------------------------------
In this lab, I learned the fundamentals of navigating the Linux file system and understanding user permissions. By practicing essential commands in the terminal, I gained hands-on experience viewing detailed directory ownerships, switching between user accounts to test access levels, and using commands like sudo to attempt actions with elevated privileges. Overall, the exercises provided a practical understanding of how Linux secures files and controls who can view or modify data across different directories.
