# 🏗️ Terraform Day 01 — My First Step into Infrastructure as Code

> 🚀 Moving away from clicking buttons in a cloud console and writing actual code to provision real infrastructure.

---

## 📋 Table of Contents

- [The Task](#the-task)
- [What is Terraform and Why Use It?](#what-is-terraform-and-why-use-it)
- [The Code](#the-code)
- [The Workflow](#the-workflow)
- [My First Error and How I Fixed It](#my-first-error-and-how-i-fixed-it)
- [Breaking Down the Script](#breaking-down-the-script)
- [Common Pitfalls](#common-pitfalls)
- [Key Outcome](#key-outcome)

---

## 🎯 The Task

Create an AWS EC2 Key Pair using Terraform with the following requirements:

| Requirement        | Value                       |
|--------------------|-----------------------------|
| Config file        | `main.tf`                   |
| Directory          | `/home/bob/terraform`       |
| Key pair name      | `nautilus-kp`               |
| Key type           | `RSA`                       |
| Private key output | `/home/bob/nautilus-kp.pem` |

---

## 💡 What is Terraform and Why Use It?

### 🧩 Infrastructure as Code (IaC)

Instead of manually creating resources through a web UI, IaC lets you define infrastructure in a code file. This brings the full power of software development to your cloud environment:

| Benefit | What it means in practice |
|---------|--------------------------|
| 🤖 Automation | Spin up or tear down an entire environment with a single command |
| 🔁 Reproducibility | Use the same code to build identical dev, staging, and prod environments |
| 📝 Version Control | Check `.tf` files into Git — every change is tracked and auditable |
| 🤝 Collaboration | Teams work from the same source of truth |

### 🔌 Providers

A **provider** is a plugin that teaches Terraform how to communicate with a specific platform's API. This task uses three:

| Provider | Purpose |
|----------|---------|
| `aws` | Talks to the AWS API to create the key pair |
| `tls` | Handles cryptographic operations — generates the RSA key |
| `local` | Interacts with the local filesystem to save the `.pem` file |

---

## 📄 The Code

Inside `/home/bob/terraform`, create a file named `main.tf` with the following content:

```hcl
# 1. Configure the AWS Provider
provider "aws" {
  region = "us-east-1"
}

# 2. Generate a new RSA private key locally
resource "tls_private_key" "nautilus_rsa" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

# 3. Register the public key as a Key Pair in AWS
resource "aws_key_pair" "nautilus_key_pair" {
  key_name   = "nautilus-kp"
  public_key = tls_private_key.nautilus_rsa.public_key_openssh
}

# 4. Write the private key to a local file
resource "local_file" "private_key_pem" {
  content         = tls_private_key.nautilus_rsa.private_key_pem
  filename        = "/home/bob/nautilus-kp.pem"
  file_permission = "0400"  # Read-only for the owner
}
```

---

## ⚙️ The Workflow

Every Terraform project follows the same three-step cycle:

```
init → plan → apply
```

### Step 1 — 🔧 `terraform init`
Downloads the required provider plugins and prepares the working directory. **Always run this first** in any new project.

```bash
terraform init
```

### Step 2 — 🔍 `terraform plan`
A dry run. Terraform calculates exactly what it will create, change, or destroy — without touching anything. Review this output carefully before moving on.

```bash
terraform plan
```

### Step 3 — ✅ `terraform apply`
Executes the plan. Terraform shows a summary and asks for confirmation — type `yes` and press Enter.

```bash
terraform apply
```

### Step 4 — 🔎 Verify

Confirm the private key file was created at the expected path:

```bash
ls -l /home/bob/nautilus-kp.pem
```

---

## 🐛 My First Error and How I Fixed It

### ❌ The Error

```
Error: Duplicate provider configuration
A default provider configuration for "aws" was already given at main.tf ...
```

### 🔍 What Caused It

Terraform treats **all `.tf` files in a directory as a single combined configuration**. There was a `provider "aws"` block in both `main.tf` and a separate `provider.tf` — Terraform saw two conflicting definitions and refused to continue.

### ✅ The Fix

Delete the duplicate file:

```bash
rm provider.tf
```

Keep everything in `main.tf` as the task requires. After removing the duplicate, `terraform init` ran without issue.

> 💡 **Lesson learned:** One directory = one merged configuration. Never define the same provider or resource in two different files within the same folder.

---

## 🔬 Breaking Down the Script

### Resource References — The Magic of Terraform

The most powerful concept in the script is how resources reference each other:

```hcl
public_key = tls_private_key.nautilus_rsa.public_key_openssh
```

This line tells Terraform:
- Look at the resource of type `tls_private_key` named `nautilus_rsa`
- Read its `public_key_openssh` output attribute
- Pass that value into the `aws_key_pair` resource

Terraform automatically figures out the **dependency order** — it knows it must generate the TLS key first, then create the AWS key pair, then save the file. You never have to specify the sequence manually.

### Resource Block Anatomy

```hcl
resource "aws_key_pair" "nautilus_key_pair" {
#         ^^^^^^^^^^^^   ^^^^^^^^^^^^^^^^
#         Resource TYPE  Local NAME (used only inside Terraform code)
  key_name   = "nautilus-kp"   # Appears in AWS console
  public_key = ...
}
```

---

## ⚠️ Common Pitfalls

| Mistake | What Happens | Fix |
|---------|-------------|-----|
| Skipping `terraform init` | `plan` and `apply` fail — providers not downloaded | Always run `init` first |
| Duplicate provider blocks | `Error: Duplicate provider configuration` | Keep one provider block per directory |
| Syntax errors | Terraform refuses to parse the file | Run `terraform validate` to catch errors early |
| Running `apply` without reviewing `plan` | Unexpected infrastructure changes | Always read the plan output before confirming |

---

## ✅ Key Outcome

A fully working Terraform configuration was written and applied. AWS now has a key pair named `nautilus-kp`, and the private key is saved locally at `/home/bob/nautilus-kp.pem` with secure read-only permissions. Most importantly, this was done entirely through code — not a single button was clicked in the AWS console. 🎉
