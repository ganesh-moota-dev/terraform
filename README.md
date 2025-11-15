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

### 2. Infrastructure Lifecycle & Paradigms
* [What is the Infrastructure Lifecycle?](#what-is-the-infrastructure-lifecycle)
* [Understanding Day 0, Day 1, and Day 2](#understanding-day-0-day-1-and-day-2)
* [Provisioning vs Deployment vs Orchestration](#provisioning-vs-deployment-vs-orchestration)
* [Mutable vs Immutable Infrastructure](#mutable-vs-immutable-infrastructure)
* [Mutable Infrastructure](#mutable-infrastructure)
* [Immutable Infrastructure](#immutable-infrastructure)

### 3. Understand Terraform Basics (The Code)
* [Terraform Configuration Files (.tf)](#terraform-configuration-files-tf)
* [Providers, Resources, and Data Sources](#providers-resources-and-data-sources)
* [Input Variables and Outputs](#input-variables-and-outputs)
* [Directory Structure and Best Practices](#directory-structure-and-best-practices)

### 4. Use the Core Terraform Workflow (The Actions)
* [Core Workflow Overview](#core-workflow-overview)
* [Understanding Execution Plans](#understanding-execution-plans)
* [Lifecycle of Infrastructure Changes](#lifecycle-of-infrastructure-changes)

### 5. Implement and Maintain State (The History)
* [What is Terraform State?](#what-is-terraform-state)
* [Purpose of `terraform.tfstate`](#purpose-of-terraformtfstate)
* [Local vs Remote State](#local-vs-remote-state)
* [State Locking and Consistency](#state-locking-and-consistency)
* [State Commands (`terraform state list`, `mv`, `rm`, `show`)](#state-commands-terraform-state-list-mv-rm-show)
* [Sensitive Data in State](#sensitive-data-in-state)
* [Best Practices for State Management](#best-practices-for-state-management)

### 6. Configuration Drift (The Problem)
* [What is Configuration Drift?](#what-is-configuration-drift)
* [How to Detect Configuration Drift?](#how-to-detect-configuration-drift)
* [How to Correct Configuration Drift?](#how-to-correct-configuration-drift)
* [How to Prevent Configuration Drift](#how-to-prevent-configuration-drift)

### 7. Regenerate and Modify Configuration (The Fix)
* [Handling Drift Between Configuration and Real Resources](#handling-drift-between-configuration-and-real-resources)
* [Terraform Refresh](#terraform-refresh)
* [Refactoring and Reusing Configurations](#refactoring-and-reusing-configurations)
* [Moving Resources Between States](#moving-resources-between-states)
* [Using `terraform import` for Existing Infrastructure](#using-terraform-import-for-existing-infrastructure)

### 8. Interact with Terraform Modules (Structure)
* [What Are Modules?](#what-are-modules)
* [Module Structure (Inputs, Outputs, Resources)](#module-structure-inputs-outputs-resources)
* [Using Public Modules from Terraform Registry](#using-public-modules-from-terraform-registry)
* [Creating and Calling Custom Modules](#creating-and-calling-custom-modules)
* [Passing Variables Between Modules](#passing-variables-between-modules)
* [Module Versioning and Best Practices](#module-versioning-and-best-practices)

### 9. Use of Terraform Outside Core Workflows (Utility Commands)
* [Terraform Import](#terraform-import-utility)
* [Terraform Taint and Untaint](#terraform-taint-and-untaint)
* [Terraform Console](#terraform-console)
* [Terraform Graph](#terraform-graph)
* [Terraform Format (fmt) and Validate](#terraform-format-fmt-and-validate)
* [Terraform Workspaces](#terraform-workspaces)

### 10. Understand Terraform Cloud Capabilities (Collaboration)
* [What is Terraform Cloud and Terraform Enterprise?](#what-is-terraform-cloud-and-terraform-enterprise)
* [Remote Operations and Workspaces](#remote-operations-and-workspaces)
* [Version Control Integration](#version-control-integration)
* [State Storage in Terraform Cloud](#state-storage-in-terraform-cloud)
* [Team Access Controls and Policy Enforcement](#team-access-controls-and-policy-enforcement)
* [Cost Estimation and Notifications](#cost-estimation-and-notifications)

---

## 1. Understand Infrastructure as Code (IaC) Concepts

### What is Infrastructure as Code (IaC)?

**What problem are we solving?**
Have you ever manually set up a server, database, or cloud resource and then needed to repeat or scale that setup—only to run into errors, inconsistencies, or “it works on my machine” problems? **Infrastructure as Code (IaC)** solves this by allowing you to describe your entire infrastructure—servers, networks, storage, policies—as configuration files, like code.

**Core idea:**
You write code (using tools like **Terraform**) to describe **what** infrastructure you need. The tool handles **how** to create and maintain it.

**Relatable developer doubts:**
- “Does defining infrastructure as code mean I never need to click in the console again?”
  Yes, for the lifecycle operations, your config files become the **source of truth**.

---

### Benefits of IaC

**Why should I bother with IaC instead of clicking around in the cloud portal?**
- **Consistency:** Every environment (dev, staging, prod) matches the code, reducing **configuration drift** and “it works on my machine” drama.
- **Automation:** Quickly create, update, or remove infrastructure by running commands—no manual steps.
- **Version control:** Your infrastructure evolves via Git commits, making changes traceable and revertible.
- **Collaboration:** Teams can review, share, and improve configs together—just like application code.
- **Scalability & Reusability:** Reuse code for similar setups across projects or teams.
- **Speed:** Automate repetitive tasks and spin up environments faster.

**Manual vs. IaC Comparison:**

| Manual Configuration | Infrastructure as Code |
|:----------------------|:------------------------|
| Click through console | Code files & automation |
| Prone to human error | Reliable, repeatable builds |
| No change history | Full audit trail (Git) |
| Knowledge silos | Documentation in code |

---

### Declarative vs Imperative Approaches

**How do I tell the tool what I want?**
- **Declarative (Terraform, CloudFormation):** You specify the *desired state* (“I want three VMs and a database”). The tool figures out how to get there.
- **Imperative (some Ansible usage, scripts):** You specify the *steps* (“First, create a VM, then install updates…”).

**Why does this matter?**
Declarative is simpler for most infrastructure problems—describe the goal, and let the tool decide the steps.
As a developer:
- “Will Terraform figure out what’s already there and only change what’s needed?” **Yes!**
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
- **Terraform:** **Declarative**, multi-cloud, open-source, large community.
  Great for **provisioning** infrastructure resources across AWS, Azure, GCP, etc.
- **Ansible:** **Imperative** (or hybrid), focuses on **configuration** and app deployment, not just infrastructure.
- **CloudFormation:** **Declarative**, **AWS-specific**.

**Quick reference:**

| Tool | Approach | Cloud support | Best at |
|:---------------|:-------------|:--------------------|:------------------------------|
| **Terraform** | Declarative | **Multi-cloud** | Infra provisioning |
| **Ansible** | Imperative | Any (via SSH/API) | Config, app deploy |
| **CloudFormation**| Declarative | AWS only | AWS infra provisioning |

* By understanding these foundations, you’re ready to dig into how Terraform works and why it’s a leading choice for modern infrastructure automation.

---

## 2. Infrastructure Lifecycle & Paradigms

As a developer learning Terraform and IaC, it's important to see infrastructure as more than just spinning up servers—it's a lifecycle you manage, just like software development.

### What is the Infrastructure Lifecycle?

The lifecycle of infrastructure covers everything from the initial idea to retiring resources:
- **Plan:** Define what you need and why (requirements, business goals).
- **Design:** Architect your solution and choose services/tools (e.g., Terraform).
- **Build:** Provision infrastructure using IaC.
- **Test:** Validate the setup—does it work? is it secure?
- **Deliver:** Move to production, or hand over for real users.
- **Maintain:** Monitor, update, patch, and optimize as needs evolve.
- **Retire:** Remove or decommission resources when no longer needed.

---

### Understanding Day 0, Day 1, and Day 2

In real-world infrastructure work, you might hear “Day 0, Day 1, Day 2”—these mark major phases of your infrastructure journey:

| Phase | What's Happening | Developer Activities |
|:-----------|:-----------------------------------------|:---------------------------------------------------|
| **Day 0** | Plan & Design | Gather requirements, architecture, choose IaC tools |
| **Day 1** | Build & Deploy | Write Terraform configs, run `terraform apply`, test |
| **Day 2** | Operate & Maintain | Monitor, optimize, update, scale, patch infra |

**Tip for beginners:**
- **Day 0** = Pre-coding prep
- **Day 1** = Coding & launching with Terraform
- **Day 2** = Keeping things running and improving over time

---

### Provisioning vs Deployment vs Orchestration

As your infrastructure skills grow, you'll work with three big concepts:

- **Provisioning:** Setting up servers, storage, networking—getting the environment ready. *Terraform is great for this!*
- **Deployment:** Installing and running your application code on that infrastructure (commonly handled by CI/CD tools).
- **Orchestration:** Automating and coordinating multiple resources, scaling, managing dependencies, and self-healing setups.

| Term | What It's About | Typical Tools | Example Scenario |
|:----------------|:-----------------------------|:---------------------------|:----------------------------------|
| **Provisioning** | Building core infrastructure | Terraform, Ansible | Launch EC2 VM, set up database |
| **Deployment** | Releasing app/software | GitHub Actions, Jenkins | Deploy web app to server |
| **Orchestration** | Coordinating many resources | Kubernetes, AWS ECS | Auto-scale containers, manage DBs |

---

### Mutable vs Immutable Infrastructure

One of the most fundamental design choices in IaC is whether your infrastructure is **mutable** (changeable after creation) or **immutable** (never changed after creation). This choice significantly impacts maintenance, updates, and consistency.

### Mutable Infrastructure

In a mutable environment, you **deploy** a resource (like a VM) and then make **changes** to its configuration *in place* over time.

* **Workflow:** **Develop** $\rightarrow$ **Deploy** (VM) $\rightarrow$ **Configure** (via Ansible, Puppet, or `cloud-init`).
* **The Problem:** Changes, updates, or patches are applied directly to the running server. Over time, different servers in the same group (e.g., three web servers) may have different update histories, leading to **Configuration Drift** ("snowflake servers").

* **Pros:** Quick to apply urgent patches; simpler initial setup.
* **Cons:** Inconsistent, harder to troubleshoot, difficult to roll back changes, suffers from configuration drift.

---

### Immutable Infrastructure

In an immutable environment, once a resource is created, it is **never modified**. To make an update, you do not patch the existing resource; instead, you build an **entirely new resource** with the desired configuration and replace the old one.

* **Workflow:** **Develop** $\rightarrow$ **Configure** (build an image/artifact using Packer) $\rightarrow$ **Deploy** (launch a new VM from the pre-configured image).
* **The Solution:** You use tools like **Packer** to bake all the necessary configurations (OS updates, application dependencies) into a **Virtual Image** (e.g., an AWS AMI or Docker image) *before* deployment. If you need a change, you bake a new image and deploy it, completely replacing the old server.

* **Pros:** **Consistency** (all instances from the same image are identical), **easy rollbacks**, and simplifies testing.
* **Cons:** Slower update process (you have to build and deploy a whole new image/artifact).

---

### IaC Tool Alignment

The best practice in modern IaC often involves a hybrid approach, but **Immutable Infrastructure is generally preferred** for its reliability and consistency, especially when combined with containerization (Docker/Kubernetes).

| Concept | Primary Tool Use Case | IaC Tool |
| :--- | :--- | :--- |
| **Provisioning** | Setting up foundational resources (VPC, Subnets, Load Balancer). | **Terraform** |
| **Image Building (Immutable)** | Creating the clean, pre-configured server image/artifact. | **Packer** |
| **Configuration (Mutable)** | Making in-place changes *after* a VM is launched. | **Ansible, Chef** |

**Summary for Developers:**
Embracing **Immutable Infrastructure** is the key to achieving the **Idempotency** and **Reproducibility** you learned about earlier.

---

## 3. Understand Terraform Basics (The Code)

### Terraform Configuration Files (.tf)

(Placeholder content for this section)

### Providers, Resources, and Data Sources

(Placeholder content for this section)

### Input Variables and Outputs

(Placeholder content for this section)

### Directory Structure and Best Practices

(Placeholder content for this section)

---

## 4. Use the Core Terraform Workflow (The Actions)

### Core Workflow Overview

The core Terraform workflow involves four essential commands used to initialize, check, create, and destroy infrastructure.


* **`terraform init`**: Initializes the working directory containing Terraform configuration files. It downloads necessary **providers** (plugins) and sets up the **backend** (where the state file is stored).
* **`terraform plan`**: Generates an execution plan by comparing the desired state (your code) with the current state (the state file) and the remote state (the actual infrastructure). It shows you exactly what changes (create, update, destroy) Terraform will make, without performing them.
* **`terraform apply`**: Executes the actions proposed in the plan (or a new plan if not saved). This is the command that provisions or updates the infrastructure in the cloud.
* **`terraform destroy`**: Executes a plan to destroy all resources managed by the current configuration and state file.

### Understanding Execution Plans

(Placeholder content for this section)

### Lifecycle of Infrastructure Changes

(Placeholder content for this section)

---

## 5. Implement and Maintain State (The History)

### What is Terraform State?

(Placeholder content for this section)

### Purpose of `terraform.tfstate`

(Placeholder content for this section)

### Local vs Remote State

(Placeholder content for this section)

### State Locking and Consistency

(Placeholder content for this section)

### State Commands (`terraform state list`, `mv`, `rm`, `show`)

(Placeholder content for this section)

### Sensitive Data in State

(Placeholder content for this section)

### Best Practices for State Management

(Placeholder content for this section)

---

## 6. Configuration Drift (The Problem)

Configuration drift is a common challenge when working with cloud infrastructure using IaC tools like Terraform. Here's what you need to know as a developer:

### What is Configuration Drift?

**Configuration drift** happens when your actual cloud infrastructure doesn't match the desired state defined in your Terraform code. This usually occurs because:
- Changes were made manually in the cloud console.
- Scripts or other automation tools modify resources outside of Terraform.
- The infrastructure evolves, but code and state files are not updated.

**Why does it matter?**
Drift can lead to inconsistent environments, surprise outages, failed deployments, and extra debugging headaches.

---

### How to Detect Configuration Drift

To spot drift:
- **Terraform Plan:** Run `terraform plan` to compare your code-defined state to the real infrastructure.
- **Terraform Refresh:** Use `terraform refresh` to update Terraform state based on what's actually deployed before planning changes.
- **Manual audits:** Reviewing the cloud provider's dashboard versus IaC code (best for small setups).

**Signs of drift:**
- `terraform plan` reports unexpected changes.
- Resources missing or settings changed in the cloud but not in code.

---

### How to Correct Configuration Drift

**Best options for fixing drift:**
- **Reapply Code:** Run `terraform apply` to bring infrastructure back in sync with your code.
- **Update Code:** If needed, update your Terraform files to match legit manual changes, then apply.
- **Import Resources:** For resources created outside Terraform, use `terraform import` to bring them under code management.

---

### How to Prevent Configuration Drift

**Practical strategies:**
- **Use IaC exclusively:** Avoid manual changes in the cloud console as much as possible.
- **Access controls:** Limit who can modify infrastructure outside of Terraform (use IAM policies).
- **Automated checks:** Integrate regular `terraform plan` or drift-detection solutions into your CI/CD pipelines.
- **Peer reviews:** Ensure all infrastructure changes go through version-controlled Terraform files and code review.

**In summary:**
* Drift undermines IaC benefits—catch it early, fix it quickly, and lock down your workflow so that your code and your cloud never get out of sync.

---

## 7. Regenerate and Modify Configuration (The Fix)

### Handling Drift Between Configuration and Real Resources

(Placeholder content for this section)

### Terraform Refresh

(Placeholder content for this section)

### Refactoring and Reusing Configurations

(Placeholder content for this section)

### Moving Resources Between States

(Placeholder content for this section)

### Using `terraform import` for Existing Infrastructure

(Placeholder content for this section)

---

## 8. Interact with Terraform Modules (Structure)

### What Are Modules?

(Placeholder content for this section)

### Module Structure (Inputs, Outputs, Resources)

(Placeholder content for this section)

### Using Public Modules from Terraform Registry

(Placeholder content for this section)

### Creating and Calling Custom Modules

(Placeholder content for this section)

### Passing Variables Between Modules

(Placeholder content for this section)

### Module Versioning and Best Practices

(Placeholder content for this section)

---

## 9. Use of Terraform Outside Core Workflows (Utility Commands)

### Terraform Import

(Placeholder content for this section)

### Terraform Taint and Untaint

(Placeholder content for this section)

### Terraform Console

(Placeholder content for this section)

### Terraform Graph

(Placeholder content for this section)

### Terraform Format (fmt) and Validate

(Placeholder content for this section)

### Terraform Workspaces

(Placeholder content for this section)

---

## 10. Understand Terraform Cloud Capabilities (Collaboration)

### What is Terraform Cloud and Terraform Enterprise?

(Placeholder content for this section)

### Remote Operations and Workspaces

(Placeholder content for this section)

### Version Control Integration

(Placeholder content for this section)

### State Storage in Terraform Cloud

(Placeholder content for this section)

### Team Access Controls and Policy Enforcement

(Placeholder content for this section)

### Cost Estimation and Notifications

(Placeholder content for this section)

---

## Contributing

Contributions are welcome! If you would like to contribute by improving existing content or fix errors, please open a pull request

Steps:
```
1. Fork the repository
2. Create a feature branch: git checkout -b feature/your-feature-name
3. Commit your changes: git commit -m 'updated content for section 1'
4. Push to the branch: git push origin feature/your-feature-name
5. Submit a pull request
```

Key points:
* Provide clear commit messages where applicable.

## License

This project is open source and contributions from the community are welcome. By contributing, you agree that your contributions will be licensed under the same open terms as the project.
