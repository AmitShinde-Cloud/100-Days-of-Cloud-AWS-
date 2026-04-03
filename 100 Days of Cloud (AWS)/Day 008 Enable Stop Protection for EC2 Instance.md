# AWS Day 08 — Enable Stop Protection on an EC2 Instance

> Safeguard a running EC2 instance against accidental shutdowns by turning on Stop Protection.

---

## Table of Contents

- [Objective](#objective)
- [What is Stop Protection?](#what-is-stop-protection)
- [Steps](#steps)
- [Stop Protection vs Termination Protection](#stop-protection-vs-termination-protection)
- [Key Outcome](#key-outcome)

---

## Objective

| Setting        | Value            |
|----------------|------------------|
| Instance Name  | `datacenter-ec2` |
| Action         | Enable Stop Protection |
| Region         | `us-east-1`      |

---

## What is Stop Protection?

Stop Protection blocks any attempt to transition an EC2 instance into a `stopped` state — whether that request comes from the Console, CLI, or an SDK. It acts as a safety net for critical instances where unplanned downtime is unacceptable.

---

## Steps

### Step 1 — Open EC2

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Switch the region to **us-east-1 (N. Virginia)**.
3. Head to **EC2 → Instances**.

### Step 2 — Select the Instance

Locate `datacenter-ec2` in the list and tick the checkbox next to it.

### Step 3 — Turn On Stop Protection

1. Click **Actions → Instance settings → Change stop protection**.
2. Tick the **Enable** checkbox.
3. Hit **Save**.

Stop Protection is now active on the instance.

---

## Stop Protection vs Termination Protection

Both attributes guard against administrative mistakes, but they cover different scenarios.

| Feature | Attribute | What it Blocks |
|---------|-----------|----------------|
| Stop Protection | `disableApiStop` | Prevents the instance from being shut down (stopped) |
| Termination Protection | `disableApiTermination` | Prevents the instance from being permanently deleted |

Using both together forms a solid defence for any mission-critical workload — a stopped instance can be restarted, but a terminated one cannot be recovered.

---

## Key Outcome

Stop Protection is now enabled on `datacenter-ec2` in `us-east-1`. Any attempt to stop the instance — from the Console, CLI, or SDK — will be blocked until the protection is explicitly turned off.
