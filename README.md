# Project: The Automated CI/CD & Observable Portfolio Pipeline

**Author:** Mahmoud Ahmed Hussein

**Role:** DevOps Engineer

## 📌 Project Overview

This project demonstrates a production-grade DevOps pipeline featuring both **Continuous Integration/Continuous Deployment (CI/CD)** and **System-Level Observability**.

The infrastructure is broken into two primary domains:

1. **Automated Deployment:** Pushing code to the `main` branch triggers a containerized Jenkins server to automatically build and deploy a static web application behind an Nginx web server.
2. **Infrastructure Monitoring:** The host machine is configured via Ansible (Infrastructure as Code) to broadcast hardware metrics, which are then scraped by Prometheus and visualized in real-time using Grafana.

## 🏛️ System Architecture

```text
[ Developer / GitHub ]
        | (Git Push)
        V
[ Jenkins Container ] ---> Builds & Deploys ---> [ Portfolio Web App Container ]
        ^                                                    |
        | (Docker.sock)                                      |
================================================================================
                           UBUNTU VM (HOST SYSTEM)
================================================================================
        |                                                    |
[ Node Exporter ] <--- Reads CPU/RAM/Network ----------------+
        |
        V
[ Prometheus Container ] ---> Scrapes metrics on Port 9090
        |
        V
[ Grafana Container ] ---> Visualizes Dashboard on Port 3000

```

## 🛠️ Technology Stack

* **Version Control:** Git & GitHub
* **OS/Environment:** Linux (Ubuntu VMware Virtual Machine)
* **CI/CD Engine:** Jenkins (Containerized via DooD Architecture)
* **Containerization:** Docker & Docker Compose
* **Web Server/Routing:** Nginx (Alpine)
* **Configuration Management:** Ansible (Infrastructure as Code)
* **Observability / Monitoring:** Prometheus, Grafana, Node Exporter

## 📂 Directory Structure

```text
automated-portfolio/
├── Jenkinsfile                  # CI/CD pipeline orchestration
├── Dockerfile                   # Web app container blueprint
├── index.html                   # Portfolio application code
│
├── ansible/                     # Configuration Management Domain
│   ├── inventory.ini            # Host machine definitions
│   └── setup_monitor.yml        # Playbook to install/configure Node Exporter
│
└── monitoring/                  # Infrastructure Monitoring Domain
    ├── prometheus.yml           # Prometheus scraping configuration
    └── docker-compose.yml       # Launches Prometheus & Grafana stack

```

## 🧠 Core Concepts Mastered

1. **Docker-out-of-Docker (DooD):** Configured a containerized Jenkins environment to securely communicate with the host machine's Docker daemon via socket mounting (`/var/run/docker.sock`).
2. **Infrastructure as Code (IaC):** Authored Ansible playbooks to ensure idempotent, automated installation and configuration of system services (Node Exporter) without manual intervention.
3. **Container Networking (Internal DNS):** Routed traffic between Prometheus and Grafana entirely within a secure Docker bridge network using service name resolution, bypassing host firewall restrictions.
4. **Hardware Observability:** Designed a real-time dashboard to track VM resource constraints (CPU, RAM, I/O) to ensure the CI/CD pipeline has adequate overhead to run efficiently.

---

## 🚨 Troubleshooting & Incident Log

During the architecture and deployment phases, several critical infrastructure blockers were systematically resolved:

### 1. Build Stage Failure: Exit Code 127 (`docker: not found`)

* **The Problem:** Jenkins failed at the `docker build` stage because it was running inside an isolated container without the Docker CLI installed.
* **The Solution:** Rebuilt the Jenkins container with a volume mount linking the host's Docker socket (`-v /var/run/docker.sock:/var/run/docker.sock`), installed the `docker.io` CLI via root, and adjusted socket permissions.

### 2. Jenkins UI Crash (The "Oops!" Screen)

* **The Problem:** Attempting to save job configurations resulted in a fatal web interface crash, preventing builds from triggering.
* **The Solution:** Diagnosed a Linux file ownership mismatch on the mounted persistent volume (`/var/jenkins_home`). Executed `chown -R jenkins:jenkins /var/jenkins_home` as root to restore the application's write permissions.

### 3. Grafana I/O Timeout (Container Networking)

* **The Problem:** Grafana threw an `i/o timeout` error when trying to connect to the Prometheus data source using the host machine's external IP address (`http://192.168.180.128:9091`).
* **The Solution:** Identified that the external request was being dropped by the host's UFW firewall. Reconfigured the Grafana data source URL to utilize Docker's internal DNS (`http://prometheus:9090`), securely routing the traffic entirely within the isolated Docker bridge network.
