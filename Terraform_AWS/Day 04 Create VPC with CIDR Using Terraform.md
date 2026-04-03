# 🌐 Terraform Level 1 — Task 4: Building a VPC with a Specific Network Range

> 📌 Today's exercise reinforced the previous lesson on creating AWS VPCs, but with a key twist: using a specific, smaller network range (`192.168.0.0/24`). This was a hands-on exercise in following architectural requirements and understanding how CIDR notation affects the size of a network.

---

## 📚 Table of Contents

- [🎯 The Goal](#the-goal)
- [🔧 My Step-by-Step Process](#my-step-by-step-process)
- [💡 What & Why](#what--why)
- [🔬 Deep Dive: The `aws_vpc` Resource & CIDR Block](#deep-dive)
- [⚠️ Watch Out For These](#watch-out)
- [📟 Commands Breakdown](#commands-breakdown)

---

## 🎯 The Goal

My objective was to use Terraform to set up a new AWS VPC with very specific parameters:

| # | Requirement |
|---|-------------|
| 1️⃣ | All code must be in a single `main.tf` file |
| 2️⃣ | The VPC's display name in AWS must be `datacenter-vpc` |
| 3️⃣ | Must be set up in the `us-east-1` region |
| 4️⃣ | Must use the IPv4 CIDR block: `192.168.0.0/24` |

---

## 🔧 My Step-by-Step Process

### 🖊️ Phase 1: Writing the Code

Inside the `/home/bob/terraform` folder, I built my `main.tf` file — making sure no other `.tf` files were present.

```terraform
# ✅ Set the AWS Provider & Region
provider "aws" {
  region = "us-east-1"
}

# ✅ Set up the VPC Resource with the required CIDR
resource "aws_vpc" "datacenter_vpc_resource" {
  cidr_block = "192.168.0.0/24"

  tags = {
    Name = "datacenter-vpc"
  }
}
```

---

### ⚙️ Phase 2: The Terraform Workflow

From the terminal in the same folder, I ran three core commands:

#### 🔹 Step 1 — `terraform init`
Downloaded the AWS provider plugin and prepared the working folder.

```bash
terraform init
```

#### 🔹 Step 2 — `terraform plan`
A "dry run" that previewed exactly what Terraform would build — confirming one `aws_vpc` resource with the `192.168.0.0/24` CIDR block.

```bash
terraform plan
```

#### 🔹 Step 3 — `terraform apply`
Executed the plan. After confirming with `yes`, Terraform built the VPC in the cloud.

```bash
terraform apply
```

> ✅ **Success message:** `Apply complete! Resources: 1 added, 0 changed, 0 destroyed.`

---

## 💡 What & Why

### 🏗️ VPC (Virtual Private Cloud)
A **private, isolated network** in the AWS cloud. It is the foundational container for all other resources like servers and databases, providing network-level security and control.

### 🧮 CIDR Block — `192.168.0.0/24` Explained

| Element | Meaning |
|---------|---------|
| `192.168.0.0` | The starting IP address of the network |
| `/24` | First 24 bits are fixed → 8 bits left for hosts |
| `2⁸ = 256` | Total available IP addresses |
| Range | `192.168.0.0` → `192.168.0.255` |

> 📊 **Compared to `/16`** (65,536 addresses), a `/24` is much smaller and more precise — ideal for a segmented, controlled environment.

---

## 🔬 Deep Dive: The `aws_vpc` Resource & CIDR Block

```terraform
# 🔷 The provider block tells Terraform which cloud API to connect to.
provider "aws" {
  region = "us-east-1"   # ← Required; specified in the task
}

# 🔷 The resource block defines one piece of infrastructure.
# "aws_vpc"                 → Resource TYPE (tells Terraform: build a VPC)
# "datacenter_vpc_resource" → Local NAME (used inside Terraform code only)
resource "aws_vpc" "datacenter_vpc_resource" {

  # 🔑 Required argument — sets the private IP address range of the VPC
  cidr_block = "192.168.0.0/24"

  # 🏷️ Tags are key-value labels for your cloud resources
  tags = {
    # ✨ "Name" tag = the display name shown in the AWS Console
    Name = "datacenter-vpc"
  }
}
```

---

## ⚠️ Watch Out For These

| ❌ Mistake | 💬 What Happens |
|-----------|----------------|
| Using the wrong CIDR (e.g., `10.0.0.0/16`) | Validation script will fail |
| Typing CIDR incorrectly (e.g., missing `/24`) | AWS API rejects the value during `plan` or `apply` |
| Skipping `terraform init` | `plan` and `apply` will fail — provider plugins not downloaded |
| Having multiple `.tf` files with conflicts | Terraform may apply unintended configurations |

---

## 📟 Commands Breakdown

| 🖥️ Command | 📋 Purpose |
|-----------|-----------|
| `terraform init` | Prepares the folder by downloading required provider plugins |
| `terraform plan` | Shows a preview of what will be built — no changes made yet |
| `terraform apply` | Executes the plan and builds resources in AWS |
| `terraform destroy` | Tears down all resources managed by Terraform |

---

> 🏁 **Task 4 — Complete!** A VPC named `datacenter-vpc` with CIDR `192.168.0.0/24` was successfully built in `us-east-1` using Terraform. 🎉
