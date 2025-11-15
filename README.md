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

### 2. Infrastructure Lifecycle
* [What is the Infrastructure Lifecycle?](#what-is-the-infrastructure-lifecycle)
* [Understanding Day 0, Day 1, and Day 2](#understanding-day-0-day-1-and-day-2)
* [Provisioning vs Deployment vs Orchestration](#provisioning-vs-deployment-vs-orchestration)

### 3. Understand Terraform Basics

* [Terraform Configuration Files (.tf)](#3-understand-terraform-basics)
* [Providers, Resources, and Data Sources](#3-understand-terraform-basics)
* [Input Variables and Outputs](#3-understand-terraform-basics)
* [Terraform CLI Overview](#3-understand-terraform-basics)
* [Directory Structure and Best Practices](#3-understand-terraform-basics)

### 4. Configuration Drift
* What is Configuration Drift?
* How to Detect Configuration Drift?
* How to Correct Configuration Drift??
* How to Prevent Configuration Drift

### 5. Use of Terraform Outside Core Workflows

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

## Infrastructure Lifecycle

As a developer learning Terraform and Infrastructure as Code (IaC), it's important to see infrastructure as more than just spinning up servers—it's a lifecycle you manage, just like software development.

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

| Phase     | What's Happening                          | Developer Activities                              |
|-----------|------------------------------------------|---------------------------------------------------|
| Day 0     | Plan & Design                            | Gather requirements, architecture, choose IaC tools|
| Day 1     | Build & Deploy                           | Write Terraform configs, run `terraform apply`, test|
| Day 2     | Operate & Maintain                       | Monitor, optimize, update, scale, patch infra      |

**Tip for beginners:**  
- **Day 0** = Pre-coding prep  
- **Day 1** = Coding & launching with Terraform  
- **Day 2** = Keeping things running and improving over time

This framework shows not just *what* you do, but *when*—helping you approach infrastructure as a sustainable, ongoing practice.

---

### Provisioning vs Deployment vs Orchestration

As your infrastructure skills grow, you'll work with three big concepts:

- **Provisioning:** Setting up servers, storage, networking—getting the environment ready. *Terraform is great for this!*
- **Deployment:** Installing and running your application code on that infrastructure (commonly handled by CI/CD tools).
- **Orchestration:** Automating and coordinating multiple resources, scaling, managing dependencies, and self-healing setups.

| Term            | What It's About               | Typical Tools              | Example Scenario                  |
|-----------------|------------------------------|----------------------------|-----------------------------------|
| Provisioning    | Building core infrastructure  | Terraform, Ansible         | Launch EC2 VM, set up database    |
| Deployment      | Releasing app/software        | GitHub Actions, Jenkins    | Deploy web app to server          |
| Orchestration   | Coordinating many resources   | Kubernetes, AWS ECS        | Auto-scale containers, manage DBs |

**Why it matters:**  
Understanding the lifecycle, day phases, and different types of automation helps you become an effective infrastructure developer. You’ll be able to plan robust systems, know which tools to use at each stage, and keep everything running smoothly as your projects and responsibilities scale up.

---

## Configuration Drift

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

### Contributing

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

### License

This project is open source and contributions from the community are welcome. By contributing, you agree that your contributions will be licensed under the same open terms as the project.
