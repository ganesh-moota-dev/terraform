# Terraform Guide

## Table of Contents

[![Terraform](https://img.shields.io/badge/terraform-%3E%3D1.0-623CE4)](https://www.terraform.io/)
[![License](https://img.shields.io/badge/license-open--source-green)](LICENSE)

### 1. Understand Infrastructure as Code (IaC) Concepts

* [What is Infrastructure as Code (IaC)?](#what-is-infrastructure-as-code-iac)
* [Benefits of IaC](#benefits-of-iac)
* [Declarative vs Imperative Approaches](#declarative-vs-imperative-approaches)
* [Idempotency and Reproducibility](#idempotency-and-reproducibility)
* [Tools in the IaC Ecosystem (Terraform, Ansible, CloudFormation)](#tools-in-the-iac-ecosystem-terraform-ansible-cloudformation)

### 2. Understand the Purpose of Terraform

* [What Problem Does Terraform Solve?](#2-understand-the-purpose-of-terraform)
* [Terraform vs Other IaC Tools](#2-understand-the-purpose-of-terraform)
* [Key Features and Advantages](#2-understand-the-purpose-of-terraform)
* [Multi-Cloud and Provider-Agnostic Nature](#2-understand-the-purpose-of-terraform)
* [Common Use Cases](#2-understand-the-purpose-of-terraform)

### 3. Understand Terraform Basics

* [Terraform Configuration Files (.tf)](#3-understand-terraform-basics)
* [Providers, Resources, and Data Sources](#3-understand-terraform-basics)
* [Input Variables and Outputs](#3-understand-terraform-basics)
* [Terraform CLI Overview](#3-understand-terraform-basics)
* [Directory Structure and Best Practices](#3-understand-terraform-basics)

### 4. Use of Terraform Outside Core Workflows

* [Terraform Import](#4-use-of-terraform-outside-core-workflows)
* [Terraform Taint and Untaint](#4-use-of-terraform-outside-core-workflows)
* [Terraform Console](#4-use-of-terraform-outside-core-workflows)
* [Terraform Graph](#4-use-of-terraform-outside-core-workflows)
* [Terraform Format (fmt) and Validate](#4-use-of-terraform-outside-core-workflows)
* [Terraform Workspaces](#4-use-of-terraform-outside-core-workflows)

### 5. Interact with Terraform Modules

* [What Are Modules?](#5-interact-with-terraform-modules)
* [Module Structure (Inputs, Outputs, Resources)](#5-interact-with-terraform-modules)
* [Using Public Modules from Terraform Registry](#5-interact-with-terraform-modules)
* [Creating and Calling Custom Modules](#5-interact-with-terraform-modules)
* [Passing Variables Between Modules](#5-interact-with-terraform-modules)
* [Module Versioning and Best Practices](#5-interact-with-terraform-modules)

### 6. Use the Core Terraform Workflow

* [Core Workflow Overview](#6-use-the-core-terraform-workflow)

  * `terraform init`
  * `terraform plan`
  * `terraform apply`
  * `terraform destroy`
* [Understanding Execution Plans](#6-use-the-core-terraform-workflow)
* [Lifecycle of Infrastructure Changes](#6-use-the-core-terraform-workflow)

### 7. Implement and Maintain State

* [What is Terraform State?](#7-implement-and-maintain-state)
* [Purpose of `terraform.tfstate`](#7-implement-and-maintain-state)
* [Local vs Remote State](#7-implement-and-maintain-state)
* [State Locking and Consistency](#7-implement-and-maintain-state)
* [State Commands (`terraform state list`, `mv`, `rm`, `show`)](#7-implement-and-maintain-state)
* [Sensitive Data in State](#7-implement-and-maintain-state)
* [Best Practices for State Management](#7-implement-and-maintain-state)

### 8. Regenerate and Modify Configuration

* [Handling Drift Between Configuration and Real Resources](#8-regenerate-and-modify-configuration)
* [Terraform Refresh](#8-regenerate-and-modify-configuration)
* [Refactoring and Reusing Configurations](#8-regenerate-and-modify-configuration)
* [Moving Resources Between States](#8-regenerate-and-modify-configuration)
* [Using `terraform import` for Existing Infrastructure](#8-regenerate-and-modify-configuration)

### 9. Understand Terraform Cloud Capabilities

* [What is Terraform Cloud and Terraform Enterprise?](#9-understand-terraform-cloud-capabilities)
* [Remote Operations and Workspaces](#9-understand-terraform-cloud-capabilities)
* [Version Control Integration](#9-understand-terraform-cloud-capabilities)
* [State Storage in Terraform Cloud](#9-understand-terraform-cloud-capabilities)
* [Team Access Controls and Policy Enforcement](#9-understand-terraform-cloud-capabilities)
* [Cost Estimation and Notifications](#9-understand-terraform-cloud-capabilities)

---

## 1. Understand Infrastructure as Code (IaC) Concepts

### What is Infrastructure as Code (IaC)?

**What problem are we solving?**
Have you ever manually set up a server, database, or cloud resource and then needed to repeat or scale that setup—only to run into errors, inconsistencies, or “it works on my machine” problems? Infrastructure as Code (IaC) solves this by allowing you to describe your entire infrastructure—servers, networks, storage, policies—as configuration files, like code.

**Core idea:**  
You write code (using tools like Terraform) to describe *what* infrastructure you need. The tool handles *how* to create and maintain it.

**Relatable developer doubts:**  
- “Does defining infrastructure as code mean I never need to click in the console again?”  
  Yes, for the lifecycle operations, your config files become the source of truth.

---

### Benefits of IaC

**Why should I bother with IaC instead of clicking around in the cloud portal?**
- **Consistency:** Every environment (dev, staging, prod) matches the code, reducing configuration drift and “it works on my machine” drama.
- **Automation:** Quickly create, update, or remove infrastructure by running commands—no manual steps.
- **Version control:** Your infrastructure evolves via Git commits, making changes traceable and revertible.
- **Collaboration:** Teams can review, share, and improve configs together—just like application code.
- **Scalability & Reusability:** Reuse code for similar setups across projects or teams.
- **Speed:** Automate repetitive tasks and spin up environments faster.

**Manual vs. IaC Comparison:**

| Manual Configuration     | Infrastructure as Code        |
|-------------------------|-------------------------------|
| Click through console   | Code files & automation       |
| Prone to human error    | Reliable, repeatable builds   |
| No change history       | Full audit trail (Git)        |
| Knowledge silos         | Documentation in code         |

---

### Declarative vs Imperative Approaches

**How do I tell the tool what I want?**
- **Declarative (Terraform, CloudFormation):** You specify the *desired state* (“I want three VMs and a database”). The tool figures out how to get there.  
- **Imperative (some Ansible usage, scripts):** You specify the *steps* (“First, create a VM, then install updates…”).

**Why does this matter?**  
Declarative is simpler for most infrastructure problems—describe the goal, and let the tool decide the steps.  
As a developer:  
- “Will Terraform figure out what’s already there and only change what’s needed?” Yes!
- “Do I need to handle failure recovery steps myself?” In declarative, the tool does that for you.

---

### Idempotency and Reproducibility

**What happens if I run Terraform again and again—will I end up with double the resources?**
- **Idempotency:** Applying the same config multiple times results in the same state. No duplicates, only the required changes.
- **Reproducibility:** Anyone using your config will get the same result, every time.

**Example:**
- Run `terraform apply`—get your infrastructure.
- Run it again, with no changes—nothing happens.
- Make a change to the config—only the change is applied.

Manual steps, in contrast, often lead to duplication and drift.

---

### Tools in the IaC Ecosystem (Terraform, Ansible, CloudFormation)

**Which IaC tool should I use?**
- **Terraform:** Declarative, multi-cloud, open-source, large community.  
  Great for provisioning infrastructure resources across AWS, Azure, GCP, etc.
- **Ansible:** Imperative (or hybrid), focuses on configuration and app deployment, not just infrastructure.
- **CloudFormation:** Declarative, AWS-specific.

**How do I choose?**
- “I want one tool for AWS, Azure, and GCP”—**Terraform**.
- “I want fine control and config management”—**Ansible**.
- “I’m all-in on AWS”—**CloudFormation**.

**Quick reference:**

| Tool          | Approach     | Cloud support      | Best at                      |
|---------------|-------------|--------------------|------------------------------|
| Terraform     | Declarative  | Multi-cloud        | Infra provisioning           |
| Ansible       | Imperative   | Any (via SSH/API)  | Config, app deploy           |
| CloudFormation| Declarative  | AWS only           | AWS infra provisioning       |

*  *By understanding these foundations, you’re ready to dig into how Terraform works and why it’s a leading choice for modern infrastructure automation. The next sections will build on these basics and help you master the workflow, state management, modules, and more.*
---

### What is an Infrastructure Lifecycle?

The **Infrastructure Lifecycle** refers to the sequence of stages that a DevOps or cloud engineer follows to manage infrastructure from start to finish.

It covers everything from **planning and building** to **maintaining and retiring** infrastructure resources.

**In simple terms:**
It’s the roadmap of how infrastructure evolves over time.

**Typical Phases:**

* **Plan** – Define business needs, architecture, and requirements.
* **Design** – Create blueprints and select tools/services.
* **Build** – Deploy or provision the infrastructure.
* **Test** – Validate performance, security, and reliability.
* **Deliver** – Move to production or release to users.
* **Maintain** – Monitor, update, and optimize.
* **Retire** – Decommission outdated or unused resources.

---

### Understanding Day 0, Day 1, and Day 2

The **Day 0–2 model** is a simplified way to describe key stages in the **infrastructure lifecycle**.
These aren’t literal days — they represent **phases** of infrastructure evolution.

| Phase     | Description            | Activities                                                                               |
| --------- | ---------------------- | ---------------------------------------------------------------------------------------- |
| **Day 0** | *Plan and Design*      | Define architecture, choose tools, set goals, and prepare automation strategies.         |
| **Day 1** | *Develop and Iterate*  | Write IaC code (like Terraform), deploy initial setups, test configurations, and refine. |
| **Day 2** | *Go Live and Maintain* | Operate infrastructure in production, monitor performance, patch, and optimize.          |

**Key Point:**
```
Day 0 → Before deployment
Day 1 → During deployment
Day 2 → After deployment (ongoing operations)
```
---

### Idempotent vs Non-Idempotent in IaC

1. **Idempotent**

* **Meaning:** Running the same operation multiple times gives the **same result**.
* **In IaC (e.g., Terraform):**
  If you apply the same configuration again **without changing the code or state**, Terraform will detect that the desired state already matches the real infrastructure and **won’t create new resources**.
* **Example:**
  Running `terraform apply` twice without any changes → *No new resources are created.*
* **Why:** Terraform compares the *current state* with the *desired configuration* and only makes changes when there’s a difference.
* **Result:** Same end state, no duplication.

2. **Non-Idempotent**

* **Meaning:** Running the same operation multiple times causes **different results** (e.g., creates new resources each time).
* **In manual or non-IaC processes:**
  Each time you manually create infrastructure (like clicking “Create Instance” in the AWS Console), you end up with **additional instances**.
* **Example:**
  Clicking “Create VM” twice → *Two separate VMs are created.*
* **Result:** Unpredictable or duplicate resources.

| Concept                           | Behavior                                               | Example Outcome                                  |
| --------------------------------- | ------------------------------------------------------ | ------------------------------------------------ |
| **Idempotent (IaC)**              | Same configuration applied twice results in no changes | `terraform apply` twice → only one instance      |
| **Non-Idempotent (Manual setup)** | Each execution creates a new resource                  | Clicking “Create Instance” twice → two instances |

---

### Provisioning vs Deployment vs Orchestration

These three terms describe **different stages** of setting up and running applications in the cloud.
They often work **together** but mean **different things**.

#### 1. **Provisioning**

**Meaning:**
Provisioning means **preparing and setting up the infrastructure** — such as servers, networks, storage, and databases — so they are ready for use.

**Example:**

* Launching an EC2 instance on AWS.
* Creating a database or S3 bucket.
* Setting up system configurations and installing required software.

**Tools used:**
Terraform, Ansible, Chef, Puppet, Bash scripts, PowerShell, or Cloud-init.

**Goal:**
Make the server or cloud environment ready to host your application.

#### 2. **Deployment**

**Meaning:**
Deployment is **putting your application code** onto the provisioned infrastructure and making it live.

**Example:**

* Uploading your web app to the EC2 instance.
* Using CI/CD pipelines (like Jenkins or GitHub Actions) to push the latest app version.

**Tools used:**
AWS CodePipeline, Jenkins, GitHub Actions, CircleCI, Azure DevOps.

**Goal:**
Run your application on the infrastructure you provisioned.

#### 3.  **Orchestration**

**Meaning:**
Orchestration is about **coordinating and managing multiple services or servers** to work together automatically.

**Example:**

* Starting and stopping multiple containers.
* Managing dependencies between services.
* Scaling up or down automatically when traffic changes.

**Tools used:**
Kubernetes, Docker Swarm, AWS ECS, Apache Airflow.

**Goal:**
Automate and coordinate multiple infrastructure and app components to behave as one system.

<br/>

| Term              | What it Does                         | Example Tools            | Example Scenario                  |
| ----------------- | ------------------------------------ | ------------------------ | --------------------------------- |
| **Provisioning**  | Set up and prepare infrastructure    | Terraform, Ansible, Chef | Create EC2 instance or database   |
| **Deployment**    | Deliver and run application code     | Jenkins, GitHub Actions  | Push app to EC2 or Kubernetes pod |
| **Orchestration** | Coordinate multiple systems/services | Kubernetes, AWS ECS      | Manage containers and auto-scale  |

```
> Provisioning = Get servers ready
> Deployment = Put code on servers
> Orchestration = Make everything work together
```
---

### Contributing

Contributions are welcome! If you would like to contribute, please open an issue or a pull request. For larger changes, please create an issue first to discuss the proposed change. Optionally add a CONTRIBUTING.md file with more details.

Key points:

* Be respectful and follow a collaborative approach.
* Open issues for bugs or feature requests.
* Provide clear commit messages and include tests/examples where applicable.

### License

This project is open source and contributions from the community are welcome. By contributing, you agree that your contributions will be licensed under the same open terms as the project.
