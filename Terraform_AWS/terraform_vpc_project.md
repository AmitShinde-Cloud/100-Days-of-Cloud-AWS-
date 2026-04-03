# 🌐 Terraform Project: Build Your First AWS VPC

## 🏗️ Advanced Architecture Diagram

```svg
<svg width="800" height="400" xmlns="http://www.w3.org/2000/svg">
  <!-- Background -->
  <rect x="0" y="0" width="800" height="400" fill="#f5f7fa"/>

  <!-- AWS Cloud -->
  <rect x="50" y="50" width="700" height="300" rx="20" fill="#e3f2fd" stroke="#2196f3" stroke-width="2"/>
  <text x="350" y="80" font-size="18" fill="#0d47a1">AWS Cloud</text>

  <!-- VPC -->
  <rect x="150" y="120" width="500" height="180" rx="15" fill="#ffffff" stroke="#4caf50" stroke-width="2"/>
  <text x="330" y="140" font-size="16" fill="#2e7d32">VPC (10.0.0.0/16)</text>

  <!-- Public Subnet -->
  <rect x="180" y="180" width="200" height="80" rx="10" fill="#e8f5e9" stroke="#66bb6a"/>
  <text x="210" y="220" font-size="14">Public Subnet</text>

  <!-- Private Subnet -->
  <rect x="420" y="180" width="200" height="80" rx="10" fill="#fff3e0" stroke="#ffa726"/>
  <text x="450" y="220" font-size="14">Private Subnet</text>

  <!-- Internet Gateway -->
  <circle cx="400" cy="320" r="20" fill="#ff7043"/>
  <text x="360" y="360" font-size="12">Internet Gateway</text>

  <!-- Connection Lines -->
  <line x1="400" y1="300" x2="400" y2="260" stroke="#000" stroke-width="2"/>
</svg>
```

---

## 📌 Description

This architecture represents:

- 🌐 AWS Cloud Environment  
- 🏢 VPC (10.0.0.0/16)  
- 🌍 Public Subnet (Internet-facing)  
- 🔒 Private Subnet (Internal services)  
- 🔗 Internet Gateway for outbound access  

---

## ⚙️ Terraform Core

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "datacenter-vpc"
  }
}
```

---

## 🚀 Workflow

```bash
terraform init
terraform plan
terraform apply
```

---

## 💡 Next Upgrade

- Add Subnets
- Attach Internet Gateway
- Deploy EC2
- Use Terraform Modules

