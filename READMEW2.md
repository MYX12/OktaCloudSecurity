# Week 2 — Okta ↔ AWS Federation (SAML) & MFA Enforcement

## 🎯 Objectives
- Configure **Okta as IdP** and **AWS as SP** via **SAML 2.0**.
- Map **Okta Groups → AWS IAM Roles** to enforce **least privilege**.
- Enforce **MFA** and conditional access for privileged roles.
- Collect **audit evidence** (Okta System Log + AWS CloudTrail).

---

## ✅ Prerequisites
- Completed **Week 1**: Users, Groups, and Policies created.
- Okta Admin access & AWS Admin access.
- Naming convention (enterprise-style):
  - Okta Groups: `GRP_AWS_Admins`, `GRP_AWS_ReadOnly`, `GRP_AWS_Developers`
  - AWS SAML Provider: `OktaIdP`
  - AWS IAM Roles: `ROLE_AWS_Admin`, `ROLE_AWS_ReadOnly`, `ROLE_AWS_Dev`

---

## 🛠️ Step-by-Step Guide

### 1) Create the AWS SAML App in Okta
- Go to **Applications → Create App Integration** → select **SAML 2.0**.
- App Name: `AWS-Prod-Federation`.
- **Single sign-on URL**: `https://signin.aws.amazon.com/saml`
- **Audience URI (SP Entity ID)**: `urn:amazon:webservices`
- **NameID format**: `EmailAddress` → `user.email`
- **Attribute Statements**:
  - `https://aws.amazon.com/SAML/Attributes/RoleSessionName` = `user.email`
  - `https://aws.amazon.com/SAML/Attributes/SessionDuration` = `3600`

📸 *Deliverable:* Screenshot of SAML app configuration.

---

### 2) Download IdP Metadata from Okta
- In the AWS app → **Sign On** tab → **View SAML setup instructions**.
- Download the **IdP metadata XML** or copy the Metadata URL.

📄 *Deliverable:* Save XML/URL link into repo.

---

### 3) Create SAML Provider in AWS
- Console path: IAM → Identity providers → Add provider → Type: SAML.
  - Name: `OktaIdP`
  - Upload the metadata XML.

- CLI example (optional):

        aws iam create-saml-provider \
          --name OktaIdP \
          --saml-metadata-document file://okta_metadata.xml

📸 *Deliverable:* Screenshot of Provider ARN.

---

### 4) Create 3 IAM Roles
- `ROLE_AWS_Admin` → attach `AdministratorAccess` (demo only).
- `ROLE_AWS_ReadOnly` → attach `ReadOnlyAccess`.
- `ROLE_AWS_Dev` → custom minimal dev policy.

- Trust policy template:

        {
          "Version": "2012-10-17",
          "Statement": [{
            "Effect": "Allow",
            "Principal": { "Federated": "arn:aws:iam::<ACCOUNT_ID>:saml-provider/OktaIdP" },
            "Action": "sts:AssumeRoleWithSAML",
            "Condition": {
              "StringEquals": { "SAML:aud": "https://signin.aws.amazon.com/saml" }
            }
          }]
        }

- Example minimal Dev policy:

        {
          "Version": "2012-10-17",
          "Statement": [
            {"Effect":"Allow","Action":["cloudwatch:Get*","cloudwatch:List*"],"Resource":"*"},
            {"Effect":"Allow","Action":["s3:ListAllMyBuckets","s3:ListBucket","s3:GetObject"],"Resource":"*"}
          ]
        }

📸 *Deliverable:* Screenshot of IAM Roles + copy ARNs.

---

### 5) Configure Okta Group → Role Mapping
- In Okta AWS App → **Assignments**.
- Add mapping (RoleARN,ProviderARN):

        GRP_AWS_Admins → arn:aws:iam::<ACCOUNT_ID>:role/ROLE_AWS_Admin,arn:aws:iam::<ACCOUNT_ID>:saml-provider/OktaIdP
        GRP_AWS_ReadOnly → arn:aws:iam::<ACCOUNT_ID>:role/ROLE_AWS_ReadOnly,arn:aws:iam::<ACCOUNT_ID>:saml-provider/OktaIdP
        GRP_AWS_Developers → arn:aws:iam::<ACCOUNT_ID>:role/ROLE_AWS_Dev,arn:aws:iam::<ACCOUNT_ID>:saml-provider/OktaIdP

📸 *Deliverable:* Screenshot of Assignments.

---

### 6) Test SSO & Role Switching
- Sign in as **Alice (Admins)** → should land in AWS Console with Admin role.
- Sign in as **Bob (ReadOnly)** → should only have ReadOnly permissions.
- Try an Admin-only action as Bob (e.g., create S3 bucket) → should fail.

📸 *Deliverables:*
- Okta AWS app role picker.
- AWS Console (federated login).
- Bob fails when trying Admin action.

---

### 7) Enforce MFA & Conditional Access
- Ensure factors enabled in **Security → Multifactor** (Okta Verify, TOTP, etc.).
- Go to AWS App → **Sign On Policy** → Add Rule:
  - Apply to group: `GRP_AWS_Admins`
  - Require **Password + Okta Verify**
  - Optionally add network/geo conditions

📸 *Deliverable:* Screenshot of Sign-On Policy enforcement.

---

### 8) Collect Audit Evidence
- **Okta System Log:** filter by AWS app → export CSV of sign-ins (user, time, MFA, IP).
- **AWS CloudTrail:** search `AssumeRoleWithSAML` → confirm federated login events.
- (Optional) Use CloudWatch Logs Insights for cleaner reporting.

📄 *Deliverables:*
- `Audit_Evidence.md` (queries + screenshots).

---

## 🧪 Acceptance Checklist
- [ ] Okta SAML App created and configured.
- [ ] AWS SAML Provider + IAM Roles created.
- [ ] Group→Role mapping completed.
- [ ] Alice (Admin) can access AWS with MFA enforced.
- [ ] Bob (ReadOnly) limited access confirmed.
- [ ] Logs exported from Okta & CloudTrail.

---

## 🆘 Troubleshooting
- **No roles appear?** Check RoleARN,PrincipalARN order in assignment.
- **Looping redirect?** Verify ACS URL and Audience URI.
- **MFA not enforced?** Ensure correct Sign-On policy applied.
- **Permissions too broad?** Replace `AdministratorAccess` with custom policies.

---

## 📦 What to Commit
- `README_week2.md` (this file, completed).
- `/screenshots/week2/` (SAML app, IAM roles, assignments, AWS console).
- `Audit_Evidence.md` (Okta + CloudTrail logs).
- `Runbook_Rollback.md` (break-glass and rollback instructions).
