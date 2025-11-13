# Terraform — Table of Contents

## 1. Understand Infrastructure as Code (IaC) Concepts

* What is Infrastructure as Code (IaC)?
* Benefits of IaC
* Declarative vs Imperative Approaches
* Idempotency and Reproducibility
* Tools in the IaC Ecosystem (Terraform, Ansible, CloudFormation)

---

## 2. Understand the Purpose of Terraform

* What Problem Does Terraform Solve?
* Terraform vs Other IaC Tools
* Key Features and Advantages
* Multi-Cloud and Provider-Agnostic Nature
* Common Use Cases

---

## 3. Understand Terraform Basics

* Terraform Configuration Files (.tf)
* Providers, Resources, and Data Sources
* Input Variables and Outputs
* Terraform CLI Overview
* Directory Structure and Best Practices

---

## 4. Use of Terraform Outside Core Workflows

* Terraform Import
* Terraform Taint and Untaint
* Terraform Console
* Terraform Graph
* Terraform Format (fmt) and Validate
* Terraform Workspaces

---

## 5. Interact with Terraform Modules

* What Are Modules?
* Module Structure (Inputs, Outputs, Resources)
* Using Public Modules from Terraform Registry
* Creating and Calling Custom Modules
* Passing Variables Between Modules
* Module Versioning and Best Practices

---

## 6. Use the Core Terraform Workflow

* Core Workflow Overview

  * `terraform init`
  * `terraform plan`
  * `terraform apply`
  * `terraform destroy`
* Understanding Execution Plans
* Lifecycle of Infrastructure Changes

---

## 7. Implement and Maintain State

* What is Terraform State?
* Purpose of `terraform.tfstate`
* Local vs Remote State
* State Locking and Consistency
* State Commands (`terraform state list`, `mv`, `rm`, `show`)
* Sensitive Data in State
* Best Practices for State Management

---

## 8. Regenerate and Modify Configuration

* Handling Drift Between Configuration and Real Resources
* Terraform Refresh
* Refactoring and Reusing Configurations
* Moving Resources Between States
* Using `terraform import` for Existing Infrastructure

---

## 9. Understand Terraform Cloud Capabilities

* What is Terraform Cloud and Terraform Enterprise?
* Remote Operations and Workspaces
* Version Control Integration
* State Storage in Terraform Cloud
* Team Access Controls and Policy Enforcement
* Cost Estimation and Notifications

---

# 1. Understand Infrastructure as Code (IaC) Concepts

### The Problem with Manual Configuration

Manually configuring your cloud infrastructure can be useful when experimenting or prototyping, but it quickly becomes inefficient and error-prone as your system grows.

**Drawbacks of manual setup:**

* **Prone to human error:** It’s easy to misconfigure settings when doing things by hand.
* **Hard to maintain consistency:** Ensuring every environment (dev, staging, prod) matches exactly becomes difficult.
* **Compliance challenges:** It’s hard to verify that systems meet security or policy standards.
* **Knowledge silos:** Configuration details often stay with individuals, making onboarding or handover difficult.
* **No version history:** There’s no reliable record of what changed and when.

---

### What is Infrastructure as Code (IaC)?

**Infrastructure as Code (IaC)** means defining your infrastructure using **code or configuration files**, rather than manually creating resources through a console or CLI.

Terraform is one of the leading tools that enables IaC.

**Core idea:**
You write code that describes *what* your infrastructure should look like, and tools like Terraform handle *how* to make it happen.

---

### Benefits of IaC

* **Automation:** Deploy, update, or destroy infrastructure automatically.
* **Consistency:** Ensures every environment is identical (no “it works on my machine” issues).
* **Version control:** IaC files can be stored in Git, allowing tracking and rollback of changes.
* **Reusability:** You can reuse modules or templates across projects.
* **Scalability:** Quickly replicate and scale infrastructure setups.
* **Collaboration:** Teams can easily share and review infrastructure code.

---

### IaC as a Blueprint

Think of IaC as a **blueprint** for your cloud infrastructure:

* It defines your architecture’s structure and relationships.
* It serves as documentation — anyone can read the code to understand your setup.
* It makes spinning up new environments predictable and repeatable.

---

| Manual Configuration                | Infrastructure as Code           |
| ----------------------------------- | -------------------------------- |
| Manual setup through console or CLI | Automated setup using scripts    |
| Error-prone and inconsistent        | Reliable and reproducible        |
| Hard to track or audit changes      | Version-controlled and auditable |
| Difficult to share knowledge        | Easy to share and collaborate    |
| Slow and repetitive                 | Fast, scalable, and automated    |

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
Day 0 → Before deployment
Day 1 → During deployment
Day 2 → After deployment (ongoing operations)

---



