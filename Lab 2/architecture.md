# 🏗️ Architecture Documentation

## Lab 2: Automating IAM Resource Creation (CloudFormation + GitSync)

---

## 📌 1. Overview

This lab demonstrates the design and implementation of a **secure, scalable IAM architecture** using:

* **AWS CloudFormation (IaC)**
* **AWS Secrets Manager**
* **IAM Users, Groups, and Policies**
* **GitSync for version-controlled deployments**

The solution follows **AWS security best practices** and **role-based access control (RBAC)** principles.

---

## 🎯 2. Objectives

* Automate IAM resource provisioning using Infrastructure as Code
* Implement **group-based permission management**
* Enforce **secure credential handling**
* Ensure **least privilege access**
* Enable **scalable user management**

---

## 🧩 3. High-Level Architecture

```id="diagram-arch"
                +----------------------+
                |     GitHub Repo      |
                | (CloudFormation IaC) |
                +----------+-----------+
                           |
                           v
                +----------------------+
                |   AWS CloudFormation |
                |   (GitSync Enabled)  |
                +----------+-----------+
                           |
        -------------------------------------------
        |                    |                    |
        v                    v                    v
+---------------+   +----------------+   +------------------+
| Secrets       |   | IAM Groups     |   | IAM Users        |
| Manager       |   |                |   |                  |
| (OTP Storage) |   | - S3 Group     |   | - ec2-user1      |
+---------------+   | - EC2 Group    |   | - ec2-user2      |
                    +--------+-------+   | - s3-user        |
                             |           +--------+---------+
                             |                    |
              ---------------------------         |
              |                         |         |
              v                         v         v
     +------------------+      +----------------------+
     | S3 Permissions   |      | EC2 Permissions      |
     | (List Buckets)   |      | (List + Create EC2)  |
     +------------------+      +----------------------+
```

---

## 🔐 4. Core Components

### 4.1 AWS CloudFormation

* Defines infrastructure in a **declarative YAML template**
* Ensures:

  * Repeatability
  * Version control
  * Reduced manual errors

---

### 4.2 GitSync Integration

* CloudFormation template is stored in GitHub
* Enables:

  * Change tracking
  * Collaboration
  * CI/CD readiness

---

### 4.3 AWS Secrets Manager

* Stores **auto-generated temporary password**
* Prevents:

  * Hardcoding credentials
  * Credential exposure in templates

---

### 4.4 IAM Groups (RBAC Design)

#### 🗂️ S3 Group

* Permission:

  * `s3:ListAllMyBuckets`

#### ⚙️ EC2 Group

* Permissions:

  * `ec2:DescribeInstances`
  * `ec2:RunInstances`

---

### 4.5 IAM Users

| User      | Group     | Access Level    |
| --------- | --------- | --------------- |
| ec2-user1 | EC2 Group | EC2 Read/Create |
| ec2-user2 | EC2 Group | EC2 Read/Create |
| s3-user   | S3 Group  | S3 Read Only    |

---

## 🔁 5. Workflow Execution

### Step 1: Code Commit

* CloudFormation template pushed to GitHub

### Step 2: Stack Deployment

* CloudFormation pulls template via GitSync
* Resources are provisioned automatically

### Step 3: Secret Resolution

* Temporary password retrieved dynamically from Secrets Manager

### Step 4: IAM Assignment

* Users are created
* Assigned to appropriate groups
* Permissions inherited via group policies

### Step 5: User Login

* Users log in via AWS Console
* Forced to change password on first login

---

## 🔐 6. Security Architecture

### ✅ Best Practices Implemented

* **Least Privilege Access**

  * Users only access required services

* **Separation of Duties**

  * S3 and EC2 permissions isolated

* **No Hardcoded Credentials**

  * Secrets managed via Secrets Manager

* **Temporary Credentials**

  * Enforced password reset on first login

---

### ⚠️ Risks Mitigated

| Risk                        | Mitigation              |
| --------------------------- | ----------------------- |
| Credential Exposure         | Secrets Manager         |
| Over-permissioning          | Group-based policies    |
| Manual Configuration Errors | CloudFormation          |
| Unauthorized Access         | Restricted IAM policies |

---

## ⚖️ 7. Design Decisions & Trade-offs

### ✅ Why IAM Groups (RBAC)?

* Scalable for multiple users
* Easier permission management

### ❌ Why Not Attach Policies Directly to Users?

* Hard to maintain at scale
* Violates best practices

---

### ✅ Why CloudFormation?

* Native AWS IaC tool
* Fully integrated with IAM and Secrets Manager

### ❌ Why Not Terraform?

* CloudFormation chosen for:

  * Simplicity (for this lab)
  * AWS Associate exam relevance

---

### ✅ Why Secrets Manager?

* Secure and dynamic secret handling

### ❌ Alternative: SSM Parameter Store

* Cheaper but:

  * Less secure for sensitive credentials (without extra config)

---

## 🧪 8. Validation Strategy

Each IAM user was tested for:

* S3 access
* EC2 access

### Expected Results

| User      | S3 Access | EC2 Access |
| --------- | --------- | ---------- |
| s3-user   | ✅ Success | ❌ Denied   |
| ec2-user1 | ❌ Denied  | ✅ Success  |
| ec2-user2 | ❌ Denied  | ✅ Success  |

---

## 📊 9. Scalability Considerations

This architecture can scale by:

* Adding more users → assign to groups
* Extending policies → update group policies
* Adding services → create new groups (e.g., RDS, Lambda)

---

## 🧠 10. Key Takeaways

* IAM Groups enable scalable access control
* CloudFormation ensures consistent infrastructure
* Secrets Manager eliminates credential risks
* RBAC is critical for secure AWS environments

---

## 👨‍💻 Author

**Anthony Bekoe Bankah**

---
