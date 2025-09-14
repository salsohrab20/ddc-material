# 📘 CI Pipeline Setup – Step-by-Step Roadmap

---

## 1. 🛠️ **Infrastructure Setup**

We need 3 main servers (on EC2 instances):

* **Jenkins** → CI orchestrator
* **Nexus** → Artifact repository
* **SonarQube** → Code analysis & quality gates

### 🔹 Steps:

1. Launch EC2 instances for each service.
2. Use **User Data Scripts (bash)** for automated installation.

    * Jenkins → Install Jenkins, Java, dependencies
    * Nexus → Install Nexus Repository Manager
    * SonarQube → Install SonarQube server
3. Verify services are running (via browser or CLI).

---

## 2. 🔐 **Networking & Security**

* Configure **Security Groups** to allow communication:

    * Jenkins ↔ GitHub (port 22 for SSH, 443 for webhooks)
    * Jenkins ↔ Nexus (port 8081 by default)
    * Jenkins ↔ SonarQube (port 9000 by default)
* Restrict external access — allow only required IPs / CIDRs.

---

## 3. 🔌 **Install Jenkins Plugins**

Plugins needed for CI pipeline:

* **Git Plugin** → Fetch source code
* **Maven Integration Plugin** → Build Java apps
* **SonarQube Scanner Plugin** → Static code analysis
* **Nexus Artifact Uploader Plugin** → Push artifacts to Nexus
* **Email / Slack Notification Plugin** → Send alerts
* (Optional) **Pipeline Plugin** → For scripted/declarative pipelines

---

## 4. 🔗 **Integrations**

### 🔹 Nexus with Jenkins

* Go to **Manage Jenkins → Credentials**.
* Add **Nexus credentials** (username/password).
* Configure Nexus plugin with:

    * Nexus URL
    * Repository name
    * Credentials ID

### 🔹 SonarQube with Jenkins

* Generate **SonarQube Token** from SonarQube server.
* In Jenkins:

    * **Manage Jenkins → Configure System → SonarQube Servers**
    * Add SonarQube URL + Token.
* Configure **SonarQube Scanner** in Jenkins Global Tools.

---

## 5. 📜 **Pipeline Script**

Write Jenkins Pipeline (Declarative or Scripted):

* **Stage 1**: Fetch code (GitHub)
* **Stage 2**: Build (Maven → `mvn clean package`)
* **Stage 3**: Unit tests (`mvn test`)
* **Stage 4**: Code Analysis (SonarQube Scanner)
* **Stage 5**: Artifact Upload (Nexus → push `.war/.jar`)
* **Stage 6**: Notifications (email/Slack on success/failure)

---

## 6. 🔔 **Notifications**

* Install **Email or Slack plugin**.
* Configure SMTP server (for email) or Slack Webhook.
* Update pipeline:

  ```groovy
  post {
    failure {
      mail to: 'dev-team@example.com',
           subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
           body: "Check console output at ${env.BUILD_URL}"
    }
  }
  ```

---

## 7. 📋 **Key Notes**

* This is the **roadmap**: Setup infra → Configure networking → Install plugins → Integrate tools → Write pipeline → Add notifications.
* Each step builds on the previous one.
* If lost during setup → revisit roadmap to know “where you are.”

---

# 🎤 **Interview Questions**

### 🌐 CI/CD Setup

1. What are the core components of a CI pipeline?
2. Why do we use separate servers for Jenkins, Nexus, and SonarQube?
3. How would you automate server setup for Jenkins/Nexus/SonarQube?

### 🔌 Integrations

4. How do you integrate Jenkins with SonarQube?
5. How do you configure Jenkins to push artifacts to Nexus?
6. What’s the difference between Nexus and SonarQube in a CI pipeline?

### 📜 Pipeline & Notifications

7. What are the typical stages of a Jenkins pipeline?
8. How would you configure notifications for build failures?
9. If a SonarQube Quality Gate fails, how do you stop the pipeline?

### ⚠️ Troubleshooting

10. If Jenkins cannot upload an artifact to Nexus, what would you check first?
11. If SonarQube analysis step fails, how would you debug?
12. What security measures should you follow when integrating multiple tools in a CI pipeline?

---

# 📘 Jenkins CI Pipeline – Nexus & SonarQube Server Setup

---

## 1. 🛠️ **Setup Approach**

* **Automation via User Data Scripts** (bash scripts from GitHub repo).
* Servers to set up:

    * **Nexus** → Artifact repository (Amazon Linux 2023)
    * **SonarQube** → Code quality analysis (Ubuntu 24)
* Dependencies installed inside scripts (JDK, DB, system configs, etc.).

---

## 2. 📦 **Nexus Server Setup (Amazon Linux 2023)**

### Steps:

1. **Launch EC2 Instance**

    * Name: `Nexus Server`
    * OS: **Amazon Linux 2023**
    * Instance Type: **t2.medium / t3.medium**
    * Key pair: Create new (e.g., `nexus-key.pem`)

2. **Security Group Rules**

    * Allow SSH: Port 22 (My IP)
    * Allow HTTP access: Port 8081 (My IP)
    * Allow Jenkins → Nexus access: Port 8081 (Source = Jenkins-SG)

3. **User Data Script** (`nexus-setup.sh`)
> For setup file navigate to https://github.com/hkhcoder/vprofile-project/, checkout to atom branch and userdata folder

    * Installs JDK 17
    * Downloads Nexus binary
    * Creates Nexus user
    * Sets up **systemd service** (`nexus.service`)
    * Starts and enables Nexus service

4. **Verify Nexus**

    * SSH into instance:

      ```bash
      ssh -i nexus-key.pem ec2-user@<Nexus-Public-IP>
      sudo -i
      systemctl status nexus
      java -version
      ```
    * Access in browser: `http://<Nexus-Public-IP>:8081`
    * Default admin password:

      ```bash
      cat /opt/nexus/sonatype-work/nexus3/admin.password
      ```
    * Login → Change password (set to e.g., `admin123`).
    * Enable **anonymous access** for read-only browsing.

---

## 3. 🔍 **SonarQube Server Setup (Ubuntu 24)**

### Steps:

1. **Launch EC2 Instance**

    * Name: `SonarQube Server`
    * OS: **Ubuntu 24**
    * Instance Type: **t2.medium / t3.medium** (needs 4GB RAM)
    * Key pair: Create new (e.g., `sonar-key.pem`)

2. **Security Group Rules**

    * Allow SSH: Port 22 (My IP)
    * Allow HTTP: Port 80 (My IP)
    * Allow Jenkins → SonarQube access: Port 80 (Source = Jenkins SG)

3. **User Data Script** (`sonar-setup.sh`)

    * Installs **JDK 17**
    * Installs and configures **PostgreSQL** (DB for SonarQube)
    * Creates DB user: `sonar` with password `admin123`
    * Downloads and installs **SonarQube** binary
    * Updates `sonar.properties` with DB credentials
    * Adds OS-level performance tuning (`systemctl.conf`, `limits.conf`) - as it is very resource heavy
    * Creates **systemd service** (`sonarqube.service`)
    * Installs **NGINX** as reverse proxy

        * Routes `:80 → :9000` (SonarQube port)
    * Reboots system (required for OS tuning changes)

4. **Verify SonarQube**

    * Access in browser: `http://<SonarQube-Public-IP>` (NGINX on port 80).
    * Login:

        * Username: `admin`
        * Password: `admin` → Change password immediately.
    * Dashboard available for projects, bugs, vulnerabilities, etc.

---

## 4. 🔗 **Networking Between Jenkins, Nexus & SonarQube**

* **Jenkins → Nexus** (port 8081) → upload artifacts.
* **Jenkins → SonarQube** (port 80) → send code analysis reports.
* **SonarQube → Jenkins** (port 8080) → send scan status back.
* Update **Security Groups** accordingly.

---

## 5. 📋 **Key Notes**

* Nexus default port → **8081**
* SonarQube default port → **9000** (proxied via NGINX on **80**)
* Jenkins default port → **8080**
* Always use **t2/t3.medium** (minimum 4GB RAM) for SonarQube.
* Shut down servers when not in use to save costs.

---

# 🎤 **Interview Questions**

### 📦 Nexus

1. What is Nexus used for in a CI/CD pipeline?
2. How do you upload artifacts to Nexus from Jenkins?
3. What’s the default port for Nexus?
4. Where is the default admin password stored after installation?

### 🔍 SonarQube

5. What is SonarQube and why do we use it?
6. How does SonarQube differ from unit testing?
7. Why does SonarQube require PostgreSQL?
8. Why do we configure NGINX in front of SonarQube?

### 🔗 Integration

9. How does Jenkins integrate with SonarQube and Nexus?
10. What networking/security group rules are required for Jenkins ↔ Nexus ↔ SonarQube communication?
11. How do you configure SonarQube Quality Gates in Jenkins pipelines?
12. What OS-level changes are required for SonarQube to run properly on Linux?
---

### Q1: What is a Jenkinsfile and why is it useful?

**Answer:**
A `Jenkinsfile` is a text file that defines a Jenkins pipeline. It enables **Pipeline-as-Code**, meaning pipelines are versioned in source control, reviewable, reusable, and portable. It standardizes CI/CD automation across teams.

---

### Q2: What’s the difference between Declarative and Scripted pipelines?

**Answer:**

* **Declarative** → higher-level, structured syntax, easier to read, recommended.
* **Scripted** → Groovy-based, more flexible but harder to maintain.
  Most projects today use **Declarative** for simplicity.

---

### Q3: What are the key blocks in a Declarative pipeline?

**Answer:**

* `agent` → execution node.
* `tools` → pre-configured tools like Maven, JDK.
* `environment` → variables used in pipeline.
* `stages → stage → steps` → main execution logic.
* `post` → actions after success/failure (notifications, cleanup).

---

### Q4: How do you handle credentials in Jenkinsfile?

**Answer:**

* Store credentials securely in Jenkins **Credentials Manager**.
* Reference them via `withCredentials` block or environment injection.
* Example:

  ```groovy
  withCredentials([string(credentialsId: 'sonar-token-id', variable: 'SONAR_TOKEN')]) {
    sh "mvn sonar:sonar -Dsonar.login=${SONAR_TOKEN}"
  }
  ```

---

### Q5: How do you run SonarQube analysis from a pipeline?

**Answer:**

* Install **SonarQube Scanner plugin**.
* Configure SonarQube server in Jenkins settings.
* In Jenkinsfile, use `withSonarQubeEnv` or pass token via `withCredentials`.
* Example:

  ```groovy
  withSonarQubeEnv('SonarQubeServer') {
    sh 'mvn sonar:sonar'
  }
  ```

---

### Q6: How do you upload artifacts to Nexus in a pipeline?

**Answer:**

* Option 1: Use **Maven deploy** with `distributionManagement` in `pom.xml`.
* Option 2: Use **Nexus Artifact Uploader plugin**.
* Example (plugin step):

  ```groovy
  nexusArtifactUploader(
    nexusVersion: 'nexus3',
    protocol: 'http',
    nexusUrl: 'nexus.example.com:8081',
    repository: 'maven-releases',
    credentialsId: 'nexus-cred-id',
    groupId: 'com.mycompany',
    artifactId: 'myapp',
    version: '1.0.${BUILD_NUMBER}',
    type: 'war',
    file: 'target/myapp.war'
  )
  ```

---

### Q7: How do you collect test reports in a pipeline?

**Answer:**
Use `junit` step to publish test results to Jenkins:

```groovy
post {
  always {
    junit '**/target/surefire-reports/*.xml'
  }
}
```

This gives trends & pass/fail stats in Jenkins UI.

---

### Q8: How do you prevent builds from running on Jenkins master node?

**Answer:**

* Use `agent { label 'build-node' }` to target specific build agents.
* Mark master as “Do not allow builds” in Jenkins node config.

---

### Q9: What is the role of the `post` block in Jenkinsfile?

**Answer:**
It defines actions to run **after stages** (or after the whole pipeline):

* `success` → run only on success.
* `failure` → run only on failure.
* `always` → run regardless (e.g., cleanup).

---

### Q10: How do you debug a failing pipeline stage?

**Answer:**

* Review console output of that stage.
* Add `echo` or `sh 'set -x'` for more logging.
* Use **Replay feature** to rerun with modified Jenkinsfile snippets.
* Check plugin versions & configuration.

---


# 📘 Jenkins Pipeline as Code — Jenkinsfile

---

## 1. 🌐 What is Pipeline-as-Code?

* Pipeline-as-Code is Jenkins’ way of defining CI/CD pipelines in a file called **`Jenkinsfile`**.
* Stored in source control → versioned, reviewed, portable.
* Syntax:

    * **Declarative** (modern, recommended, easier to read).
    * **Scripted** (Groovy-like, more flexible).

---

## 2. 🏗️ Declarative Jenkinsfile Structure

```groovy
pipeline {
  agent any                // where to run
  tools { ... }            // configured tools (Maven, JDK)
  environment { ... }      // environment variables
  stages {
    stage('Name') {
      steps { ... }        // actual commands
      post { ... }         // stage-level post actions
    }
  }
  post { ... }             // pipeline-level post actions
}
```

---

## 3. 🔑 Building Blocks

* **agent** → specifies where to run (`any`, `label`, or docker).
* **tools** → loads tools (Maven, JDK, Sonar Scanner) from *Global Tool Config*.
* **environment** → defines variables like `NEXUS_URL`, `SONAR_HOST`.
* **stages** → logical blocks (Checkout, Build, Test, Deploy).
* **steps** → actual commands (`sh 'mvn test'`, `git branch: ...`).
* **post** → actions after stages/pipeline (success, failure, always).

---

## 4. ✅ Example Jenkinsfile

```groovy
pipeline {
  agent any

  tools {
    maven "Maven 3.9"
    jdk "JDK 17"
  }

  environment {
    NEXUS_URL  = "http://nexus.example.com:8081"
    SONAR_HOST = "http://sonar.example.com"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'atom', url: 'https://github.com/hkcoder/vprofile-project.git'
      }
    }

    stage('Unit Test') {
      steps {
        sh 'mvn test'
      }
      post {
        always {
          junit '**/target/surefire-reports/*.xml'
        }
      }
    }

    stage('Build') {
      steps {
        sh 'mvn clean package -DskipTests'
      }
      post {
        success {
          echo "Build successful — archiving artifact"
          archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
        }
      }
    }

    stage('Code Analysis (SonarQube)') {
      steps {
        withCredentials([string(credentialsId: 'sonar-token-id', variable: 'SONAR_TOKEN')]) {
          sh "mvn sonar:sonar -Dsonar.host.url=${SONAR_HOST} -Dsonar.login=${SONAR_TOKEN}"
        }
      }
    }

    stage('Publish to Nexus') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'nexus-cred-id',
                                          usernameVariable: 'NEXUS_USER',
                                          passwordVariable: 'NEXUS_PASS')]) {
          sh 'mvn deploy -DskipTests -Dnexus.username=$NEXUS_USER -Dnexus.password=$NEXUS_PASS'
        }
      }
    }
  }

  post {
    success {
      echo "Pipeline finished SUCCESS ✅"
    }
    failure {
      echo "Pipeline FAILED ❌"
    }
    always {
      cleanWs()
    }
  }
}
```

---

## 5. 🎤 Interview Questions & Answers

### Q1: What is a Jenkinsfile and why is it useful?

**Answer:**
A `Jenkinsfile` is a text file that defines a Jenkins pipeline. It enables **Pipeline-as-Code**, meaning pipelines are versioned in source control, reviewable, reusable, and portable. It standardizes CI/CD automation across teams.

---

### Q2: What’s the difference between Declarative and Scripted pipelines?

**Answer:**

* **Declarative** → higher-level, structured syntax, easier to read, recommended.
* **Scripted** → Groovy-based, more flexible but harder to maintain.
  Most projects today use **Declarative** for simplicity.

---

### Q3: What are the key blocks in a Declarative pipeline?

**Answer:**

* `agent` → execution node.
* `tools` → pre-configured tools like Maven, JDK.
* `environment` → variables used in pipeline.
* `stages → stage → steps` → main execution logic.
* `post` → actions after success/failure (notifications, cleanup).

---

### Q4: How do you handle credentials in Jenkinsfile?

**Answer:**

* Store credentials securely in Jenkins **Credentials Manager**.
* Reference them via `withCredentials` block or environment injection.
* Example:

  ```groovy
  withCredentials([string(credentialsId: 'sonar-token-id', variable: 'SONAR_TOKEN')]) {
    sh "mvn sonar:sonar -Dsonar.login=${SONAR_TOKEN}"
  }
  ```

---

### Q5: How do you run SonarQube analysis from a pipeline?

**Answer:**

* Install **SonarQube Scanner plugin**.
* Configure SonarQube server in Jenkins settings.
* In Jenkinsfile, use `withSonarQubeEnv` or pass token via `withCredentials`.
* Example:

  ```groovy
  withSonarQubeEnv('SonarQubeServer') {
    sh 'mvn sonar:sonar'
  }
  ```

---

### Q6: How do you upload artifacts to Nexus in a pipeline?

**Answer:**

* Option 1: Use **Maven deploy** with `distributionManagement` in `pom.xml`.
* Option 2: Use **Nexus Artifact Uploader plugin**.
* Example (plugin step):

  ```groovy
  nexusArtifactUploader(
    nexusVersion: 'nexus3',
    protocol: 'http',
    nexusUrl: 'nexus.example.com:8081',
    repository: 'maven-releases',
    credentialsId: 'nexus-cred-id',
    groupId: 'com.mycompany',
    artifactId: 'myapp',
    version: '1.0.${BUILD_NUMBER}',
    type: 'war',
    file: 'target/myapp.war'
  )
  ```

---

### Q7: How do you collect test reports in a pipeline?

**Answer:**
Use `junit` step to publish test results to Jenkins:

```groovy
post {
  always {
    junit '**/target/surefire-reports/*.xml'
  }
}
```

This gives trends & pass/fail stats in Jenkins UI.

---

### Q8: How do you prevent builds from running on Jenkins master node?

**Answer:**

* Use `agent { label 'build-node' }` to target specific build agents.
* Mark master as “Do not allow builds” in Jenkins node config.

---

### Q9: What is the role of the `post` block in Jenkinsfile?

**Answer:**
It defines actions to run **after stages** (or after the whole pipeline):

* `success` → run only on success.
* `failure` → run only on failure.
* `always` → run regardless (e.g., cleanup).

---

### Q10: How do you debug a failing pipeline stage?

**Answer:**

* Review console output of that stage.
* Add `echo` or `sh 'set -x'` for more logging.
* Use **Replay feature** to rerun with modified Jenkinsfile snippets.
* Check plugin versions & configuration.

---

## 6. 📋 Key Takeaways

* Jenkinsfile = CI/CD pipeline in code.
* Declarative syntax is recommended.
* Always use credentials manager for secrets.
* Pipelines should be modular → checkout, build, test, analyze, publish.
* Documentation & plugin pages are the best reference when stuck.

---

# 📘 Comprehensive Notes: Jenkins + SonarQube Integration (Code Analysis)

---

## 1. **What is Code Analysis?**

* **Definition:** Automated checking of source code against best practices, coding standards, and vulnerabilities.
* **Why needed?**

    * Detects **bad practices** early.
    * Identifies **OWASP Top 10 vulnerabilities**.
    * Finds **bugs & maintainability issues** before they reach production.
* **Tools:**

    * **Static Analysis:** Checkstyle, PMD, FindBugs.
    * **Coverage/Testing:** Cobertura, MSTest.
    * **Security/Quality:** SonarQube, OWASP tools.

**👉 Real-life:** Code analysis is part of a **Continuous Integration (CI)** pipeline, ensuring quality gates before deployment.
**👉 Interview Tip:** If asked *“Why integrate SonarQube into CI/CD?”* → Answer: To enforce coding standards, detect vulnerabilities, and ensure long-term maintainability.

---

## 2. **SonarQube Integration — Required Steps**

We must configure Jenkins with:

1. **SonarQube Scanner tool** (like Maven/Git tools).
2. **SonarQube Server details** (URL + authentication token).

---

## 3. **Step-by-Step Setup**

### **Step 1: Ensure services are running**

* Jenkins server ✅
* SonarQube server ✅ (with Nginx proxy, accessible on port 80).

Verify by visiting SonarQube URL:

```
http://<SonarQube-Server-PublicIP or PrivateIP>
```

---

### **Step 2: Install SonarQube Scanner Tool in Jenkins**

1. In Jenkins UI → **Manage Jenkins → Global Tool Configuration**.
2. Scroll to **SonarQube Scanner Installation**.
3. Click **Add SonarQube Scanner**.
4. Name: `Sonar-6.2` (exact name must be used in pipeline code).
5. Select version: **6.2.1.14610** (recommended stable version).
6. Save.

---

### **Step 3: Add SonarQube Server Details in Jenkins**

1. Go to Jenkins → **Manage Jenkins → System**.
2. Scroll to **SonarQube Servers** section.
3. Check **Enable injection of SonarQube server configuration as build environment variables**.
4. Click **Add SonarQube**.

    * **Name:** `Sonar-Server`.
    * **URL:** `http://<Private-IP>` (no need to mention `:80` if using Nginx).
    * **Authentication Token:** (create from SonarQube, see step 4).

---

### **Step 4: Generate Authentication Token in SonarQube**

1. Login to SonarQube UI.
2. Go to **My Account → Security**.
3. Create new **Token**:

    * Name: `Jenkins`.
    * Type: **User Token**.
4. Copy token (only shown once).

---

### **Step 5: Store Token in Jenkins Credentials**

1. In Jenkins UI → Credentials → **System → Global credentials (unrestricted)** → Add Credentials.
2. Select **Kind: Secret Text**.
3. Paste the token.
4. ID: `sonar-token` (or descriptive name).
5. Save.
6. Back in SonarQube Servers config → select this credential from dropdown.
7. Save configuration.

---

### **Step 6: Networking Check**

* Ensure **SonarQube Security Group** allows inbound traffic from Jenkins Security Group on **port 80**.
* Since both Jenkins and SonarQube are in the same VPC, always prefer **private IPs** for communication.

---

## 4. **Validation**

* Jenkins should now recognize:

    * **SonarQube Scanner tool** (`Sonar-6.2`).
    * **SonarQube Server** (`Sonar-Server`) with token.
* Next, we’ll use pipeline code to run analysis and upload reports to SonarQube.

---

## 5. **Error Handling / Troubleshooting**

* ❌ **401 Unauthorized** → Wrong/missing token → Regenerate in SonarQube.
* ❌ **Connection refused** → Wrong IP/port OR Security group not open.
* ❌ **Plugin missing** → Install **SonarQube Scanner for Jenkins** plugin.
* ❌ **Wrong scanner name in pipeline** → Ensure tool name matches Global Tool Config.

---

## 6. **Best Practices**

* Always **use private IPs** for inter-service communication inside VPC.
* Store SonarQube tokens securely in Jenkins Credentials.
* Use **consistent naming** (`Sonar-6.2`, `Sonar-Server`) to avoid pipeline errors.
* Integrate SonarQube scans in every CI pipeline (before deployment stage).

---

## 7. **Interview-Ready Q\&A**

**Q1:** Why do we integrate SonarQube in Jenkins pipelines?
➡️ To enforce coding standards, detect vulnerabilities, and improve maintainability.

**Q2:** How does Jenkins authenticate with SonarQube?
➡️ Using a **token-based authentication**, stored as secret text in Jenkins credentials.

**Q3:** Why use private IP instead of public IP for SonarQube in Jenkins integration?
➡️ Security + performance. Services in same VPC can communicate privately, avoiding public exposure.

**Q4:** What happens if the scanner tool name in pipeline doesn’t match Jenkins config?
➡️ Build fails because Jenkins cannot locate the correct scanner. Always use the exact configured name.

---
