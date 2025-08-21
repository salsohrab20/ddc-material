### **Installing Jenkins on an EC2 Instance (Ubuntu)**
https://www.jenkins.io/
#### **Overview**

In this lecture, we'll walk through **installing Jenkins** on an **AWS EC2 instance running Ubuntu**. We'll cover:

* Why Ubuntu is preferred
* Required system resources
* Step-by-step setup: EC2 + Jenkins + Java
* Jenkins configuration and access

---

### **Step 1: Understand Prerequisites**

* Jenkins can run on **Linux, macOS, Windows, Docker, and Kubernetes**.
* We'll use **Ubuntu Server 24.04 LTS**.
* Jenkins itself needs **256 MB RAM**, but builds typically require **at least 1 GB**.
* Combined with OS requirements, your EC2 instance should have **at least 2 GB RAM**. We’ll use a **t2.small** instance.

---

### **Step 2: Prepare Installation Commands**

Before launching EC2, prepare these commands:

1. **Install JDK 21**

   ```bash
   sudo apt update
   sudo apt install openjdk-21-jdk -y
   ```

2. **Add Jenkins Repository & Install**

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
   ```

---

### **Step 3: Launch EC2 Instance**

1. Go to AWS EC2 → **Launch instance**
2. Name: `Jenkins-Server`
3. AMI: **Ubuntu Server 24.04 LTS**
4. Instance Type: **t2.small** (2 GB RAM)
5. Key Pair: Create and download (e.g., `jenkins-serverkey.pem`)
6. Security Group:

    * **SSH (port 22)**: Your IP
    * **Jenkins (port 8080)**: Your IP

Then launch the instance.

---

### **Step 4: Connect and Install Jenkins**

1. SSH into your instance:

   ```bash
   ssh -i /path/to/jenkins-serverkey.pem ubuntu@<EC2-PUBLIC-IP>
   ```

2. Run installation commands (from Step 2)

3. Verify Jenkins:

   ```bash
   systemctl status jenkins
   ```

4. Jenkins home directory:

   ```
   /var/lib/jenkins
   ```

   This contains config files, workspace, and job data (except logs, which are in `/var/log/jenkins`).

---

### **Step 5: Access Jenkins in Browser**

* Visit: `http://<EC2-PUBLIC-IP>:8080`
* You'll see the **"Unlock Jenkins"** page.
* Get the initial admin password:

  ```bash
   cat /var/lib/jenkins/secrets/initialAdminPassword
  ```

---

### **Step 6: Initial Setup**

1. **Unlock Jenkins** using the password
2. Choose **"Install suggested plugins"**
    * You can also manually review popular plugins (e.g., Node.js, Git, Maven)
3. Create admin user
4. Set **Jenkins URL**
    * ⚠️ EC2 public IP changes on restart
    * To avoid performance issues, set a **fake domain name** (e.g., `jenkins.example.local`)
        * This avoids Jenkins referencing a stale IP after reboot

---

### **Step 7: Jenkins Dashboard**

* Access: `Manage Jenkins → Configure System`
* Modify Jenkins URL anytime
* All plugins and configurations can be managed here
* We'll explore **agents, plugins, builds**, and more in later lectures

---

### **Important Tips**

* Always **shut down** the EC2 instance when not in use (to avoid unnecessary billing)
* Use `t2.small` or higher for running actual builds
* **Plugins power Jenkins** – features depend on what's installed

---

### **Next Steps**

Continue to the next lecture, or shut down your EC2 instance for now. See you soon!

---

# 🧰 Jenkins: Freestyle Jobs vs Pipeline as Code

## 📌 What Are Jobs in Jenkins?

* **Jobs** in Jenkins represent units of work 🛠️ (like automation scripts in Bash).
* Jenkins runs these jobs to accomplish tasks like build, test, and deploy 🚀.

## 🎛️ Freestyle Jobs

* **Graphical UI-based jobs** created through a form in Jenkins 📝.
* Simple to create for learning purposes 🧪.
* **Each step (fetch code, build, test, deploy)** requires a separate job ➕.
* Jobs must be **manually connected** to form a complete pipeline 🔗.
* ❌ **Not scalable or maintainable** for real-world projects.
* ✅ Best for beginners to **explore Jenkins concepts**.

## 🧾 Pipeline as Code

* Entire pipeline is defined in a **Groovy script** within a Jenkins job 💻.
* Supports **version control** (e.g. Git) since it's text-based 🗂️.
* Easier to **replicate and maintain** across projects or Jenkins instances 🔄.
* Aligned with modern **DevOps practices** 🧑‍💻 where everything is treated as code:

   * Infrastructure as Code (IaC)
   * CI/CD as Code
* ✅ **Recommended** for real-time and production environments.

## 🔍 Summary Comparison

| 🔸 Feature      | 🧩 Freestyle Job           | 📜 Pipeline as Code     |
| --------------- | -------------------------- | ----------------------- |
| Interface       | 🖱️ Graphical (form-based) | 💻 Code (Groovy script) |
| Maintenance     | 🧍 Manual                  | 🔁 Automated & reusable |
| Reusability     | ❌ Low                      | ✅ High                  |
| Version Control | 🚫 No                      | 🗃️ Yes                 |
| Best For        | 🧪 Learning Jenkins basics | 🚀 Real-world CI/CD     |

> ⚠️ **Note:** Before diving into Pipeline as Code, it's helpful to explore Jenkins using Freestyle Jobs first.

---

# 🔧 Jenkins Tools: Plugins & System Installations

## 🧱 Building a Project in Jenkins

To build a project like **VProfile**, you typically need:

* 🧬 **Git** – to clone the source code
* ⚙️ **Maven** – to build the project
* ☕ **JDK (Java)** – required by Maven to compile Java code

---

## 🔍 Where Are These Tools Used?

* While **Jenkins UI** (frontend) lets you configure tools via plugins,
* **Actual execution** happens on the **OS level** (e.g., Linux Ubuntu backend) using real system commands.

> 💡 **Always remember**: Jenkins requires both the **plugin (UI integration)** and the **actual tool installed on the OS**.

---

## ⚙️ Tool Installation Approaches

### 1️⃣ System-Level Installation

* Use terminal/SSH to install tools directly:

  ```bash
  sudo apt install maven
  sudo apt install openjdk-17-jdk
  ```
* Used when Jenkins can't install the tool automatically (e.g., custom JDK versions).

### 2️⃣ Jenkins UI Installation (Manage Jenkins → Tools)

* Available tools depend on the plugins installed.
* Examples:

   * ✅ JDK
   * ✅ Maven
   * ✅ Git
   * ✅ Gradle
   * ✅ Node.js

> 💡 These options appear based on installed **plugins** (e.g., Node.js plugin).

---

## 📦 Adding Maven in Jenkins

1. Go to: `Manage Jenkins → Global Tool Configuration`
2. Under **Maven**, click **Add Maven**
3. Name it (e.g., `MAVEN3.9`)
4. ✅ Check **Install automatically**
5. Choose version: `3.9.9` (✅ Compatible with VProfile)

---

## ☕ Adding JDK 17 (Manual Method)

1. SSH into Jenkins machine:

   ```bash
   ssh user@<Jenkins-IP>
   sudo -i
   apt install openjdk-17-jdk -y
   ```

2. Find the JDK installation path:

   ```bash
   ls /usr/lib/jvm/
   ```

   Example path:

   ```
   /usr/lib/jvm/java-17-openjdk-amd64
   ```

3. In Jenkins UI:

   * Go to: `Manage Jenkins → Global Tool Configuration`
   * Add JDK → Name: `JDK17`
   * Uncheck auto-installation
   * Paste the path in **JAVA\_HOME** field

---

## 🧠 Key Takeaways

* ✅ **Both plugin & actual tool installation** are required.
* 🔧 Use **UI for simpler tools** (like Maven).
* 🖥️ Use **SSH & terminal for custom setups** (like JDK 17).
* 📚 Tools depend on the **plugins you've installed**.

---

## 📝 Your Tasks

* [ ] Install **Maven 3.9.9** via Jenkins UI
* [ ] Install **JDK 17** via terminal and configure JAVA\_HOME in Jenkins
* [ ] Explore Jenkins settings:

   * 🔍 Tools
   * 🧩 Plugins
   * 🌐 Nodes
   * 🛠️ Global Config

> 🎯 We’ll explore each of these sections hands-on throughout the course. Stay curious and keep experimenting!

---
