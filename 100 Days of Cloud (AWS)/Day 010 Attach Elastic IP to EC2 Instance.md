# ☁️ AWS Day 10 — Attach an Elastic IP to an EC2 Instance

> 📌 Give your EC2 instance a permanent public IP address that survives stops, restarts, and hardware changes.

---

## 📋 Table of Contents

- [Objective](#objective)
- [Why Elastic IPs Exist](#why-elastic-ips-exist)
- [Steps](#steps)
- [How Elastic IPs Work](#how-elastic-ips-work)
- [Key Outcome](#key-outcome)

---

## 🎯 Objective

| Setting       | Value                |
|---------------|----------------------|
| Instance Name | `datacenter-ec2`     |
| Elastic IP    | `datacenter-ec2-eip` |
| Region        | `us-east-1`          |

---

## 🌐 Why Elastic IPs Exist

By default, every EC2 instance gets a **dynamic** public IP from the AWS pool — one that changes each time the instance is stopped and restarted. This causes real problems:

- 🔗 DNS records break
- 🔌 External applications lose their connection
- 🔄 Manual reconfiguration is needed every time

An **Elastic IP (EIP)** solves this by providing a **static** public IPv4 address that is tied to your AWS account rather than the underlying hardware. It stays the same no matter what happens to the instance.

---

## 🚀 Steps

### Step 1 — Open EC2

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Switch the region to **us-east-1 (N. Virginia)**.
3. Navigate to **EC2**.

### Step 2 — Find the Elastic IP

1. In the left sidebar, scroll to the **Network & Security** section.
2. Click **Elastic IPs**.
3. 🔍 Locate the entry tagged `datacenter-ec2-eip`.

### Step 3 — Associate the Address

1. Tick the checkbox next to `datacenter-ec2-eip`.
2. Click **Actions → Associate Elastic IP address**.
3. Set **Resource type** to **Instance**.
4. In the **Instance** search box, select `datacenter-ec2`.
5. Click **Associate**. ✅

The Elastic IP is now linked to the instance and will appear as its public IP address.

---

## 💡 How Elastic IPs Work

### 🔁 Persistence Across Restarts

Once attached, the Elastic IP stays with the instance through stops, starts, and instance type changes. No more broken DNS records or lost connections after maintenance.

### 🔀 Failover Flexibility

If the original instance ever goes down, the Elastic IP can be quickly **re-associated** with a replacement instance — making it a lightweight tool for basic failover scenarios.

### 💰 Cost Behaviour

| State | Cost |
|-------|------|
| ✅ Attached to a running instance | **Free** |
| ⚠️ Allocated but not attached | Small hourly charge |
| ⚠️ Attached to a stopped instance | Small hourly charge |

> AWS charges for unattached Elastic IPs to discourage hoarding of public IPv4 addresses. Always release EIPs you are no longer using.

---

## ✅ Key Outcome

The Elastic IP `datacenter-ec2-eip` is now associated with `datacenter-ec2` in `us-east-1`. The instance has a fixed public IP address that will remain consistent across any future stops and restarts.
