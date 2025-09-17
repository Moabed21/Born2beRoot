# This guide summarizes the configuration of your Debian server, following the requirements of the Born2beRoot project.

<h2>Chapter 1:Initial Server Setup & Debian Installation </h2>

Goal: Install a minimal, command-line-only Debian server with a secure and flexible disk layout.

Steps Taken:

Virtual Machine Creation: A new virtual machine was created in VirtualBox.

OS Installation: The Debian installer was booted.

Manual Partitioning: We chose the manual partitioning method to create the following custom layout:

/boot Partition: A small, unencrypted primary partition (approx. 500MB) was created to store the kernel and bootloader files.

Encrypted Container: The remaining disk space was allocated to a single partition, which was then configured as a "physical volume for encryption." This created a secure, encrypted container.

LVM Setup: We configured Logical Volume Management (LVM) inside the encrypted container.

A Volume Group (VG) was created to manage all the space within the encrypted container.

Three Logical Volumes (LV) were created inside the Volume Group:

root (/): For the main operating system files.

swap: As virtual memory for the system.

home (/home): For user files.

Formatting: Each logical volume was formatted with the correct file system (ext4 for / and /home, and swap for the swap volume).

Software Selection: During the installation, we made sure to:

DESELECT any graphical desktop environment (like GNOME).

SELECT the SSH server and standard system utilities.

First Boot: After installation, the system was booted. We successfully unlocked the encrypted disk by entering the passphrase and logged in for the first time as the normal user (moabed).

<h2>Chapter 2: Server Hardening & Configuration</h2>

Goal: Secure the server by configuring users, passwords, network access, and a firewall.

Steps Taken:

sudo Configuration:

Problem: The sudo command was not installed by default.

Solution: We logged in as root (using su -), installed the package (apt install sudo), and added our user to the sudo group (usermod -aG sudo moabed). We then logged out and back in for the new permissions to take effect.

AppArmor Verification:

We confirmed that the AppArmor security module was loaded and running using the sudo aa-status command.

Strong Password Policy:

Quality Rules: Installed the libpam-pwquality package. We then edited /etc/pam.d/common-password to enforce rules for password complexity (length, uppercase, lowercase, numbers, etc.).

Aging Rules: We edited /etc/login.defs to set password expiration (PASS_MAX_DAYS=30), minimum change time (PASS_MIN_DAYS=2), and warning period (PASS_WARN_AGE=7).

Application: We applied the new policy to existing users by changing the passwords for both root (sudo passwd root) and our own user (passwd).

SSH Service Hardening:

We edited the SSH configuration file /etc/ssh/sshd_config to:

Change the listening Port from 22 to 4242.

Forbid direct login as the root user by setting PermitRootLogin no.

We restarted the SSH service to apply the changes (sudo systemctl restart ssh).

UFW Firewall Configuration:

Problem: The ufw command was not installed.

Solution: We logged in as root and installed it (apt install ufw).

We configured the firewall with a "default deny" policy (sudo ufw default deny incoming).

We created a specific rule to allow traffic only on our new SSH port (sudo ufw allow 4242).

Finally, we enabled the firewall (sudo ufw enable).

User & Group Management:

We created the required user42 group (sudo addgroup user42).

We added our user to this new group (sudo usermod -aG user42 moabed).

We verified the correct group membership using the groups command.

<h2>Chapter 3: Current Status: Building monitoring.sh</h2>

Goal: Write a script to display system statistics.

Steps Taken So Far:

File Creation: Created monitoring.sh and made it executable (chmod +x).

Script Content: We have successfully written the commands to display:

System Architecture (uname -a).

Physical CPU count (lscpu | grep "Socket(s):" ...).

Virtual CPU count (lscpu | grep "^CPU(s):" ...).

Troubleshooting:

Problem: We needed the bc (Basic Calculator) program to calculate percentages for memory usage, but it was not installed (bc: command not found).

Attempted Solution: We tried to install it with sudo apt install bc.

New Problem Encountered: The apt command failed with Network is unreachable errors. This means the virtual machine has no internet connection.

Current Task:
Your current objective is to fix the virtual machine's internet connection by changing its network setting in VirtualBox from NAT to Bridged Adapter, as described in the previous message. After that, you will be able to install bc and continue building the script.
