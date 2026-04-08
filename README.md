# 🔐 AWS IAM, Automation & Infrastructure as Code Labs

## 📌 Overview

This repository contains two hands-on labs focused on **AWS Identity and Access Management (IAM)**, **Automation**, and **Infrastructure as Code (IaC)** using **AWS CloudFormation**.

The labs are designed to build practical skills in:

* Managing IAM users, roles, and permissions
* Applying security best practices (least privilege, role-based access)
* Automating infrastructure deployment using CloudFormation
* Integrating Git-based workflows (GitSync)

These exercises align with **AWS Certified Solutions Architect – Associate** exam objectives and real-world cloud engineering practices.

---

## 🧪 Lab 1: Testing IAM User Permissions

### 🎯 Objective

To create and validate IAM user permissions using:

* AWS CloudShell
* AWS CLI (local machine)
* Managed and inline IAM policies

---

### 🏗️ What Was Implemented

#### 1. IAM User Creation

* Created IAM user via **AWS CloudShell**
* Generated **Access Keys** for programmatic access
* Attached AWS managed policy:

  * `AmazonS3ReadOnlyAccess`

#### 2. AWS CLI Configuration (Local Machine)

* Configured CLI profile using:

  ```bash
  aws configure --profile lab-user
  ```
* Verified identity:

  ```bash
  aws sts get-caller-identity
  ```

#### 3. Permissions Testing

Validated access using AWS CLI:

| Action             | Expected Result            |
| ------------------ | -------------------------- |
| List S3 Buckets    | ✅ Allowed                  |
| Create S3 Buckets  | ❌ Denied (ReadOnly Policy) |
| List EC2 Instances | ❌ Denied                   |

---

### 🔐 Key IAM Concepts Demonstrated

* Principle of **Least Privilege**
* Difference between:

  * Managed Policies vs Inline Policies
* Programmatic vs Console Access
* IAM authentication using Access Keys

---

### ⚠️ Security Considerations

* Avoid using root account
* Rotate access keys regularly
* Prefer IAM Roles over IAM Users (best practice)

---

## 🚀 Lab 2: Automating IAM Resource Creation (CloudFormation + GitSync)

### 🎯 Objective

To automate IAM resource provisioning using **CloudFormation** and manage infrastructure through Git-based workflows.

---

### ▶️ Lab 2: Setup and Run Procedure

Follow this procedure to deploy and validate Lab 2 in your own AWS account.

#### 1. Fork the repository

Fork this repository to your own GitHub account:

https://github.com/bankah-junior/BEM10-IAM_IaC_Automation_Tools.git

#### 2. Prepare AWS credentials

Create or use an IAM user/access key pair with permissions for:

* CloudFormation stack create/update
* IAM user/group/policy management
* Secrets Manager create/read

Collect:

* AWS Access Key ID
* AWS Secret Access Key

#### 3. Add GitHub repository secrets

In your forked repository, go to:

Settings → Secrets and variables → Actions → Repository secrets

Add these secrets exactly:

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`

#### 4. Confirm AWS region in workflow

Open `.github/workflows/deploy.yml` and verify `aws-region` matches the region where you want to deploy resources.

#### 5. Trigger deployment

Push changes to the `lab2` branch (the workflow is configured to run on this branch).

Wait for a successful run in GitHub Actions.

#### 6. Retrieve IAM users and temporary password

After deployment succeeds:

* Open AWS IAM and confirm users:

  * `ec2-user1`
  * `ec2-user2`
  * `s3-user`

* Open AWS Secrets Manager and retrieve the secret named `lab-user-temp-password`.

#### 7. Sign in and reset password

Sign in as each IAM user with the temporary password, then reset the password when prompted.

#### 8. Validate permissions

Test each user in AWS Console:

* EC2 users should be able to view EC2 and launch instances (based on policy scope)
* S3 user should be able to list S3 buckets

#### 9. Expected access boundaries

Expected least-privilege outcomes:

* `ec2-user1` and `ec2-user2` cannot list S3 buckets
* `s3-user` cannot view EC2 instances

#### 10. Compare with sample outputs

Use screenshots in `Lab2/screenshots` as reference outputs for success/failure validation.

#### Important notes

* If deployment fails in GitHub Actions, check workflow logs first for CloudFormation validation errors.
* If IAM users fail password reset, verify your account password policy and use a compliant new password.
* If permissions appear unchanged, confirm the stack update completed and re-login to refresh session policy context.

---

### 🏗️ Architecture Overview

This lab provisions:

* 🔑 **Secrets Manager**

  * Stores auto-generated temporary password

* 👥 **IAM Groups**

  * `S3Group` → List S3 buckets
  * `EC2Group` → List & Create EC2 instances

* 👤 **IAM Users**

  * `ec2-user1`
  * `ec2-user2`
  * `s3-user`

* 🔐 **Security Features**

  * Console access enabled
  * Temporary password enforced
  * Password reset required on first login

---

### ⚙️ CloudFormation Highlights

* Declarative infrastructure using YAML
* Dynamic secret resolution using:

  ```yaml
  {{resolve:secretsmanager:...}}
  ```
* Group-based permission assignment
* Scalable IAM design (RBAC model)

---

### 🔁 GitSync Integration

* CloudFormation template stored in GitHub
* Enables:

  * Version control
  * Change tracking
  * Team collaboration

---

### 🧪 Validation Testing

Each IAM user was tested for access:

| User      | S3 Access | EC2 Access                              |
| --------- | --------- | --------------------------------------- |
| s3-user   | ✅ Allowed | ❌ Denied                                |
| ec2-user1 | ❌ Denied  | ✅ Allowed                               |
| ec2-user2 | ❌ Denied  | ✅ Allowed (including instance creation) |

---

### 🔐 Key Concepts Demonstrated

* Role-Based Access Control (RBAC)
* Infrastructure as Code (IaC)
* Secure secret management
* IAM group-based permissions
* Automation of identity provisioning

---

## 🧠 Design Decisions & Trade-offs

### ✅ Why IAM Groups?

* Easier to manage permissions at scale
* Reduces duplication

### ❌ Why Not Inline Policies Per User?

* Hard to maintain
* Not scalable

### ✅ Why Secrets Manager?

* Secure storage of sensitive data
* Avoids hardcoding credentials

---

## ⚠️ Best Practices Applied

* Least privilege access
* Separation of concerns (S3 vs EC2 roles)
* No hardcoded credentials
* Enforced password rotation
* Infrastructure versioning via Git

---

## 📂 Repository Structure

```
├── 📁 .github
│   └── 📁 workflows
│       └── ⚙️ deploy.yml
├── 📁 Lab1
│   ├── 📁 Task1
│   │   ├── 🖼️ Anthony_Bekoe_Bankah_Image_1.png
│   │   ├── 🖼️ Anthony_Bekoe_Bankah_Image_2.png
│   │   └── 📝 Note.md
│   ├── 📁 Task2
│   │   ├── 🖼️ Anthony_Bekoe_Bankah_Image_3.png
│   │   ├── 🖼️ Anthony_Bekoe_Bankah_Image_4.png
│   │   ├── 🖼️ Anthony_Bekoe_Bankah_Image_5.png
│   │   ├── 🖼️ Anthony_Bekoe_Bankah_Image_6.png
│   │   ├── 🖼️ Anthony_Bekoe_Bankah_Image_7.png
│   │   └── 📝 Note.md
│   └── 📁 Task3
│       ├── 🖼️ Anthony_Bekoe_Bankah_Image_8.png
│       └── 📝 Note.md
├── 📁 Lab2
│   ├── 📁 screenshots
│   │   ├── 🖼️ ec2-user1_failure.png
│   │   ├── 🖼️ ec2-user1_success.png
│   │   ├── 🖼️ ec2-user2_failure.png
│   │   ├── 🖼️ ec2-user2_success.png
│   │   ├── 🖼️ s3-user_failure.png
│   │   └── 🖼️ s3-user_success.png
│   ├── 📝 architecture.md
│   └── ⚙️ cloudformation-template.yaml
├── 📝 IAMLoginError.md
└── 📝 README.md
```

---

## 🎓 Learning Outcomes

By completing these labs, I gained the ability to:

* Design secure IAM architectures
* Implement and test AWS permissions
* Automate infrastructure using CloudFormation
* Apply AWS security best practices in real-world scenarios

---

## 📎 Deliverables

* ✅ Screenshots for all tasks (Lab 1 & Lab 2)
* ✅ Fully functional CloudFormation template
* ✅ GitHub repository with version-controlled IaC

---

## 👨‍💻 Author

**Anthony Bekoe Bankah**
