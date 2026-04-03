# 🏗️ Terraform Day 02 — Building a Virtual Firewall with Code

> 🔒 Translate security requirements into a declarative Terraform script — turning firewall rules into version-controlled, reproducible code.

---

## 📋 Table of Contents

- [The Task](#the-task)
- [What is a Security Group?](#what-is-a-security-group)
- [The Code](#the-code)
- [The Workflow](#the-workflow)
- [Breaking Down the Script](#breaking-down-the-script)
- [Common Pitfalls](#common-pitfalls)
- [Key Outcome](#key-outcome)

---

## 🎯 The Task

Create an AWS Security Group using Terraform with the following specification:

| Requirement     | Value                                    |
|-----------------|------------------------------------------|
| Config file     | `main.tf`                                |
| SG Name         | `nautilus-sg`                            |
| Description     | `Security group for Nautilus App Servers` |
| Inbound rule 1  | HTTP — port `80` — source `0.0.0.0/0`   |
| Inbound rule 2  | SSH — port `22` — source `0.0.0.0/0`    |

---

## 🛡️ What is a Security Group?

A **Security Group** acts as a virtual firewall sitting in front of an EC2 instance. It decides what traffic is allowed in and out.

| Direction | Term | Default Behaviour |
|-----------|------|-------------------|
| 🔽 Incoming traffic | `ingress` | **Blocked** — you must explicitly open ports |
| 🔼 Outgoing traffic | `egress` | **Allowed** — all outbound traffic is permitted by default |

### 🌍 What is `0.0.0.0/0`?

This CIDR block means **the entire internet** — any IP address from anywhere. It is fine for port 80 (public web traffic), but in a real production setup, SSH (port 22) should be restricted to a trusted IP range like your office or VPN rather than left open to the world.

---

## 📄 The Code

Inside `/home/bob/terraform`, create `main.tf` with the following content:

```hcl
# 1. Configure the AWS Provider
provider "aws" {
  region = "us-east-1"
}

# 2. Define the Security Group
resource "aws_security_group" "nautilus_sg_resource" {
  name        = "nautilus-sg"
  description = "Security group for Nautilus App Servers"

  # Inbound rule — allow HTTP traffic
  ingress {
    description = "Allow HTTP inbound"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Inbound rule — allow SSH traffic
  ingress {
    description = "Allow SSH inbound"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

---

## ⚙️ The Workflow

```
init → plan → apply
```

| Step | Command | Purpose |
|------|---------|---------|
| 🔧 Initialize | `terraform init` | Downloads the AWS provider plugin |
| 🔍 Plan | `terraform plan` | Dry run — previews what will be created |
| ✅ Apply | `terraform apply` | Builds the resource in AWS (type `yes` to confirm) |

**Expected output after apply:**

```
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

---

## 🔬 Breaking Down the Script

### Resource Names — Two Different Things

```hcl
resource "aws_security_group" "nautilus_sg_resource" {
#                              ^^^^^^^^^^^^^^^^^^^^
#                              Local name — only used inside Terraform code

  name = "nautilus-sg"
#        ^^^^^^^^^^^^
#        This is what appears in the AWS Console
```

These two names serve completely different purposes. Mixing them up is a common source of confusion early on.

### The `ingress` Block

```hcl
ingress {
  description = "Allow HTTP inbound"
  from_port   = 80          # Start of port range
  to_port     = 80          # End of port range (same = single port)
  protocol    = "tcp"       # Must be lowercase
  cidr_blocks = ["0.0.0.0/0"]  # Must be a list, even for one entry
}
```

Multiple `ingress` blocks can be stacked inside the same resource — one block per rule. The same pattern applies to `egress` blocks for outbound rules.

---

## ⚠️ Common Pitfalls

| Mistake | What Happens | Fix |
|---------|-------------|-----|
| SG name already exists in the VPC | `terraform apply` fails with a conflict error | Use a unique name or import the existing resource |
| Missing square brackets on `cidr_blocks` | Syntax error — it expects a list | Always use `["0.0.0.0/0"]` not `"0.0.0.0/0"` |
| Uppercase protocol (`"TCP"`) | Error during plan or apply | AWS requires lowercase — use `"tcp"` |
| Confusing the local name with `name` | Wrong resource gets referenced in other configs | Remember: local name = Terraform only, `name` = AWS console |

---

## ✅ Key Outcome

A Security Group named `nautilus-sg` has been provisioned in AWS via Terraform with two inbound rules — HTTP on port 80 and SSH on port 22. The firewall configuration now lives in code, meaning it can be reviewed, versioned, and reproduced in any environment with a single `terraform apply`. 🎉
