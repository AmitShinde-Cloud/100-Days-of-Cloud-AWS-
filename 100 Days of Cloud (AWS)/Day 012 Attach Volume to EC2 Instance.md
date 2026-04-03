# ☁️ AWS Day 12 — Attach an EBS Volume to an EC2 Instance

> 💾 Expand your EC2 instance's storage by connecting a standalone EBS volume — ready to be formatted and mounted as a fully usable filesystem.

---

## 📋 Table of Contents

- [Objective](#objective)
- [What Happens When You Attach a Volume?](#what-happens-when-you-attach-a-volume)
- [Steps](#steps)
- [Things to Know Before You Attach](#things-to-know-before-you-attach)
- [Key Outcome](#key-outcome)

---

## 🎯 Objective

| Setting       | Value           |
|---------------|-----------------|
| Instance Name | `devops-ec2`    |
| Volume Name   | `devops-volume` |
| Device Name   | `/dev/sdb`      |
| Region        | `us-east-1`     |

---

## 💡 What Happens When You Attach a Volume?

Attaching an EBS volume is the cloud equivalent of plugging in a new hard drive. AWS connects the volume to the instance over the network, and it shows up inside the OS as a block device. From there, the volume needs to be:

1. 🔧 **Formatted** — if it is brand new
2. 📂 **Mounted** — assigned a directory path inside the OS

Until those steps are done inside the instance, the volume is connected but not usable.

---

## 🚀 Steps

### Step 1 — Open EC2

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Switch the region to **us-east-1 (N. Virginia)**.
3. Navigate to **EC2**.

### Step 2 — Find the Volume

1. In the left sidebar, scroll to the **Elastic Block Store** section.
2. Click **Volumes**.
3. 🔍 Locate the volume tagged `devops-volume`.

### Step 3 — Attach the Volume

1. Tick the checkbox next to `devops-volume`.
2. Click **Actions → Attach volume**.
3. In the **Instance** search box, select `devops-ec2`.
4. Clear the default device name and type `/dev/sdb`.
5. Click **Attach volume**. ✅

### Step 4 — Verify the Attachment

Refresh the volumes list and confirm the following:

| Column                 | Expected Value                        |
|------------------------|---------------------------------------|
| State                  | `In-use` ✅                           |
| Attachment information | Shows instance ID + `/dev/sdb` ✅     |

---

## ⚙️ Things to Know Before You Attach

### 🗺️ Availability Zone Must Match
An EBS volume can only be attached to an instance that sits in the **same Availability Zone**. A volume in `us-east-1a` cannot reach an instance in `us-east-1b`. Always verify both resources are in the same AZ before attempting to attach.

### 🏷️ Device Naming on Linux
You assign the device name `/dev/sdb` in the AWS Console, but modern Linux kernels running the NVMe driver may rename the device internally — for example, to `/dev/nvme1n1`. To avoid broken mounts after a reboot, always reference the volume by its **UUID or label** inside `/etc/fstab` rather than the device path.

### ⚡ Instance State
An EBS volume can be attached whether the instance is `running` or `stopped`. No restart is required — the OS will detect the new block device once it is connected.

---

## ✅ Key Outcome

The `devops-volume` EBS volume is now attached to `devops-ec2` in `us-east-1` at device path `/dev/sdb`. The volume state has moved from `Available` to `In-use` and is ready to be formatted and mounted inside the instance.
