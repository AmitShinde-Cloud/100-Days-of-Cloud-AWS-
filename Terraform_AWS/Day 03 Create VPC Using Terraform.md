
# 🏗️ Terraform Day 03 — Laying the Foundation: Your First Cloud Network

> 🌐 Before servers, databases, or any other resource can exist — there must be a network. Today we build that network from scratch using code.

---

## 📋 Table of Contents

- [Goal](#goal)
- [Understanding the VPC](#understanding-the-vpc)
- [Writing the Configuration](#writing-the-configuration)
- [Deploying the Infrastructure](#deploying-the-infrastructure)
- [Anatomy of the Script](#anatomy-of-the-script)
- [Watch Out For](#watch-out-for)
- [Result](#result)

---

## 🎯 Goal

Provision an AWS VPC entirely through Terraform with the following spec:

| Setting      | Value            |
|--------------|------------------|
| Config file  | `main.tf`        |
| VPC Name     | `datacenter-vpc` |
| Region       | `us-east-1`      |
| IP Range     | `10.0.0.0/16`    |

---

## 🌐 Understanding the VPC

### What is a VPC?

A **Virtual Private Cloud** is your own isolated, private network inside AWS — think of it as building your own datacenter floor inside a shared skyscraper. Resources inside it are invisible to the outside world by default. Every server, database, and load balancer you ever create in AWS will live inside a VPC.

### 🔢 Decoding the CIDR Block

The CIDR block defines the **private IP address space** available inside the network.

| Part | Meaning |
|------|---------|
| `10.0.0.0` | Starting IP address of the network |
| `/16` | Network size — controls how many IPs are available |

A `/16` block covers `10.0.0.0` through `10.0.255.255` — a pool of **65,536 private IP addresses**. This is the most common choice for a new VPC and leaves plenty of room to carve out subnets later.

### 🏷️ Why Tags Matter

Tags are key-value labels attached to AWS resources. The `Name` tag is particularly important — its value is what shows up as the display name in the AWS Console. Without it, you end up with a list of anonymous resource IDs that are impossible to manage.

---

## 📄 Writing the Configuration

Inside `/home/bob/terraform`, create `main.tf` with the following content:

```hcl
# 1. Tell Terraform which cloud to talk to
provider "aws" {
  region = "us-east-1"
}

# 2. Define the VPC
resource "aws_vpc" "datacenter_vpc_resource" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "datacenter-vpc"
  }
}
```

> 💡 Make sure no other `.tf` files exist in the directory — Terraform merges all `.tf` files together and will throw a duplicate provider error if the `provider` block appears more than once.

---

## ⚙️ Deploying the Infrastructure

```
init → plan → apply
```

| Step | Command | What it Does |
|------|---------|--------------|
| 🔧 Initialize | `terraform init` | Downloads the AWS provider plugin |
| 🔍 Preview | `terraform plan` | Shows exactly what will be created — no changes yet |
| 🚀 Deploy | `terraform apply` | Builds the VPC in AWS (type `yes` to confirm) |

**Expected output after apply:**

```
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

---

## 🔬 Anatomy of the Script

### Two Names, Two Purposes

```hcl
resource "aws_vpc" "datacenter_vpc_resource" {
#                   ^^^^^^^^^^^^^^^^^^^^^^
#                   Local identifier — only meaningful inside Terraform

  tags = {
    Name = "datacenter-vpc"
#          ^^^^^^^^^^^^^^^
#          This is the display name that appears in the AWS Console
  }
}
```

These are completely separate. The local identifier is how you reference this resource in other parts of your Terraform code. The `Name` tag is purely for human readability inside the AWS Console.

### How CIDR Flows into the VPC

```hcl
resource "aws_vpc" "datacenter_vpc_resource" {
  cidr_block = "10.0.0.0/16"   # Required — Terraform will error without this
  
  tags = {
    Name = "datacenter-vpc"    # Optional but always recommended
  }
}
```

`cidr_block` is the only required argument for an `aws_vpc` resource. Everything else — DNS settings, tenancy, IPv6 — is optional and defaults to sensible values if left out.

---

## ⚠️ Watch Out For

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Invalid CIDR format (e.g. `10.0.0.0` without the mask) | AWS API returns an error at `plan` or `apply` | Always use the `x.x.x.x/n` format |
| Overlapping CIDR with an existing VPC | Networking conflicts when peering or routing between VPCs | Plan your IP ranges before creating multiple VPCs |
| Skipping `terraform init` | Plan and apply fail — provider not downloaded | Always run `init` first in a new directory |
| Missing `Name` tag | VPC appears as an anonymous ID in the console | Tag every resource — it saves a lot of confusion later |

---

## ✅ Result

A VPC named `datacenter-vpc` with the IP range `10.0.0.0/16` is now live in `us-east-1`. This private network is the foundation everything else will be built on — subnets, servers, databases, and more. It was provisioned entirely through code, meaning the same configuration can recreate it in any region or account with a single command. 🎉
