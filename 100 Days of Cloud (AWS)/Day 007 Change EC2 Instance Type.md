# AWS Day 07 — Change EC2 Instance Type

> Resize an existing EC2 instance to optimize cost and resource utilization.

---

## Table of Contents

- [Objective](#objective)
- [The Golden Rule of Resizing](#the-golden-rule-of-resizing)
- [Steps](#steps)
- [Key Outcome](#key-outcome)

---

## Objective

| Setting         | Value         |
|-----------------|---------------|
| Target Instance | `xfusion-ec2` |
| Current Type    | `t2.micro`    |
| New Type        | `t2.nano`     |
| Region          | `us-east-1`   |

**Conditions:**
- Wait for all status checks to pass before making changes.
- Confirm the instance is back in `Running` state after the resize.

---

## The Golden Rule of Resizing

**An instance must be stopped before its type can be changed.**

AWS needs to migrate the virtual machine to different underlying physical hardware that matches the new instance type's specifications. This cannot happen while the instance is running.

The workflow is always: **Stop → Modify → Start**

---

## Steps

### Step 1 — Navigate to EC2

1. Log in to the [AWS Management Console](https://console.aws.amazon.com).
2. Set the region to **us-east-1 (N. Virginia)**.
3. Go to **EC2 → Instances**.

### Step 2 — Wait for Status Checks

Locate `xfusion-ec2` in the instances list. Check the **Status check** column — wait until it reads `2/2 checks passed` before proceeding.

### Step 3 — Stop the Instance

1. Select `xfusion-ec2`.
2. Click **Instance state → Stop instance** and confirm.
3. Wait for the state column to change from `Stopping` to `Stopped`.

### Step 4 — Change the Instance Type

1. With `xfusion-ec2` selected, click **Actions → Instance settings → Change instance type**.
2. Select **t2.nano** from the dropdown.
3. Click **Apply**.

### Step 5 — Start the Instance

1. Click **Instance state → Start instance**.
2. Wait for the state to return to `Running`.

---

## Key Outcome

The `xfusion-ec2` instance in `us-east-1` has been resized from `t2.micro` to `t2.nano` and is back in a `Running` state. The change reduces the instance's resource footprint, optimizing for cost.
