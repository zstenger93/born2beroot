<h1 align="center">
	✏️ Born2beroot
</h1>

<p align="center">
	<b><i>You can do anything you want to, this is your world!</i></b><br>
</p>

<p align="center">
	<img alt="GitHub code size in bytes" src="https://img.shields.io/github/languages/code-size/zstenger93/born2beroot?color=lightblue" />
	<img alt="Code language count" src="https://img.shields.io/github/languages/count/zstenger93/born2beroot?color=yellow" />
	<img alt="GitHub top language" src="https://img.shields.io/github/languages/top/zstenger93/born2beroot?color=blue" />
	<img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/zstenger93/born2beroot?color=green" />
</p>

---

## 💡 About the project

> _This project aims to introduce you to the wonderful world of virtualization._

	You will create your first machine in VirtualBox (or UTM if you can’t use VirtualBox)
under specific instructions. Then, at the end of this project, you will be able to set up
your own operating system while implementing strict rules.

---

## Table of Contents
1. [OS Installation](#step-1-installing-manually-the-chosen-os)
2. [*sudo*](#installing-sudo)
3. [SSH](#ssh)
4. [UFW](#ufw)
5. [User Management](#user-management)
6. [*cron*](#install-cron-and-set-up-a-cron-job)

---

### Mandatory part

## *sudo*

### Step 1: Installing manually the chosen OS

*	Think beforhand if you want to do the bonus or not, you need to setup according to that

*	First you need to install manually the OS of your choice. The latest stable Debian or CentOS.

*	Create the partitions with LVM ad setup the partitions accordingly to the displayed path.

*	Set the install folder to goinfre

*	Setup the hostname and password correctly

*	Carefully set up your partitions with the correct path and sizes

*	Create at least 2 encrypted partitions

---

### Step 2: Installing *sudo*

Switch to *root* and its environment via `su -`:
```
$ su -
Password:
#
```
Install *sudo* via `apt install sudo`:
```
# apt install sudo
```
Verify whether *sudo* was successfully installed via `dpkg -l | grep sudo`:
```
# dpkg -l | grep sudo
```
### Step 3: Adding user to the sudo group
Add user to *sudo* group via `adduser <username> sudo`:
```
# adduser <username> sudo
```
>Or, add user to *sudo* group via `usermof -aG <username>`
>```
># usermod -aG sudo <username>
>```
To verify if it is added:
```
# reboot
<--->
Debian GNU/Linux 10 <hostname> tty1

<hostname> login: <username>
Password: <password>
<--->
$ sudo -v
[sudo] password for <username>: <password>
```

### Step 4: Running *root* Commands from now on you can
Add the prefix `sudo` eg:
```
$ sudo apt update
```

### Step 5: Configuring *sudo*
The filename cannot contain `~` or `.`:
```
$ sudo vi /etc/sudoers.d/<filename>
```
Setting limits to authentication --defaults 3-- in case of incorrect password:
```
Defaults		passwd_tries=3
```
Adding a custom error message if you typed wrong pw:
```
Defaults		badpass_message="Do you have memory leaks? Wrong password!"
```

###
Setup to log every *sudo* command to this path:
```
$ sudo mkdir /var/log/sudo/<filename>
<~~~>
Defaults		logfile="/var/log/sudo/<filename>"
<~~~>
```
The following line to archive all *sudo* inputs to this file:
```
Defaults		log_input,log_output
Defaults		iolog_dir="/var/log/sudo"
```
Require *TTY*:
```
Defaults		requiretty
```
Setup the *sudo* path:
```
Defaults		secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

## SSH

### Installing & Configuring
Install the openssh server:
```
$ sudo apt install openssh-server
```
Check if it's installed:
```
$ dpkg -l | grep ssh
```
Config SSH via:
```
$ sudo vi /etc/ssh/sshd_config
```
Setup the port as required to 4242 replace the:
```
13 #port 22
```
With:
```
13 #port 4242
```
Disable SSH login as *root* no matter what replace the line:
```
32 #PermitRootLogin prohibit-password
```
With:
```
32 #PermitRootLogin no
```
Check the status of SSH via:
```
$ sudo service ssh status
```

## UFW

### Installing & Configuring UFW
Install UFW via:
```
$ sudo apt install ufw
```
Check if it went well:
```
$ dpgk -l | grep ufw
```
Enable the firewall:
```
$ sudo ufw enable
```
Allowing connections only via 4242 port:
```
$ sudo ufw allow 4242
```
Check the status with:
```
$ sudo ufw status
```

### Connecting to Server via SSH
SSH into your virtual machine using the port you have done the setup for:
```
$ ssh <username>@<ip-address> -p 4242
```
You can check for the ip address with:
```
$ ip addr
```
Or:
```
$ hostname -I
```
Logout to terminate the session at any time with:
```
$ logout
```

## User Management

### Implement a strong password policy

#### Password age
Config pw policy via:
```
$ sudo vi /etc/login.defs
```
Make it expire every 30 days change line 160 to:
```
160 PASS_MAX_DAYS	30
```
Minimum days between changes set to 2:
```
161 PASS_MIN_DAYS	2
```
Send a warning 7 days before it would expire:
```
162 PASS_WARN_AGE	7
```

#### Password strength
For setting up these policies you need to install:
```
$ sudo apt install libpam-pwquality
```
Check if it's installed:
```
$ dpgk -l | grep libpam-pwquality
```
Now you can go and config the pw strength policy at:
```
$ sudo vi /etc/pam.d/common-password
```
At line 25 which is:
```
25 password			requisite						pam_pwquality.so retry=3
```
`Add these changes to the end of the line:`
To set the min length of the pw:
```
minlen=10
```
Req to contain at least one lower, uppercase and one numeric char:
```
lcredit=-1 ucredit=-1 dcredit=-1
```
It cannot contain more than 3 consecutive identical chars with:
```
maxrepeat=3
```
Reject the pw if it contains the username in some form:
```
reject_username
```
To set how many chars have to be different compared to the old pw:
```
difok=7
```
Give the same policy to root:
```
enforce_for_root
```

### Creating a new user
Create via:
```
$ sudo adduser <username>
```
Check if its added successfully:
```
$ getent passwd <username>
```
Check the new user's pw policy:
```
$ sudo chage -l <username>
```

### Creating a new group
Create the new group with:
```
$ sudo addgroup user42
```
Add the user to this group with:
```
$ sudo adduser <username> user42
```
>Alternatively, add user to *user42* group via `sudo usermod -aG user42 <username>`.
>```
>$ sudo usermod -aG user42 <username>
>``
Check if it's added to the group with:
```
$ getent group user42
```

## *cron*

### Install *cron* and set up a cron job
To install cron:
```
$ sudo apt-get install cron
```
Config *cron* as *root*:
```
$ sudo crontab -u root -e
```
Schedule the script to run every 10 min change line 23 to:
```
23 */10 * * * * sh /path/to/script
```
`path is the way to the script you wrote to run via *cron* job`
Check the scheduled job with:
```
$ sudo crontab -u root -l
```

---

### Bonus

> _If you aren't doing the bonus, pro tip: set up at least the partitions correctly for some extra points & time._

<h1 align="center">
	ERROR 404 bonus not found
</h1>