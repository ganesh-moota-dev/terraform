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

## The Problem with Manual Configuration

Manually configuring your cloud infrastructure can be useful when experimenting or prototyping, but it quickly becomes inefficient and error-prone as your system grows.

**Drawbacks of manual setup:**

* **Prone to human error:** It’s easy to misconfigure settings when doing things by hand.
* **Hard to maintain consistency:** Ensuring every environment (dev, staging, prod) matches exactly becomes difficult.
* **Compliance challenges:** It’s hard to verify that systems meet security or policy standards.
* **Knowledge silos:** Configuration details often stay with individuals, making onboarding or handover difficult.
* **No version history:** There’s no reliable record of what changed and when.

---

## What is Infrastructure as Code (IaC)?

**Infrastructure as Code (IaC)** means defining your infrastructure using **code or configuration files**, rather than manually creating resources through a console or CLI.

Terraform is one of the leading tools that enables IaC.

**Core idea:**
You write code that describes *what* your infrastructure should look like, and tools like Terraform handle *how* to make it happen.

---

## Benefits of IaC

* **Automation:** Deploy, update, or destroy infrastructure automatically.
* **Consistency:** Ensures every environment is identical (no “it works on my machine” issues).
* **Version control:** IaC files can be stored in Git, allowing tracking and rollback of changes.
* **Reusability:** You can reuse modules or templates across projects.
* **Scalability:** Quickly replicate and scale infrastructure setups.
* **Collaboration:** Teams can easily share and review infrastructure code.

---

## IaC as a Blueprint

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

## What is an Infrastructure Lifecycle?

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

## Understanding Day 0, Day 1, and Day 2

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

## Idempotent vs Non-Idempotent in IaC

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

## Provisioning vs Deployment vs Orchestration

These three terms describe **different stages** of setting up and running applications in the cloud.
They often work **together** but mean **different things**.

### 1. **Provisioning**

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

### 2. **Deployment**

**Meaning:**
Deployment is **putting your application code** onto the provisioned infrastructure and making it live.

**Example:**

* Uploading your web app to the EC2 instance.
* Using CI/CD pipelines (like Jenkins or GitHub Actions) to push the latest app version.

**Tools used:**
AWS CodePipeline, Jenkins, GitHub Actions, CircleCI, Azure DevOps.

**Goal:**
Run your application on the infrastructure you provisioned.

### 3.  **Orchestration**

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



