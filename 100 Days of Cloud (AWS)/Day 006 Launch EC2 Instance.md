# AWS Day 06 — Launch an EC2 Instance

> Provision a virtual server on AWS using the EC2 service. Two approaches are covered: the AWS Management Console and the AWS CLI.

---

## Table of Contents

- [Objective](#objective)
- [Component Overview](#component-overview)
- [Method 1: AWS Management Console](#method-1-aws-management-console)
- [Method 2: AWS CLI](#method-2-aws-cli)
- [Key Outcome](#key-outcome)

---

## Objective

Launch an EC2 instance with the following configuration:

| Setting        | Value          |
|----------------|----------------|
| Instance Name  | `nautilus-ec2` |
| AMI            | Amazon Linux 2 |
| Instance Type  | `t2.micro`     |
| Key Pair       | `nautilus-kp`  |
| Security Group | `default`      |
| Region         | `us-east-1`    |

---

## Component Overview

| Component | Description |
|-----------|-------------|
| **EC2** | Virtual server running on AWS infrastructure — has CPU, memory, networking, and storage |
| **AMI** | OS template used to boot the instance (Amazon Linux 2 in this case) |
| **Instance Type** | Determines hardware resources — `t2.micro` provides 1 vCPU and 1 GB RAM |
| **Key Pair** | SSH authentication — AWS stores the public key, you keep the private key |
| **Security Group** | Virtual firewall controlling inbound and outbound traffic to the instance |

---

## Method 1: AWS Management Console

### Step 1 — Open EC2

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Set the region to **us-east-1 (N. Virginia)**.
3. Navigate to **EC2**.

### Step 2 — Start the Launch Wizard

Click **Launch instance** from the EC2 dashboard.

### Step 3 — Configure Instance Details

| Field         | Value             |
|---------------|-------------------|
| Name          | `nautilus-ec2`    |
| AMI           | Amazon Linux 2    |
| Instance Type | `t2.micro`        |

### Step 4 — Create a Key Pair

| Field          | Value        |
|----------------|--------------|
| Key pair name  | `nautilus-kp` |
| Key type       | RSA          |
| File format    | `.pem`       |

Download the `.pem` file and store it somewhere secure — AWS will not let you download it again.

### Step 5 — Configure Networking

Under **Network Settings**:

- Use the **default VPC** and **default subnet**
- Select **existing security group** → choose `default`

### Step 6 — Launch

Click **Launch Instance**. Navigate to **EC2 → Instances** — the instance should appear with status `Running` within a minute or two.

---

## Method 2: AWS CLI

CLI provisioning is the standard approach for automation and infrastructure scripting.

### Step 1 — Create a Key Pair

```bash
aws ec2 create-key-pair \
  --key-name nautilus-kp \
  --key-type rsa \
  --query 'KeyMaterial' \
  --output text > nautilus-kp.pem \
  --region us-east-1

chmod 400 nautilus-kp.pem
```

`chmod 400` restricts the private key to read-only for the owner — required by SSH.

### Step 2 — Get the Default Security Group ID

```bash
DEFAULT_SG_ID=$(aws ec2 describe-security-groups \
  --filters Name=group-name,Values=default \
  --query 'SecurityGroups[0].GroupId' \
  --output text \
  --region us-east-1)

echo $DEFAULT_SG_ID
# sg-094aae8bf2d6c6a6d
```

### Step 3 — Retrieve the Latest Amazon Linux AMI

```bash
AMI_ID=$(aws ssm get-parameters \
  --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 \
  --query 'Parameters[0].Value' \
  --output text \
  --region us-east-1)

echo $AMI_ID
# ami-0199fa5fada510433
```

Using the SSM Parameter Store path ensures you always get the **latest** Amazon Linux 2 AMI without hardcoding an AMI ID.

### Step 4 — Launch the Instance

```bash
aws ec2 run-instances \
  --image-id $AMI_ID \
  --instance-type t2.micro \
  --key-name nautilus-kp \
  --security-group-ids $DEFAULT_SG_ID \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=nautilus-ec2}]' \
  --region us-east-1
```

**Key fields from the response:**

| Field          | Value                  |
|----------------|------------------------|
| `InstanceId`   | `i-0c2459ecf77232e94`  |
| `State`        | `pending` → `running`  |
| `InstanceType` | `t2.micro`             |
| `KeyName`      | `nautilus-kp`          |
| `PrivateIpAddress` | `172.31.17.121`    |

### Step 5 — Verify the Instance

```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=nautilus-ec2" \
  --query "Reservations[*].Instances[*].[InstanceId,State.Name,PublicIpAddress]" \
  --output table \
  --region us-east-1
```

**Expected output:**

```
-----------------------------------------------------
|                 DescribeInstances                 |
+----------------------+----------+-----------------+
|  i-0c2459ecf77232e94 |  running |  100.53.13.199  |
+----------------------+----------+-----------------+
```

Confirm:

- `running` state ✅
- A public IP address is assigned ✅
- Instance ID matches the one returned at creation ✅

---

## Key Outcome

An EC2 instance named `nautilus-ec2` is running in `us-east-1` using the `t2.micro` type and Amazon Linux 2 AMI. It is secured with the `nautilus-kp` key pair and the default security group.
