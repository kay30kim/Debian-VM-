# Project Guide 
#### Through this project, I not only expanded my knowledge of Debian and operating systems but also significantly enhanced my professionalism in managing firewalls and SSH security measures.
## Table of Contents
1. [Installation](#part-1---installation)
2. [Background Knowledge](#part-2---background-knowledge)
	- [Part 2.1 What is VM & LVM](#part-21-what-is-vm--lvm)
	- [Part 2.2 Rocky & Debian](#part-22-rocky--debian)
	- [Part 2.3 Aptitude & Apt](#part-23-aptitude--apt)
	- [Part 2.4 AppArmor & SELinux](#part-24-apparmor--selinux)
3. [Part 3 - Password Rule](#part-3---password-rule)
4. [Part 4 - USER & GROUP](#part-4---user--group)
5. [Part 5 - UFW & SSH](#part-5---ufw--ssh)
6. [Part 6 - Configuration](#part-6---configuration)
7. [Part 7 - monitoring.sh](#part-7---monitoringsh)

## Part 1 - Installation
At the time of writing, the latest stable version of [Debian](https://www.debian.org) is *Debian 11 Buster*.

## Part 2 - Background Knowledge
### Part 2.1 What is VM & LVM
The words may appear similar, but these two are entirely distinct concepts.
*VMs enable you to run multiple operating systems* on a single physical machine, while *LVM* simplifies and *optimizes the management of storage resources* in a flexible and *efficient manner*.

1. VM (Virtual Machine):
- A Virtual Machine is a software-based emulation of a physical computer.
- It allows you to run multiple operating systems on a single physical machine.
- VMs are isolated from each other and share the hardware resources of the host machine.
- Popular virtualization software includes VMware, VirtualBox, and Hyper-V.

2. LVM (Logical Volume Manager):
- LVM is a storage management system in Linux that allows for flexible and efficient disk storage management.
- It abstracts physical storage devices into logical volumes, making it easier to manage storage.
- LVM provides features like dynamic resizing, snapshots, and striping for improved data management.
- It consists of physical volumes (disks or partitions), volume groups, and logical volumes (filesystems).

#### How it works:
- VMs: Virtualization software creates a virtual environment that simulates a real computer, complete with its own operating system and applications. The physical hardware is shared among multiple VMs, and each VM operates independently, unaware of the others.
- LVM: LVM works by managing storage in three layers - physical volumes, volume groups, and logical volumes. Physical disks or partitions are added to a volume group, and logical volumes are created from the volume group. This allows for flexible resizing and allocation of storage space without affecting the underlying physical storage.

#### Therefore, using LVM offers the following benefits:
1. Easy expansion of disk space.
2. Aggregation of multiple disk spaces for efficient use, optimizing disk partition space effectively.

### Part 2.2 Rocky & Debian

| Aspect                | Rocky Linux                     | Debian                    |
|-----------------------|---------------------------------|---------------------------|
| Origin                | CentOS alternative              | Independent               |
| Base                  | Built on RHEL source            | Independent               |
| Package Management    | RPM (Red Hat Package Manager)   | APT(Advanced Package Tool)|
| Release Cycle         | Stable, enterprise-like         | Multiple branches         |
| Community & Philosophy| Focused on compatibility        | Open-source commitment    |
| Use Cases             | Enterprise, RHEL compatibility  | Versatile, wide use       |

I choose Debian for its strong commitment to *open source, stability, diverse package options, and a large, supportive community*. It's a versatile choice suitable for various use cases, from servers to personal desktops.

#### To check OS version
```
cat /etc/os-release
```

### Part 2.3 Aptitude & Apt

> Apart from main difference being that Aptitude is a high-level package manager while APT is lower-level package manager which can be used by other higher-level package managers, other main highlights that separate 

Aspect              | Apt                    | Aptitude                   |
------------------- | ---------------------- | ---------------------------|
Dependencies        | Automatically resolved | More fine-grained control  |
Installation        | sudo apt install       | sudo aptitude install      |
Removal             | sudo apt remove        | sudo aptitude remove       |

### Part 2.4 AppArmor & SELinux
These *security systems* provide tools to isolate applications from each other and in turn isolate an attacker from the rest of the system when an application is compromised.

> AppArmor is application-centric, simpler to set up, and focuses on specific applications' security. SELinux is system-wide, more powerful, and complex, emphasizing strict security policies across the entire system, which can be challenging for beginners.

AppArmor is a security framework for Linux that enhances the security of applications and processes. AppArmor uses profiles to define how processes can access system resources, and these profiles are configured in different modes, each serving a specific purpose:

- Enforce Mode: In this mode, the security policies defined in the AppArmor profiles are strictly enforced. Processes must adhere to the rules specified in the profile, and any attempts to violate the policy are blocked. Enforce mode is used in actual security environments to prevent potential security threats.

- Complain Mode: Complain mode records violations of the AppArmor profile rules but does not enforce them. Processes can potentially breach the policy, but these instances are logged for analysis. System administrators can use this information to fine-tune the profiles. Complain mode is useful for profile development and debugging.

## Part 3 - Password Rule
> • To set up a strong password policy, you have to comply with the following requirements:
> • Your password has to expire every 30 days.
> • The minimum number of days allowed before the modification of a password will be set to 2.
> • The user has to receive a warning message 7 days before their password expires.
> • Your password must be at least 10 characters long. It must contain an uppercase letter, a lowercase letter, and a number. Also, it must not contain more than 3 consecutive identical characters.
> • The password must not include the name of the user.
> • The following rule does not apply to the root password: The password must have at least 7 characters that are not part of the former password.
> • Of course, your root password has to comply with this policy

```
sudo vi /etc/login.defs
:160
sudo vi /etc/pam.d/common-password
sudo visudo
```


## Part 4 - USER & GROUP
> • The hostname of your virtual machine must be your login ending with 42 (e.g.,wil42). You will have to modify this hostname during your evaluation.
> • You have to implement a strong password policy.
> • You have to install and configure sudo following strict rules.
> • In addition to the root user, a user with your login as username has to be present.
> • This user has to belong to the user42 and sudo groups
```
hostnamectl
hostnamectl set-hostname 새로운호스트이름
sudo nano /etc/hosts
sudo reboot

사용자 더하는거
sudo adduser 사용자이름
sudo userdel 사용자이름

사용자 체크
/etc/passwd | grep 사용자이름
= getent passwd (사용자이름)

사용자에 그룹 더하는거
sudo adduser 사용자이름 그룹이름
=sudo usermod -aG sudo(그룹이름) 사용자이름

그룹 더하는거
=sudo groupadd 그룸이름

groups 사용자이름
=getent 그룸이름

```

## Part 5 - UFW & SSH
> A SSH service will be running on port 4242 only. For security reasons, it must not be possible to connect using SSH as root
> I had to configure your operating system with the UFW (or firewalld for Rocky) firewall and thus leave only port 4242 open.
```
sudo vi /etc/ssh/sshd_config
:15
:34

dpkg -l | grep ssh
sudo service ssh status

dpkg -l | grep ufw

sudo ufw allow 포트숫자
sudo ufw status
sudo ufw delete 번호(포트숫자아님)
sudo ufw status
sudo systemctl restart ssh

밖에서 접속
ssh kay@localhost -p 4242
```

## Part 6 - Configuration
> • Authentication using sudo has to be limited to 3 attempts in the event of an incorrect password.
> • A custom message of your choice has to be displayed if an error due to a wrong password occurs when using sudo.
> • Each action using sudo has to be archived, both inputs and outputs. The log file has to be saved in the /var/log/sudo/ folder.
> • The TTY mode has to be enabled for security reasons.
> • For security reasons too, the paths that can be used by sudo must be restricted.
```
sudo nano /etc/sudoers
> Defaults     secure_path="..."
> Defaults     passwd_tries=3

> Defaults     badpass_message="Password is wrong, please try again!"

> Defaults	logfile="/var/log/sudo/sudo.log"
> Defaults	log_input,log_output

> Defaults        requiretty
```
Require tty: (Why use tty? If some non-root code is exploited (a PHP script, for example), the requiretty option means that the exploit code won't be able to directly upgrade its privileges by running sudo.)

## Part 7 - monitoring.sh
*Monitoring.sh.*
It must be developed in bash. At server startup, the script will display some information (listed below) on all terminals every 10 minutes (take a look at wall). The banner is optional. No error must be visible.

Your script must always be able to display the following information:
• The architecture of your operating system and its kernel version.
• The number of physical processors.
• The number of virtual processors.
• The current available RAM on your server and its utilization rate as a percentage.
• The current available memory on your server and its utilization rate as a percentage.
• The current utilization rate of your processors as a percentage.
• The date and time of the last reboot.
• Whether LVM is active or not.
• The number of active connections.
• The number of users using the server.
• The IPv4 address of your server and its MAC (Media Access Control) address.
• The number of commands executed with the sudo program

```
vi /usr/local/bin/monitoring.sh

$ sudo visudo
사용자이름 ALL=(ALL) NOPASSWD: /usr/local/bin/monitoring.sh
```
