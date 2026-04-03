# AWS Day 05 — Create a GP3 EBS Volume

> Provision a standalone Amazon EBS volume using the **GP3** volume type — a virtual hard disk that can be attached to any EC2 instance for persistent storage.

---

## Table of Contents

- [Objective](#objective)
- [Concept Overview](#concept-overview)
- [Method 1: AWS Management Console](#method-1-aws-management-console)
- [Method 2: AWS CLI](#method-2-aws-cli)
- [EBS Volume Lifecycle](#ebs-volume-lifecycle)
- [Key Outcome](#key-outcome)

---

## Objective

Create an EBS volume with the following configuration:

| Setting           | Value         |
|-------------------|---------------|
| Name Tag          | `devops-volume` |
| Volume Type       | `gp3`         |
| Size              | `2 GiB`       |
| Region            | `us-east-1` (N. Virginia) |

---

## Concept Overview

EBS (Elastic Block Store) provides **block-level storage** — think of it as a virtual hard drive for your EC2 instances. Unlike S3 (object storage), EBS behaves like a physical disk.

Key characteristics:

- Tied to a specific **Availability Zone (AZ)** — it can only be attached to instances in that same AZ.
- **Persists independently** of the EC2 instance lifecycle — data survives instance stops and restarts.
- The `gp3` type is a **general-purpose SSD** that delivers a baseline of **3,000 IOPS** and **125 MiB/s throughput**, regardless of volume size.

---

## Method 1: AWS Management Console

### Step 1 — Open the EC2 Service

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Set the region to **us-east-1 (N. Virginia)**.
3. Navigate to **EC2**.

### Step 2 — Go to Volumes

1. In the left sidebar, under **Elastic Block Store**, click **Volumes**.
2. Click **Create volume**.

### Step 3 — Configure the Volume

| Field             | Value                  |
|-------------------|------------------------|
| Volume Type       | General Purpose SSD (gp3) |
| Size              | 2 GiB                  |
| IOPS              | 3000 (default)         |
| Throughput        | 125 MiB/s (default)    |
| Availability Zone | `us-east-1a` (or your preferred AZ) |

> The Availability Zone is **required** — the volume physically resides there and can only be attached to instances in the same AZ.

### Step 4 — Add a Name Tag

Under **Tags**, add:

- **Key:** `Name`
- **Value:** `devops-volume`

Tags make volumes easier to identify and manage across your AWS account.

### Step 5 — Create the Volume

Click **Create volume**. Once provisioned, the volume state should read **Available**, meaning it's ready to attach to an EC2 instance.

---

## Method 2: AWS CLI

### Step 1 — Create the Volume

```bash
aws ec2 create-volume \
    --volume-type gp3 \
    --size 2 \
    --availability-zone us-east-1a \
    --tag-specifications 'ResourceType=volume,Tags=[{Key=Name,Value=devops-volume}]' \
    --region us-east-1
```

**Expected response:**

```json
{
    "Iops": 3000,
    "Tags": [
        {
            "Key": "Name",
            "Value": "devops-volume"
        }
    ],
    "VolumeType": "gp3",
    "MultiAttachEnabled": false,
    "Throughput": 125,
    "VolumeId": "vol-060f74f55a4d65caa",
    "Size": 2,
    "SnapshotId": "",
    "AvailabilityZone": "us-east-1a",
    "State": "creating",
    "CreateTime": "2026-02-17T00:35:11.000Z",
    "Encrypted": false
}
```

**Flag breakdown:**

| Flag                    | Purpose                                      |
|-------------------------|----------------------------------------------|
| `create-volume`         | API action to provision an EBS volume        |
| `--volume-type gp3`     | Selects the GP3 performance tier             |
| `--size 2`              | Sets capacity in GiB                         |
| `--availability-zone`   | Specifies the physical AZ for placement      |
| `--tag-specifications`  | Attaches the `Name` tag at creation time     |
| `--region us-east-1`    | Targets the correct AWS region               |

### Step 2 — Verify the Volume

```bash
aws ec2 describe-volumes \
    --filters Name=tag:Name,Values=devops-volume \
    --region us-east-1
```

**Expected response:**

```json
{
    "Volumes": [
        {
            "Iops": 3000,
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "devops-volume"
                }
            ],
            "VolumeType": "gp3",
            "MultiAttachEnabled": false,
            "Throughput": 125,
            "VolumeId": "vol-060f74f55a4d65caa",
            "Size": 2,
            "SnapshotId": "",
            "AvailabilityZone": "us-east-1a",
            "State": "available",
            "CreateTime": "2026-02-17T00:35:11.897Z",
            "Attachments": [],
            "Encrypted": false
        }
    ]
}
```

Confirm the following fields in the response:

- `"State": "available"` ✅
- `"Size": 2` ✅
- `"VolumeType": "gp3"` ✅

---

## EBS Volume Lifecycle

```
Created → available → attached (to EC2) → in-use → detached → available
```

1. Volume is created inside a specific AZ.
2. State becomes `available`.
3. It is attached to a running EC2 instance in the same AZ.
4. The OS sees it as a block device (e.g., `/dev/xvdf`).
5. The instance formats and mounts the volume before use.
6. When detached, the volume returns to `available` — **data is retained**.

---

## Key Outcome

A standalone EBS volume named `devops-volume` has been provisioned in `us-east-1` using the `gp3` type with **2 GiB** of storage. It is now in an `available` state and ready to be attached to an EC2 instance for persistent block storage.
