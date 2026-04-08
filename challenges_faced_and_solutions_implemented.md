## ⚠️ Challenges Faced & Solutions Implemented

Throughout the lab, several real-world issues were encountered across **Infrastructure as Code (IaC)**, **Git workflows**, and **IAM configuration**. Below is a breakdown of the challenges and how they were resolved.

---

## 1️⃣ YAML Formatting Errors (Indentation Issues)

### ❗ Problem

The CloudFormation template failed during deployment via GitHub workflow due to **improper YAML indentation**.

### 🔍 Cause

YAML is highly sensitive to spacing and structure. Even a small indentation mistake can:

* Break the template parsing
* Cause CloudFormation stack creation to fail

### 🛠️ Solution

* Carefully reviewed and corrected indentation levels
* Ensured consistent use of spaces (no tabs)
* Validated the template before deployment

### 🧠 Key Lesson

> YAML is strict — **structure matters as much as content**

---

## 2️⃣ Incorrect Template Path in GitHub Workflow

### ❗ Problem

The GitHub workflow failed because the CloudFormation template file could not be located.

### 🔍 Cause

* The file path to the template was either:

  * Incorrect
  * Not aligned with the repository structure

### 🛠️ Solution

* Updated the workflow configuration to point to the correct template path
* Verified repository structure and file locations

### 🧠 Key Lesson

> Automation depends heavily on **accurate file referencing**

---

## 3️⃣ IAM Login Error – Password Change Failure

### ❗ Error Encountered

> *“You may not be authorized to perform this action, or the new password does not comply with the account password policy…”*

---

### 🔍 Initial Assumption (Incorrect)

I initially believed the issue was related to **password complexity requirements**.

### Actions Taken:

* Modified password generation in the CloudFormation template
* Adjusted Secrets Manager configuration
* Redeployed the stack multiple times

---

### 💥 Root Cause

The issue was actually due to **missing IAM permissions**, not password complexity.

👉 IAM users were required to change their password on first login but **lacked permission to do so**.

---

### 🛠️ Solution

Added a policy to allow users to change their own password:

```yaml
PasswordChangePolicy:
  Type: AWS::IAM::ManagedPolicy
  Properties:
    ManagedPolicyName: AllowUserToChangePassword
    PolicyDocument:
      Version: "2012-10-17"
      Statement:
        - Effect: Allow
          Action:
            - iam:GetUser
            - iam:ChangePassword
          Resource: "arn:aws:iam::*:user/${aws:username}"
```

Attached this policy to relevant IAM groups.

---

### 🎉 Outcome

* Users successfully logged in
* Password change worked as expected
* No further authorization errors

---

## 🚀 Final Takeaways

* 🔐 IAM issues are often **permission-related, not configuration-related**
* 🧩 Small YAML mistakes can break entire deployments
* ⚙️ Automation workflows require precise configuration
* 🧠 Always **analyze error messages carefully before assuming causes**

---

## 👨‍💻 Reflection

These challenges improved my ability to:

* Debug CloudFormation templates effectively
* Work with GitHub-based deployment pipelines
* Understand IAM permission boundaries in depth

---
