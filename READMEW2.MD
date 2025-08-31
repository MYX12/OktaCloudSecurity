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
**Console path:** IAM → Identity providers → Add provider → Type: SAML.  
- Name: `OktaIdP`  
- Upload the metadata XML.  

**CLI example (optional):**
```bash
aws iam create-saml-provider \
  --name OktaIdP \
  --saml-metadata-document file://okta_metadata.xml
