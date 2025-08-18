# Week 1 - Okta Fundamentals & Protocol Quickstart

## 🎯 Learning Goals
- Get familiar with the Okta Admin Console and its core objects (Users, Groups, Policies, Applications).  
- Understand the differences between **SAML 2.0** and **OIDC**.  
- Prepare the foundation for Okta ↔ AWS federation in Week 2.  

---

## 🛠️ Hands-On Steps

### 1. Environment Setup
- Sign up for a free Okta Developer account: [https://developer.okta.com/signup/](https://developer.okta.com/signup/)  
- Access the tenant URL: `https://dev-xxxxxx.okta.com`  
- Explore the Admin Console navigation:  
  - *Directory → People (Users)*  
  - *Directory → Groups*  
  - *Security → Authentication Policies*  
  - *Applications → Applications*  

### 2. Users and Groups
- Created 2 sample users:  
  - `Alice`  
  - `Bob`  
- Created 2 groups:  
  - `Admins` (requires MFA)  
  - `ReadOnlyUsers` (basic access)  
- Assigned users to groups.  

📸 **Screenshots to include**: User list, Group list, Assignments.  

### 3. Authentication Policies
- Created an MFA policy that applies to the **Admins** group.  
- Set basic single-factor access for the **ReadOnlyUsers** group.  

📸 **Screenshots to include**: MFA policy, conditional rules.  

---

## 🔐 Protocol Quick Reference

### SAML 2.0
- **Protocol**: XML-based.  
- **Use case**: Enterprise Web SSO (Okta → AWS, Office 365).  
- **Output**: SAML Assertion.  

**Flow:**  
1. User requests access to AWS (Service Provider).  
2. AWS redirects to Okta (Identity Provider).  
3. Okta authenticates the user, issues a SAML Assertion.  
4. AWS validates and grants access.  

### OIDC (OpenID Connect)
- **Protocol**: JSON/JWT, built on top of OAuth 2.0.  
- **Use case**: Modern web/mobile apps, APIs.  
- **Output**: ID Token + Access Token.  

**Flow:**  
1. User logs in via application.  
2. Application redirects to Okta (Identity Provider).  
3. Okta returns a JWT (ID Token + Access Token).  
4. Application uses tokens to access APIs.  

### Comparison Table
| Feature       | SAML 2.0                      | OIDC (OpenID Connect)        |
|---------------|-------------------------------|-------------------------------|
| Data Format   | XML                           | JSON/JWT                      |
| Common Use    | Enterprise Web SSO            | SaaS / API / Mobile Apps      |
| Token Type    | SAML Assertion                | ID Token + Access Token       |
| Adoption      | Legacy enterprise systems      | Cloud-native & modern apps    |

📸 **Diagram to include**: SAML flow & OIDC flow.  

---

## ✅ Week 1 Deliverables
- Okta developer environment set up.  
- Users, groups, and MFA policies configured.  
- Protocol notes (SAML vs OIDC) with diagrams.  
- This README documentation.  

---

## 🚀 Key Takeaways
1. Okta identity management is built on **Users → Groups → Policies → Applications**.  
2. **SAML 2.0** = enterprise-grade Web SSO, XML-based.  
3. **OIDC** = modern API login, JSON/JWT-based.  
4. MFA and conditional policies are essential for Zero Trust.  
5. This setup prepares the foundation for AWS federation in Week 2.  
