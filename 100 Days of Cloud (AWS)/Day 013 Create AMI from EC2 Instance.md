# ☁️ AWS Day 13 — Create an AMI from an EC2 Instance

> 📸 Capture a complete snapshot of your EC2 instance and turn it into a reusable image — ready to spin up identical servers on demand.

---

## 📋 Table of Contents

- [Objective](#objective)
- [What is an AMI?](#what-is-an-ami)
- [Steps](#steps)
- [What Happens Behind the Scenes](#what-happens-behind-the-scenes)
- [Key Outcome](#key-outcome)

---

## 🎯 Objective

| Setting         | Value                 |
|-----------------|-----------------------|
| Source Instance | `datacenter-ec2`      |
| AMI Name        | `datacenter-ec2-ami`  |
| Region          | `us-east-1`           |
| Expected State  | `available`           |

---

## 🖼️ What is an AMI?

An **Amazon Machine Image (AMI)** is a reusable template for launching EC2 instances. Think of it as a photograph of a fully configured server — OS, installed packages, configuration files, and all.

Every AMI bundles three things:

| Component | Description |
|-----------|-------------|
| 🐧 OS Image | The base operating system (Amazon Linux, Ubuntu, etc.) |
| ⚙️ App Configuration | All software and settings installed inside the instance |
| 💾 Block Device Mapping | The storage layout — which EBS volumes get attached on launch |

Once created, the AMI can be used to launch as many identical instances as needed — making it the foundation of scaling, disaster recovery, and golden image pipelines.

---

## 🚀 Steps

### Step 1 — Open EC2

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Switch the region to **us-east-1 (N. Virginia)**.
3. Navigate to **EC2 → Instances**.

### Step 2 — Select the Source Instance

🔍 Find `datacenter-ec2` in the instances list and tick the checkbox beside it.

### Step 3 — Kick Off AMI Creation

1. Click **Actions → Image and templates → Create image**.
2. Set the **Image name** to `datacenter-ec2-ami`.
3. Leave all other settings at their defaults.
4. Click **Create image**. ✅

AWS will immediately begin taking snapshots of the instance's attached EBS volumes to build the image.

### Step 4 — Monitor the AMI Status

1. In the left sidebar, go to **Images → AMIs**.
2. 🔍 Locate `datacenter-ec2-ami`.
3. Wait until the **State** column reads `available`.

> ⏳ The AMI may show `pending` for a few minutes while AWS finishes capturing the volume snapshots. This is normal — no action needed.

---

## 🔍 What Happens Behind the Scenes

Understanding the internal process helps explain why AMI creation takes a few minutes and why it is storage-efficient:

```
EC2 Instance
    ↓
AWS identifies all attached EBS volumes
    ↓
Point-in-time snapshots are created for each volume
    ↓
Snapshots are stored internally in S3 (managed by AWS)
    ↓
AMI metadata template is built, referencing those snapshots
    ↓
State → available ✅
```

When a new instance is later launched from this AMI, AWS reconstructs the EBS volumes from the stored snapshots — no full disk copy required upfront.

### 🛠️ Common Use Cases for AMIs

| Use Case | How AMIs Help |
|----------|---------------|
| 🔁 Infrastructure Replication | Launch identical servers across environments |
| 📈 Auto Scaling | Use as a launch template for scaling groups |
| 🛡️ Disaster Recovery | Restore a server to a known-good state quickly |
| 🏗️ Golden Image Pipelines | Bake a pre-configured base image for all new deployments |

---

## ✅ Key Outcome

The AMI `datacenter-ec2-ami` has been created from `datacenter-ec2` in `us-east-1` and reached the `available` state. It now serves as a complete, reusable image of the original server — ready to launch identical instances whenever needed.
