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

