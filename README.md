# DevOps And Cloud Migration
Moving Backend API Deployment from Heroku to Multi-Cloud Infrastructure

## Overview

* **Challenge:** High Heroku costs and limited infrastructure control prompted a migration for a complex Django API (handling Celery task queues, Redis, and Postgres) serving development, staging, and production environments.

* **Goal:** Rebuild the entire infrastructure on a standard cloud provider (Linode/Digital Ocean) using Infrastructure as Code (IaC) to maintain a rock-solid CircleCI deployment pipeline.

## Tech Stack
* **Backend:** Django, Django REST Framework (DRF)
* **Async Processing:** Celery, Celery Beat, Redis, RabbitMQ
* **Infrastructure:** Terraform, Docker, Docker Compose
* **Cloud & Storage:** Linode (Compute), AWS (S3 for static/media assets)
* **CI/CD:** CircleCI

---

## Execution

### 1. Architecting the Infrastructure as Code (IaC)
Instead of manually clicking through cloud dashboards, I used **Terraform** to ensure our environments were identical and reproducible.
* Containerized the Terraform CLI using Docker Compose to ensure local environment parity for all developers.
* Provisioned specific compute instances for the API, Databases, and message brokers across Development, Staging, and Production.
* Abstracted sensitive keys by utilizing a dedicated secrets management strategy (`.tfvars`), keeping the repository secure.

### 2. Solving the Message Broker & Cache Dilemma
Initially, I attempted to install Redis and RabbitMQ directly on the servers via custom shell scripts. 
* **The Pivot:** Recognizing that manual scripts increase maintenance overhead and scaling friction, I refactored the architecture. I shifted RabbitMQ and Redis into Docker containers and eventually integrated managed solutions like CloudAMQP for enterprise-grade reliability.

### 3. Orchestrating the CI/CD Pipeline
To prevent a heavy lift during deployment, I used Terraform to actually configure CircleCI itself.
* Automated the creation of CircleCI environment variables.
* Setup remote backends for Terraform state tracking directly within the pipeline.
* Integrated JFrog for secure artifact/package management.

---

### **Key Results**
* **Full Automation:** Achieved 100% Infrastructure as Code coverage. New staging or production environments can now be spun up in minutes.
* **Vendor Flexibility:** Decoupled media storage from the compute layer by introducing AWS S3, making the application cloud-agnostic.
* **Cost & Control:** Successfully broke free from Heroku's pricing tier, allowing granular control over database pooling, RAM, and CPU allocation.
