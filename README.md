Project: The Automated CI/CD Portfolio Pipeline


📌 Project Overview

This project demonstrates a foundational Continuous Integration and Continuous Deployment (CI/CD) pipeline. The objective was to fully automate the build and deployment process of a containerized web application. Upon pushing new code to the main branch, a Jenkins automation server detects the change, builds a new Docker image, and deploys it behind an Nginx web server without any manual intervention.


🛠️ Technology Stack

1.Version Control: Git & GitHub

2.OS/Environment: Linux (Ubuntu VMware Virtual Machine)

3.CI/CD Engine: Jenkins (Containerized)

4.Containerization: Docker & Docker-out-of-Docker (DooD) Architecture

5.Web Server/Routing: Nginx (Alpine)


🧠 Core Concepts Mastered

-Declarative Pipelines as Code: Authored a Jenkinsfile to define the build and deploy stages, ensuring the infrastructure deployment process is version-controlled and repeatable.

-Container Isolation & Networking: Packaged a static web application into a lightweight Nginx container using a custom Dockerfile.

-Docker-out-of-Docker (DooD): Configured a containerized Jenkins environment to securely communicate with the host machine's Docker daemon via socket mounting (/var/run/docker.sock).

-Automated Cleanup: Implemented shell commands within the pipeline to gracefully tear down outdated containers before deploying new iterations, preventing port conflicts and memory leaks.


🚨 Troubleshooting & Incident Log

During the architecture and deployment phases, several critical pipeline blockers were encountered and systematically resolved:

1. Build Stage Failure: Exit Code 127 (docker: not found)  
The Problem: Jenkins failed at the docker build stage. Because Jenkins was running inside its own isolated container, it did not have the Docker CLI installed and could not access the host's Docker engine.

The Solution: Implemented a DooD (Docker-out-of-Docker) architecture:
Rebuilt the Jenkins container with a volume mount linking the host's Docker socket: -v /var/run/docker.sock:/var/run/docker.sock.
Accessed the Jenkins container as the root user to install the docker.io CLI.
Adjusted socket permissions (chmod 666 /var/run/docker.sock) to grant Jenkins execution rights.


2. Jenkins UI Crash (The "Oops!" Screen)  
The Problem: Attempting to save job configurations resulted in a fatal web interface crash, preventing builds from triggering.
The Solution: Diagnosed a Linux file ownership mismatch on the mounted persistent volume (/var/jenkins_home). Executed chown -R jenkins:jenkins /var/jenkins_home as root to restore the application's write permissions, followed by a container restart.
