# Week 1 — Okta Fundamentals & Protocol Quickstart

## 🎯 Learning Goals
- Get familiar with the Okta Admin Console and its core objects (**Users**, **Groups**, **Policies**, **Applications**).
- Understand the differences between **SAML 2.0** and **OIDC** (purpose, tokens, typical flows).
- Prepare the foundation for Okta ↔ AWS federation in Week 2.

---

## ✅ Prerequisites
- Free **Okta Developer** account → https://developer.okta.com/signup/  
- Basic understanding of identity concepts (user, group, policy), and web login flows.
- A place to store artifacts (GitHub repo or local folder) for screenshots and notes.

---

## 🛠️ Hands-On Steps

### 1) Environment Setup
1. Sign up for a free Okta Developer org.
2. Record your org URL (e.g., `https://dev-123456.okta.com`).  
3. Explore the Admin Console:
   - **Directory → People** (users)
   - **Directory → Groups**
   - **Security → Authentication → Policies**
   - **Applications → Applications**

📸 *Deliverable:* Screenshot of Admin Console landing page (blur/redact personal info if needed).

---

### 2) Users & Groups
1. Create two users:
   - `alice@demo.co` (Admin persona)
   - `bob@demo.co` (Read-only persona)
2. Create three groups:
   - `GRP_AWS_Admins`
   - `GRP_AWS_ReadOnly`
   - `GRP_AWS_Developers`
3. Assign users to groups:
   - Alice → `GRP_AWS_Admins`
   - Bob → `GRP_AWS_ReadOnly`

📸 *Deliverables:*  
- Screenshot: Users list  
- Screenshot: Groups list  
- Screenshot: Group membership

---

### 3) Authentication Policies (MFA/Conditional)
1. Create two policies:
   - `POL_Admins` → **MFA required** (Okta Verify/TOTP/WebAuthn)
   - `POL_ReadOnly` → single-factor (for demo), or softer rules
2. Do **not** bind to apps yet (we’ll attach in Week 2 to the AWS app).

📸 *Deliverables:*  
- Screenshot: Policy list  
- Screenshot: MFA factor settings and rule conditions

---

## 🔐 Protocol Quick Reference (Interview-Ready)

### SAML 2.0
- **Format:** XML  
- **Use case:** Enterprise **Web SSO** (IdP → SP)  
- **Output:** SAML **Assertion** (contains attributes/claims)

**Typical Flow:**
1. User accesses Service Provider (e.g., AWS console).
2. SP redirects to Identity Provider (Okta) for authentication.
3. Okta authenticates user and returns a SAML Assertion to SP.
4. SP validates assertion → user gains access.

### OIDC (OpenID Connect)
- **Format:** JSON / JWT (built on OAuth 2.0)  
- **Use case:** Modern web/mobile **apps & APIs**  
- **Tokens:** **ID Token** (who you are), **Access Token** (what you can call)

**Typical Flow:**
1. App redirects user to Okta for authentication.
2. Okta returns **JWT tokens** to the app.
3. App calls APIs using the Access Token; ID Token identifies the user.

### Comparison Table
| Feature         | SAML 2.0                       | OIDC (OpenID Connect)         |
|-----------------|--------------------------------|--------------------------------|
| Data Format     | XML                            | JSON/JWT                       |
| Primary Use     | Enterprise Web SSO (IdP→SP)    | Modern Apps & APIs             |
| Token/Artifact  | SAML Assertion                 | ID Token & Access Token        |
| Typical Targets | AWS console, legacy web apps   | SPA/mobile/API-backed services |

📄 *Deliverable:* Add your own mini-sequence diagrams for both flows (can be hand-drawn or made in draw.io).

---

## 🧪 Acceptance Checklist (Week 1 Done)
- [ ] Okta org created and accessible
- [ ] Users (Alice/Bob) and groups created
- [ ] Policies drafted (MFA for Admins; lighter for ReadOnly)
- [ ] SAML vs OIDC notes + simple diagrams
- [ ] All screenshots saved to repo/folder
