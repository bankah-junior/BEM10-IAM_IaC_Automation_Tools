## 🐛 Debugging IAM Login Error – Password Change Failure

### ❗ Error Encountered

During the login process for newly created IAM users, the following error was encountered:

> *“You may not be authorized to perform this action, or the new password does not comply with the account password policy set by your administrator.”*

---

## 🔍 Initial Assumption (Incorrect Path)

At first, I assumed the issue was related to **AWS password policy requirements**.

### Actions Taken:

* Modified the **CloudFormation template** multiple times
* Adjusted password generation settings in **AWS Secrets Manager**
* Reviewed AWS password policy requirements (length, complexity, symbols)
* Redeployed the stack several times with updated configurations

Despite these changes, the issue persisted.

---

## 🧠 Root Cause Discovery

After further investigation, I focused on the key part of the error:

> *“You may not be authorized to perform this action…”*

This led to the realization that:

👉 The issue was **not the password itself**,
👉 but rather a **missing IAM permission**.

---

## 💥 Actual Problem

The IAM users were required to:

* Log in with a temporary password
* Change their password on first login

However, they **did not have permission to change their own password**.

---

## ✅ Solution Implemented

I updated the CloudFormation template to include a policy that allows users to change their own password.

### 🔐 Policy Added

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

### 🔗 Attached Policy to IAM Groups

This policy was then attached to both:

* `S3Group`
* `EC2Group`

---

## 🎉 Outcome

After applying the fix:

* IAM users were able to successfully log in
* Password change on first login worked as expected
* No further errors were encountered

---

## 🧠 Key Lessons Learned

* Error messages can be **misleading** — always break them down
* IAM permissions control more than just resource access
* **Password change requires explicit permission (`iam:ChangePassword`)**
* Debugging in AWS often requires checking both:

  * Configuration (passwords, templates)
  * Permissions (IAM policies)

---

## 🚀 Takeaway

This issue reinforced the importance of:

* Understanding **IAM permission boundaries**
* Following a **systematic debugging approach**
* Not assuming the most obvious cause is the correct one

---

## 👨‍💻 Reflection

This debugging process improved my ability to:

* Analyze AWS error messages critically
* Identify permission-related issues
* Apply least-privilege principles effectively

---
