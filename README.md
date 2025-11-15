# Terraform — Table of Contents

[![Terraform](https://img.shields.io/badge/terraform-%3E%3D1.0-623CE4)](https://www.terraform.io/)
[![CI](https://github.com/ganesh-moota-dev/terraform/actions/workflows/ci.yml/badge.svg)](https://github.com/ganesh-moota-dev/terraform/actions)
[![License](https://img.shields.io/badge/license-open--source-green)](LICENSE)

## 1. Understand Infrastructure as Code (IaC) Concepts

* [What is Infrastructure as Code (IaC)?](#1-understand-infrastructure-as-code-iac-concepts)
* [Benefits of IaC](#1-understand-infrastructure-as-code-iac-concepts)
* [Declarative vs Imperative Approaches](#1-understand-infrastructure-as-code-iac-concepts)
* [Idempotency and Reproducibility](#1-understand-infrastructure-as-code-iac-concepts)
* [Tools in the IaC Ecosystem (Terraform, Ansible, CloudFormation)](#1-understand-infrastructure-as-code-iac-concepts)

---

## 2. Understand the Purpose of Terraform

* [What Problem Does Terraform Solve?](#2-understand-the-purpose-of-terraform)
* [Terraform vs Other IaC Tools](#2-understand-the-purpose-of-terraform)
* [Key Features and Advantages](#2-understand-the-purpose-of-terraform)
* [Multi-Cloud and Provider-Agnostic Nature](#2-understand-the-purpose-of-terraform)
* [Common Use Cases](#2-understand-the-purpose-of-terraform)

---

## 3. Understand Terraform Basics

* [Terraform Configuration Files (.tf)](#3-understand-terraform-basics)
* [Providers, Resources, and Data Sources](#3-understand-terraform-basics)
* [Input Variables and Outputs](#3-understand-terraform-basics)
* [Terraform CLI Overview](#3-understand-terraform-basics)
* [Directory Structure and Best Practices](#3-understand-terraform-basics)

---

## 4. Use of Terraform Outside Core Workflows

* [Terraform Import](#4-use-of-terraform-outside-core-workflows)
* [Terraform Taint and Untaint](#4-use-of-terraform-outside-core-workflows)
* [Terraform Console](#4-use-of-terraform-outside-core-workflows)
* [Terraform Graph](#4-use-of-terraform-outside-core-workflows)
* [Terraform Format (fmt) and Validate](#4-use-of-terraform-outside-core-workflows)
* [Terraform Workspaces](#4-use-of-terraform-outside-core-workflows)

---

## 5. Interact with Terraform Modules

* [What Are Modules?](#5-interact-with-terraform-modules)
* [Module Structure (Inputs, Outputs, Resources)](#5-interact-with-terraform-modules)
* [Using Public Modules from Terraform Registry](#5-interact-with-terraform-modules)
* [Creating and Calling Custom Modules](#5-interact-with-terraform-modules)
* [Passing Variables Between Modules](#5-interact-with-terraform-modules)
* [Module Versioning and Best Practices](#5-interact-with-terraform-modules)

---

## 6. Use the Core Terraform Workflow

* [Core Workflow Overview](#6-use-the-core-terraform-workflow)

  * `terraform init`
  * `terraform plan`
  * `terraform apply`
  * `terraform destroy`
* [Understanding Execution Plans](#6-use-the-core-terraform-workflow)
* [Lifecycle of Infrastructure Changes](#6-use-the-core-terraform-workflow)

---

## 7. Implement and Maintain State

* [What is Terraform State?](#7-implement-and-maintain-state)
* [Purpose of `terraform.tfstate`](#7-implement-and-maintain-state)
* [Local vs Remote State](#7-implement-and-maintain-state)
* [State Locking and Consistency](#7-implement-and-maintain-state)
* [State Commands (`terraform state list`, `mv`, `rm`, `show`)](#7-implement-and-maintain-state)
* [Sensitive Data in State](#7-implement-and-maintain-state)
* [Best Practices for State Management](#7-implement-and-maintain-state)

---

## 8. Regenerate and Modify Configuration

* [Handling Drift Between Configuration and Real Resources](#8-regenerate-and-modify-configuration)
* [Terraform Refresh](#8-regenerate-and-modify-configuration)
* [Refactoring and Reusing Configurations](#8-regenerate-and-modify-configuration)
* [Moving Resources Between States](#8-regenerate-and-modify-configuration)
* [Using `terraform import` for Existing Infrastructure](#8-regenerate-and-modify-configuration)

---

## 9. Understand Terraform Cloud Capabilities

* [What is Terraform Cloud and Terraform Enterprise?](#9-understand-terraform-cloud-capabilities)
* [Remote Operations and Workspaces](#9-understand-terraform-cloud-capabilities)
* [Version Control Integration](#9-understand-terraform-cloud-capabilities)
* [State Storage in Terraform Cloud](#9-understand-terraform-cloud-capabilities)
* [Team Access Controls and Policy Enforcement](#9-understand-terraform-cloud-capabilities)
* [Cost Estimation and Notifications](#9-understand-terraform-cloud-capabilities)

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
  If you apply the same configuration again **without changing the code or state**, Terraform will detect that the desired state already matches the real infrastructure and **won’t create new r[...]**
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

## Contributing

Contributions are welcome! If you would like to contribute, please open an issue or a pull request. For larger changes, please create an issue first to discuss the proposed change. Optionally add a CONTRIBUTING.md file with more details.

Key points:

* Be respectful and follow a collaborative approach.
* Open issues for bugs or feature requests.
* Provide clear commit messages and include tests/examples where applicable.

## License

This project is open source and contributions from the community are welcome. By contributing, you agree that your contributions will be licensed under the same open terms as the project.
