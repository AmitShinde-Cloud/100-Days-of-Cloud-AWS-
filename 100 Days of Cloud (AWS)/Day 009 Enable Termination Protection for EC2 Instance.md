# ☁️ AWS Day 09 — Enable Termination Protection on an EC2 Instance

> 🛡️ Add a critical safeguard to your EC2 instance that prevents it from being accidentally deleted — ever.

---

## 📋 Table of Contents

- [Objective](#objective)
- [What is Termination Protection?](#what-is-termination-protection)
- [Steps](#steps)
- [Operational Guardrails](#operational-guardrails)
- [Key Outcome](#key-outcome)

---

## 🎯 Objective

| Setting       | Value                       |
|---------------|-----------------------------|
| Instance Name | `datacenter-ec2`            |
| Action        | Enable Termination Protection |
| Region        | `us-east-1`                 |

---

## 🔒 What is Termination Protection?

Termination Protection is a guard rail that stops an EC2 instance from being permanently deleted — whether the request originates from the Console, CLI, or any AWS SDK. Once switched on, deletion becomes a deliberate **two-step process**:

1. 🔓 Disable the protection attribute
2. 🗑️ Then terminate the instance

This small friction is intentional — it forces administrators to pause and confirm before wiping out a resource that may be irreplaceable.

---

## 🚀 Steps

### Step 1 — Open EC2

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Switch the region to **us-east-1 (N. Virginia)**.
3. Navigate to **EC2 → Instances**.

### Step 2 — Select the Instance

🔍 Locate `datacenter-ec2` in the instances list and tick the checkbox beside it.

### Step 3 — Turn On Termination Protection

1. Click **Actions → Instance settings → Change termination protection**.
2. ✅ Tick the **Enable** checkbox.
3. Click **Save**.

Termination Protection is now active. Any delete attempt will be blocked immediately.

---

## 🧱 Operational Guardrails

### ⚠️ Auto Scaling Exception

Termination Protection blocks **API-driven** termination commands only. It does **not** stop Amazon EC2 Auto Scaling from terminating an instance when:

- The Auto Scaling group needs to scale down
- The instance is marked unhealthy by the group

If your instance is part of an Auto Scaling group, keep this limitation in mind.

### 🆚 Stop Protection vs Termination Protection

| Feature | What it Blocks |
|---------|----------------|
| 🔴 Stop Protection | Prevents the instance from being **shut down** (stopped) |
| 🗑️ Termination Protection | Prevents the instance from being **deleted** (terminated) |

Together, they form a two-layer defence for any production workload. Stop Protection handles accidental shutdowns; Termination Protection handles accidental deletions.

---

## ✅ Key Outcome

Termination Protection is now active on `datacenter-ec2` in `us-east-1`. Any attempt to delete the instance will be rejected until the protection is deliberately turned off — keeping your infrastructure safe from accidental removal.
