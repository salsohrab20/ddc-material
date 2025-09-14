# Linux for DevOps – Comprehensive Notes

## 1. Introduction

* Linux is the **foundation of DevOps** and essential for any IT professional.
* If you’re already a Linux administrator, you may skip, but for everyone else, mastering Linux is critical.
* This course is designed with **efficiency in mind**: learn a lot of Linux in a short time.

---

## 2. Course Coverage

You will learn about:

1. **Basic Commands** – copy, move, directories, editing files.
2. **File System & Permissions** – understanding structure, file types, permissions.
3. **Filters & Redirection** – crucial for DevOps.
4. **Users & Groups** – management and `sudo`.
5. **Software Management** – RPM, Debian, Yum, Apt, etc.
6. **Services & Processes** – monitoring and management.
7. **Server Management** – setup web servers, database servers.

Focus Areas: **Commands, Files, Software, Servers**

---

## 3. Open Source & Linux History

* **Open Source**:

    * Not just free software; it’s about **freely available source code**.
    * Anyone can inspect, modify, enhance, and redistribute.
    * Large community → more contributions, better quality, more secure.
* **Linux Origins**:

    * **1984** – GNU utilities released (Unix-like, GPL license).
    * **1991** – Linus Torvalds created the **Linux Kernel** (Unix-like, open source).
    * Community built utilities → complete operating systems (distros).
* Today’s Linux:

    * **Kernel** is common (Linux Kernel).
    * Distros differ in utilities, package management, stability/security.
    * Examples: **Ubuntu, Debian, Red Hat, CentOS, Fedora, openSUSE, Android**.

---

## 4. Linux Principles

1. **Everything is a File** – hardware, devices, processes.
2. **Small, Single-purpose Programs** – combine with pipes for complex tasks.
3. **Avoid Captive UI** – no dependency on GUI prompts.
4. **Configuration in Text Files** – easy to edit and automate.
5. **Automation Friendly** – powerful scripting, cron jobs, pipelines.
6. **Secure by Design** – widely used in servers, supercomputers.

---

## 5. Why Linux?

* Open source → strong community support.
* Works on wide variety of hardware (phones, laptops, servers).
* High **customization** and **automation** capabilities.
* Majority of servers and supercomputers run Linux.
* Considered more **secure** compared to Windows (though debatable).

---

## 6. Linux Architecture

* **Hardware** → CPU, RAM, Network, etc.
* **Kernel** → Core, communicates with hardware.
* **Shell** → User interface to interact with kernel (CLI).
* **Tools/Programs** → Commands (`ls`, `cd`, `cat`) or applications (browsers, readers).

---

## 7. Linux Distributions (Distros)

### Desktop Distros

* **Ubuntu** (most popular, user-friendly).
* **Linux Mint** (easy to use).
* **Fedora**, **Debian**, **openSUSE**, **Arch Linux**.

### Server Distros

* **Red Hat Enterprise Linux (RHEL)** – stable, secure, licensed.
* **Ubuntu Server** – open source, user-friendly.
* **CentOS** – RHEL-like but open source.
* **SUSE Enterprise Linux** – enterprise-grade.

### Packaging Families

* **RPM-based**: RHEL, CentOS, Fedora, openSUSE, Amazon Linux.
* **Debian-based**: Debian, Ubuntu, Kali Linux, Linux Mint.

---

## 8. RPM vs Debian (Key Differences)

* **Package Format**:

    * RPM (`.rpm`) → installed with `rpm -ivh <package.rpm>`.
    * Debian (`.deb`) → installed with `dpkg -i <package.deb>`.
* **Repositories**:

    * RHEL-based distros → controlled, tested software (stable & secure).
    * Debian-based distros → wider, faster access to latest software.
* **Use Cases**:

    * **RHEL** → stable production servers.
    * **Debian/Ubuntu** → automation, DevOps, flexibility.

---

## 9. Linux Directory Structure

| Directory                               | Purpose                                         |
| --------------------------------------- | ----------------------------------------------- |
| `/root`                                 | Home directory of root (admin) user             |
| `/home/<username>`                      | Home directory of normal users                  |
| `/bin`, `/usr/bin`, `/usr/local/bin`    | User commands (`ls`, `cat`, etc.)               |
| `/sbin`, `/usr/sbin`, `/usr/local/sbin` | Admin/system commands (`useradd`, `rpm`, etc.)  |
| `/media`, `/mnt`                        | Mounted external devices (USB, CD)              |
| `/etc`                                  | Configuration files (network, user, services)   |
| `/tmp`                                  | Temporary files (cleared on reboot)             |
| `/boot`                                 | Kernel and boot loader                          |
| `/var`, `/srv`                          | Server data (`/var/www/html`, `/var/lib/mysql`) |
| `/proc`, `/sys`                         | System information, process details             |
| `/lib`, `/usr/lib`, `/usr/local/lib`    | Shared libraries                                |

---

## 10. Linux Command Cheatsheet

### 🔹 File & Directory Management

```bash
pwd              # Show current directory
ls               # List files
ls -l            # Detailed listing
ls -a            # Show hidden files
cd <dir>         # Change directory
mkdir <dir>      # Create new directory
rmdir <dir>      # Remove empty directory
rm -rf <dir>     # Remove directory with contents
cp file1 file2   # Copy file
mv file1 file2   # Move or rename file
touch file.txt   # Create empty file
```

### 🔹 Viewing & Editing Files

```bash
cat file.txt         # View file contents
less file.txt        # Scrollable view
head -n 10 file.txt  # First 10 lines
tail -n 10 file.txt  # Last 10 lines
nano file.txt        # Edit with nano editor
vi file.txt          # Edit with vi editor
```

### 🔹 Permissions & Ownership

```bash
chmod 755 file     # Change permissions
chmod u+x file     # Add execute permission to user
chown user:group file # Change file owner
```

### 🔹 File Search & Filters

```bash
find / -name file.txt        # Find file by name
grep "text" file.txt         # Search text in file
grep -r "pattern" /dir       # Recursive search
sort file.txt                # Sort file contents
uniq file.txt                # Remove duplicate lines
wc -l file.txt               # Count lines
```

### 🔹 Redirection & Pipes

```bash
command > file.txt       # Redirect output to file
command >> file.txt      # Append output to file
command < input.txt      # Take input from file
cmd1 | cmd2              # Pipe output to another command
```

### 🔹 User & Group Management

```bash
whoami                  # Show current user
id                      # Show user ID and groups
adduser username        # Add new user
passwd username         # Set user password
groupadd groupname      # Add group
usermod -aG group user  # Add user to group
```

### 🔹 Process & Services

```bash
ps aux                  # Show running processes
top                     # Monitor system processes
kill -9 <pid>           # Kill process by ID
systemctl start service # Start service
systemctl stop service  # Stop service
systemctl status service # Check service status
```

### 🔹 Package Management

**Debian/Ubuntu:**

```bash
apt update              # Update repo info
apt install pkg         # Install package
apt remove pkg          # Remove package
dpkg -i pkg.deb         # Install .deb package
```

**RHEL/CentOS:**

```bash
yum install pkg         # Install package
yum remove pkg          # Remove package
rpm -ivh pkg.rpm        # Install .rpm package
```

### 🔹 Networking

```bash
ifconfig               # Show network interfaces
ip a                   # Show IP addresses
ping google.com        # Check connectivity
curl http://site.com   # Fetch data from URL
wget http://site.com   # Download file
scp file user@host:/path # Copy file via SSH
ssh user@host          # Connect to remote server
```

---

## 11. Key Takeaways

* Linux is **mandatory knowledge** for DevOps professionals.
* Understand **commands, filesystem, software mgmt, server mgmt**.
* Be familiar with **RPM vs Debian packaging**.
* Master Linux **principles, automation, and directory structure**.
* Use the **command cheatsheet** daily until commands become natural.

---
 also want me to extend this cheatsheet with **DevOps-specific commands** (like `docker`, `kubectl`, `systemctl`, `journalctl`) so that it directly aligns with DevOps workflows?
