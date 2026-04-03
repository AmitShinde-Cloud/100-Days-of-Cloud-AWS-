# ☁️ AWS Day 14 — Terminate an EC2 Instance

> 🗑️ Permanently remove an obsolete EC2 instance to keep your infrastructure clean and avoid unnecessary charges.

---

## 📋 Table of Contents

- [Objective](#objective)
- [Before You Proceed](#before-you-proceed)
- [Steps](#steps)
- [Stopping vs Terminating](#stopping-vs-terminating)
- [Key Outcome](#key-outcome)

---

## 🎯 Objective

| Setting       | Value            |
|---------------|------------------|
| Instance Name | `datacenter-ec2` |
| Action        | Terminate        |
| Final State   | `terminated`     |
| Region        | `us-east-1`      |

---

## ⚠️ Before You Proceed

Termination is **permanent and irreversible**. Unlike stopping an instance, there is no going back once the terminate command is confirmed.

A few things happen automatically at termination:

- 🗑️ The **root EBS volume** is deleted by default (`DeleteOnTermination = true`)
- 💾 Any **additional EBS volumes** may or may not be deleted depending on their individual settings
- 💸 All instance-related charges stop immediately

If there is any data on the instance that needs to be preserved, create an AMI or a snapshot before proceeding.

---

## 🚀 Steps

### Step 1 — Open EC2

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Switch the region to **us-east-1 (N. Virginia)**.
3. Navigate to **EC2 → Instances**.

### Step 2 — Select the Instance

🔍 Find `datacenter-ec2` in the list and tick the checkbox beside it.

### Step 3 — Terminate

1. Click **Instance state → Terminate instance**.
2. A warning dialog will appear noting that the root EBS volume will be deleted.
3. Click the orange **Terminate** button to confirm. 🔴

### Step 4 — Verify the State

Watch the **Instance state** column transition:

```
Running → Shutting-down → Terminated ✅
```

Wait until the state reads `Terminated` before considering the task complete. The instance will remain visible in the console for a short window before disappearing entirely.

---

## 🆚 Stopping vs Terminating

It is worth understanding the difference — choosing the wrong action can cause data loss or unexpected charges.

| | ⏹️ Stop | 🗑️ Terminate |
|---|---------|------------|
| **Instance data** | EBS volumes preserved | Root volume deleted by default |
| **Instance charges** | Paused ✅ | Stopped completely ✅ |
| **EBS charges** | Continue 💸 | Stopped (for deleted volumes) ✅ |
| **Elastic IP charges** | Continue 💸 | Stopped if EIP is released ✅ |
| **Reversible?** | ✅ Yes — can restart anytime | ❌ No — permanent |

> 💡 **Rule of thumb:** Stop an instance when you plan to use it again. Terminate it when it is no longer needed at all.

---

## ✅ Key Outcome

The `datacenter-ec2` instance in `us-east-1` has been permanently terminated. Its root EBS volume has been removed, all instance charges have stopped, and the infrastructure is cleaner as a result.
