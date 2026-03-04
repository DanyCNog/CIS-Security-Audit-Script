# CIS-Security-Audit-Script
A comprehensive, robust, and fully automated Bash script to perform a security audit of **Debian 12** systems, based on the strict guidelines of the **CIS (Center for Internet Security) Benchmark**.

This script evaluates over **310 security controls**, covering critical areas such as file permissions, firewall rules, SSH configurations, password policies (PAM), scheduled tasks (Cron), and system auditing (AuditD).

# Table of Contents

**1.** Key Features

**2.** Prerequisites

**3.** Step 1: Prepare the File (Windows / VS Code)

**4.** Step 2: Send the Script to the Linux Server

**5.** Step 3: Connect to the Server and Execute

**6.** Step 4: Export and Analyze Results

**7.** Understanding the Control Classifications

**8.** Troubleshooting

# Key Features
- **Fast and Lightweight:** Uses optimized Bash functions (*awk*, *sed*, *grep*) to scan the system in seconds.

- **Secure:** Runs in Read-Only mode. **The script does not make any changes** to the system; it only audits and reports.

- **Modular:** If you do not use a specific service (e.g., Nftables), the script smartly ignores those controls, marking them as N/A (Not Applicable).

- **CSV Export:** At the end of the audit, it automatically generates a *.csv* report ready to be imported into Excel to present to management or compliance teams.

# Prerequisites
- A machine/VM running **Debian 12**.

- **SSH** access to the Linux machine.

- **Root** privileges (via *sudo su* or *su -*) on the Linux machine.

- A client machine (e.g., Windows 10/11) with a terminal (PowerShell or CMD) to send the files.

# Step 1: Prepare the File (Windows / VS Code)
Linux and Windows process line breaks differently. It is crucial to ensure the file is in the correct format before sending it to the server.

**1.** Open the auditoria_cis.sh file in your **Visual Studio Code**.

**2.** Look at the blue bar in the **bottom right corner** of the screen.

**3.** If it says **CRLF** (Windows format), click it and change it to **LF** (Linux format).

**4.** Save the file by pressing *Ctrl + S*.

**5.** Take note of the folder where you saved the file (e.g., Desktop).

# Step 2: Send the Script to the Linux Server
We will securely transfer the file from your Windows computer to the Linux machine using the scp command.

**1.** Open **Windows PowerShell**.

**2.** Navigate to the folder where you saved your script. For example, if it's on your Desktop:
*cd Desktop*

**3.** Run the following command, replacing *USERNAME* with your Linux username and *SERVER_IP* with your machine's IP address:
*scp auditoria_cis.sh USERNAME@SERVER_IP:/home/USERNAME/*

**4.** Enter your password when prompted. You should see the transfer reach 100%. 

# Step 3: Connect to the Server and Execute
**1.** In the same PowerShell window, access your Linux server via SSH:
*ssh USERNAME@SERVER_IP*

**2.** After logging in, elevate your privileges to **root**. This is absolutely mandatory, as the script needs to read protected system files (like */etc/shadow* and firewall configurations): 
*sudo su*
(Your terminal prompt should change to something like *root@server:~#*)

**3.** Navigate to the directory where the script was uploaded in Step 2:
*cd /home/USERNAME/*

**4** Apply execution permissions and a preventive "vaccine" (to remove any hidden Windows carriage return characters *\r* that might have slipped through):
*sed -i 's/\r//g' auditoria_cis.sh
chmod +x auditoria_cis.sh*

**5.** Run the script! Use the *less -R* command so you can scroll through the results at your own pace while keeping the color formatting:
*./auditoria_cis.sh | less -R*

# Step 4: Export and Analyze Results
Use your **Up/Down arrow keys** to read through the results of each section.

When you reach the end (or if you want to exit midway), press the *q* key.

The script will display a **Summary** and prompt you with:
*Do you want to export the results to a CSV file? (Y/N):*

Type *Y* and hit *Enter*.

The script will generate a *.csv* file in the current directory (e.g., */home/USERNAME/relatorio_auditoria.csv*).

**BONUS:** If you want to copy this CSV file back to your Windows PC to open it in Excel, open a new PowerShell window on your PC and run:
*scp USERNAME@SERVER_IP:/home/USERNAME/relatorio_auditoria.csv C:\Users\YOUR_WINDOWS_USER\Desktop\*

# Extraction and Analysis of Results (CSV)
The script allows you to export the audit results to a clean CSV file. To analyze the data correctly on a Windows machine, follow these steps:

**Transfer the file via SCP**
In your local machine's terminal (Windows PowerShell or Linux/macOS command prompt), use the *scp* command to pull the file. **Do not run this command within the SSH session.**

*#Navigate to the desired destination folder (e.g., Desktop)*
**cd Desktop**

*#Download the file (replace the IP with the server address)*
**scp fogadmin@<IP_DA_VM>:/home/fogadmin/relatorio_auditoria.csv .**

# Open in Microsoft Excel
To avoid formatting errors (text clustered in the first column):

**1.** Open a new document in Excel.

**2.** Go to **Data -> From Text/CSV.**

**3.** Select the *relatorio_auditoria.csv* file.

**4.** Ensure that the selected **Delimiter** is **Semicolon** (*;*).

**4.** Click **Load**.

The table will be automatically generated with aligned headers, allowing you to easily filter the *[PASSED]*, *[FAILED]*, and *[MANUAL]* statuses.

# Understanding the Control Classifications
During the audit, you will see colored output in the terminal representing four possible states:

🟢 **[PASSED]:** The control is configured according to the CIS Benchmark best practices. The system is secure in this aspect.

🔴 **[FAILED]:** The control failed. There is a vulnerability, an improperly open port, or a loose configuration that should be addressed.

🟡 **[MANUAL]:** Requires human intervention. The script cannot automatically validate this check (e.g., reviewing local user accounts, confirming allowed IPs in the firewall, testing physical security).

🟡 **[N/A]:** Not Applicable. Example: If you are using the *UFW* firewall, the specific controls meant for *iptables* or *nftables* will be bypassed and marked as N/A.

# Troubleshooting
**Error:** *Connection timed out* **in PowerShell**

- **Cause:** The Linux server's IP address has changed, or the machine is turned off.

- **Solution:** Go to your Linux server's console (e.g., inside VirtualBox), log in, and run the *ip a* command to find out the new IP address. Use this new IP in your *scp* and *ssh* commands.

**Error:** *Permission denied* **when running the script**

- **Cause:** You do not have the correct privileges, or the script lacks execution permissions.

- **Solution:** Ensure you ran *sudo su* (or logged in as *root*) and ran the command *chmod +x auditoria_cis.sh*.

**Error: Strange commands or unrecognized syntax** (e.g., *\r: command not found*)

- **Cause:** The file was saved with hidden Windows line-break formatting.

- **Solution:** Run the Linux formatting "vaccine" command: *sed -i 's/\r//g' auditoria_cis.sh*

  # Built based on the Center for Internet Security (CIS) standards for Debian Family Linux.
