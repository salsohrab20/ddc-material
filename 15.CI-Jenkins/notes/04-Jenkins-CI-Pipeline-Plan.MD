# 📘 Jenkins CI Pipeline – Flow & Concepts

---

## 1. 🌐 **Overview**

The CI (Continuous Integration) pipeline ensures that every code change is:

1. **Fetched** from version control,
2. **Built** into an artifact,
3. **Tested** with unit tests,
4. **Analyzed** for code quality & security,
5. **Versioned and published** to a central repository,
6. Ready for **deployment**.

Tools in this example:

* **Jenkins** → Orchestrator
* **GitHub (Git)** → Source code repository
* **Maven** → Build & Unit tests
* **SonarQube + Checkstyle** → Code quality & static analysis
* **Nexus (Sonatype Repository Manager)** → Artifact repository

---

## 2. 🔄 **Pipeline Flow**

### Step 1: 👨‍💻 Developer Stage

* Developer writes and tests code locally.
* Pushes changes to a centralized **GitHub repository**.

---

### Step 2: 📥 Code Fetch

* Jenkins detects changes in GitHub (via webhook or polling).
* Uses **Git plugin** to clone/fetch the latest code.

---

### Step 3: 🏗️ Build

* Jenkins triggers a **Maven build**.
* Source code is compiled → produces **artifact** (`.jar` / `.war`).

---

### Step 4: ✅ Unit Testing

* Maven executes unit tests (JUnit, TestNG, etc.).
* Generates **XML/HTML reports**.
* Reports integrated into Jenkins job dashboard.

---

### Step 5: 🔍 Code Analysis

* Run **SonarQube Scanner + Checkstyle**.
* Detects:

    * Code vulnerabilities
    * Bugs & code smells
    * Best practices violations
* Reports sent to **SonarQube server** for visualization.
* **Quality Gates**:

    * If code fails predefined quality rules → **pipeline fails**.
    * If passes → continue.

---

### Step 6: 📦 Artifact Repository

* Verified artifact is **versioned**.
* Uploaded to **Nexus Repository Manager**.
* Stored centrally for future deployments.

---

### Step 7: 🚀 Deployment (Later Stage – not in this lecture)

* Stored artifacts can be deployed to staging/production environments.

---

## 3. 📊 **Pipeline Flow Summary (Diagram Style)**

```
 Developer → GitHub (commit code)
         ↓
   Jenkins (fetch code via Git)
         ↓
     Build (Maven)
         ↓
 Unit Tests (JUnit/TestNG)
         ↓
 Code Analysis (SonarQube, Checkstyle)
         ↓
  Quality Gate (Pass/Fail)
         ↓
  Artifact Repository (Nexus)
         ↓
   Ready for Deployment
```

---

## 4. 📋 **Key Points**

* **Jenkins = central automation hub**.
* **Code → Build → Test → Analyze → Publish** is the standard CI flow.
* **SonarQube Quality Gates** ensure poor-quality code does not move forward.
* **Nexus/Artifact repository** allows traceability and reusability of builds.
* Tools can vary (GitLab CI, CircleCI, Bamboo, etc.), but **flow remains the same**.

---

## 5. 🎤 **Interview Questions**

### 🌐 General CI/CD

1. What is Continuous Integration and how is it different from Continuous Deployment?
2. Explain a typical CI pipeline flow with tools you’ve used.
3. Why is CI important in modern software development?

### 🔄 Flow-Specific

4. What triggers Jenkins to start a pipeline job after a GitHub commit?
5. Why do we use Maven in a CI pipeline? Can you replace it with Gradle?
6. How do unit tests and static code analysis differ?
7. What is a SonarQube Quality Gate and why is it important?
8. Why do we upload artifacts to Nexus or Artifactory instead of keeping them in Jenkins?

### ⚠️ Troubleshooting & Best Practices

9. What happens if SonarQube finds vulnerabilities in your code?
10. How would you ensure the pipeline does not deploy code with failing unit tests?
11. If Jenkins cannot connect to GitHub, how would you debug the issue?
12. How do you version artifacts in Nexus for traceability?
13. What security practices would you follow in a CI pipeline?

---

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