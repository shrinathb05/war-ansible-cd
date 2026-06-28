# 🚀 Production-Ready Continuous Deployment Pipeline for Java WAR Applications

A production-ready **Continuous Deployment (CD)** pipeline that automates the deployment of Java WAR applications to multiple Apache Tomcat servers using **Jenkins**, **Ansible**, and **Nexus Repository Manager**.

The project demonstrates how enterprise deployment pipelines are built by separating orchestration from deployment logic while following Infrastructure as Code (IaC) best practices.

---

# 📖 Project Overview

This project automates the complete deployment lifecycle of a Java web application.

The pipeline performs the following tasks:

* Clone deployment repository from GitHub
* Validate deployment prerequisites
* Download the latest WAR artifact from Nexus
* Backup the currently deployed application
* Stop Apache Tomcat
* Remove old deployment
* Deploy the new WAR
* Validate deployment using SHA-256 checksum
* Start Apache Tomcat
* Perform application health checks
* Clean temporary artifacts and Jenkins workspace

The deployment logic is implemented using **Ansible Roles**, while **Jenkins** is responsible only for orchestrating the deployment stages.

---

# 🏗️ Architecture

```text
                    Developer
                        │
                        ▼
                 GitHub Repository
                        │
                        ▼
               Jenkins Master Server
                        │
                        ▼
             Jenkins Agent / Ansible Node
                        │
          ┌─────────────┴─────────────┐
          │                           │
          ▼                           ▼
  Nexus Repository             Ansible Playbooks
          │                           │
          └─────────────┬─────────────┘
                        ▼
               Deploy to Tomcat Servers
          ┌────────┬────────┬────────┐
          ▼        ▼        ▼
     Tomcat-01  Tomcat-02  Tomcat-03
```

---

# 🛠️ Technology Stack

* Jenkins
* Ansible
* Apache Tomcat 10
* Nexus Repository Manager
* Git & GitHub
* Ubuntu Linux
* LXD Containers (Lab Environment)

---

# 📁 Project Structure

```text
tomcat-war-deployment/
│
├── Jenkinsfile                 # Jenkins Declarative Pipeline
├── README.md                   # Project Documentation
├── ansible.cfg                 # Ansible Configuration
│
├── artifacts/                  # Downloaded WAR artifacts
├── logs/                       # Deployment logs
│
├── inventory/
│   └── production.yml          # Target server inventory
│
├── group_vars/
│   ├── all.yml                 # Common variables
│   ├── dev.yml                 # Development variables
│   ├── staging.yml             # Staging variables
│   └── production.yml          # Production variables
│
├── playbooks/
│   ├── precheck.yml
│   ├── artifact_download.yml
│   ├── backup.yml
│   ├── tomcatstop.yml
│   ├── deploy_war.yml
│   ├── start_tomcat.yml
│   ├── healthcheck.yml
│   ├── cleanup.yml
│   └── rolling_deploy.yml
│
└── roles/
    ├── precheck/
    ├── artifact_download/
    ├── backup/
    ├── tomcat_stop/
    ├── deploy_war/
    ├── start_tomcat/
    ├── healthcheck/
    ├── cleanup/
    └── rolling_deploy/
```

---

# 🔄 Jenkins Pipeline Stages

The deployment pipeline is divided into individual stages, making it easy to monitor, troubleshoot, and maintain.

```text
Jenkinsfile
│
├── Stage 1  ── Clean Workspace
│              • Remove previous Jenkins workspace
│              • Prepare a clean deployment environment
│
├── Stage 2  ── Checkout & Precheck
│              • Checkout deployment repository
│              • Validate SSH connectivity
│              • Verify Tomcat installation
│              • Validate required directories
│              • Perform deployment prerequisite checks
│
├── Stage 3  ── Download Artifact
│              • Connect to Nexus Repository
│              • Read maven-metadata.xml
│              • Detect latest released version
│              • Download latest WAR artifact
│
├── Stage 4  ── Backup
│              • Create timestamped backup
│              • Backup currently deployed WAR
│
├── Stage 5  ── Stop Service
│              • Stop Apache Tomcat
│              • Wait until service is stopped
│              • Remove old WAR and extracted directory
│
├── Stage 6  ── Deploy WAR
│              • Deploy new WAR file
│              • Validate SHA-256 checksum
│
├── Stage 7  ── Start Service
│              • Start Apache Tomcat
│              • Wait for WAR extraction
│              • Verify application deployment
│
├── Stage 8  ── Health Check
│              • Verify Tomcat service
│              • Validate application endpoint
│
├── Stage 9  ── Cleanup
│              • Remove downloaded artifacts
│              • Clean Jenkins workspace
│
└── Post
               • Success Notification
               • Failure Notification
```

---

# 🔄 Deployment Workflow

```text
Developer
     │
     ▼
Push Code to GitHub
     │
     ▼
Jenkins Pipeline Trigger
     │
     ▼
Checkout Repository
     │
     ▼
Pre-Deployment Validation
     │
     ▼
Download Latest WAR from Nexus
     │
     ▼
Backup Existing Deployment
     │
     ▼
Stop Tomcat Service
     │
     ▼
Remove Previous Deployment
     │
     ▼
Deploy New WAR
     │
     ▼
Validate SHA-256 Checksum
     │
     ▼
Start Tomcat Service
     │
     ▼
Application Health Check
     │
     ▼
Cleanup
     │
     ▼
Deployment Completed
```

---

# ⚙️ Ansible Roles

| Role                  | Purpose                                           |
| --------------------- | ------------------------------------------------- |
| **precheck**          | Validate deployment prerequisites                 |
| **artifact_download** | Download the latest WAR from Nexus                |
| **backup**            | Backup the existing deployed application          |
| **tomcat_stop**       | Stop Apache Tomcat and remove previous deployment |
| **deploy_war**        | Deploy the latest WAR and verify checksum         |
| **start_tomcat**      | Start Tomcat and verify application extraction    |
| **healthcheck**       | Verify service status and application health      |
| **cleanup**           | Remove downloaded artifacts and clean workspace   |
| **rolling_deploy**    | Future enhancement for zero-downtime deployments  |

---

# 🔐 Security Features

* Jenkins Credentials Store integration
* Secure Nexus authentication
* No hardcoded credentials
* SSH Key-based authentication
* Separation of deployment logic from orchestration
* Environment-specific configuration using Ansible group variables

---

# ✨ Features

* Modular Ansible Role-based architecture
* Jenkins Declarative Pipeline
* Latest artifact detection using Maven Metadata
* Multi-server deployment
* SHA-256 checksum validation
* Timestamped deployment backups
* Automated Tomcat service management
* Health verification after deployment
* Workspace cleanup
* Artifact cleanup
* Secure credential management
* Environment-based configuration

---

# 🚀 Future Enhancements

* Rolling Deployment
* Version-Based Rollback
* Blue-Green Deployment
* Canary Deployment
* Automatic Rollback on Failure
* Slack Notifications
* Email Notifications
* Deployment Approval Gates
* SonarQube Quality Gates
* Prometheus Monitoring
* Grafana Dashboard
* Deployment Reports

---

# 📚 Learning Outcomes

This project demonstrates practical knowledge of:

* Continuous Deployment (CD)
* Jenkins Pipelines
* Ansible Roles
* Infrastructure as Code (IaC)
* Configuration Management
* Apache Tomcat Administration
* Nexus Repository Management
* Linux System Administration
* Secure Secret Management
* Enterprise Deployment Strategies
* Production Deployment Best Practices

---

# 👨‍💻 Author

**Shrinath Bhosale**

DevOps Engineer

### Skills

* Linux
* Jenkins
* Ansible
* Docker
* Kubernetes
* Terraform
* AWS
* Git & GitHub
* Nexus Repository

---

## ⭐ Support

If you found this project helpful, consider giving the repository a **⭐ Star**.
It helps others discover the project and motivates future improvements.
