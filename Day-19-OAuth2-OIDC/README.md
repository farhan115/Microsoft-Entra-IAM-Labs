# Day 19 – OAuth 2.0 & OpenID Connect (OIDC) in Microsoft Entra ID

## Objective

Learn how Microsoft Entra ID uses OAuth 2.0 and OpenID Connect (OIDC) to authenticate users and authorize applications securely.

---

## Lab Environment

- Microsoft Entra ID (Azure AD)
- Microsoft Entra Admin Center
- App Registration: IAM-Lab-App

---

## Lab Tasks Performed

- Opened the App Registration for IAM-Lab-App.
- Reviewed the App Registration Overview.
- Examined application details, including:
  - Application (Client) ID
  - Object ID
  - Directory (Tenant) ID
  - Supported Account Types
  - Redirect URIs
- Accessed the Authentication configuration.
- Studied OAuth 2.0 and OpenID Connect authentication concepts.

---

## Screenshots

### Screenshot 1
**App Registration Overview**

Shows:
- Application (Client) ID
- Object ID
- Directory (Tenant) ID
- Redirect URIs
- Supported Account Types

### Screenshot 2
**Authentication (Preview)**

Shows:
- Platform Configurations
- Redirect URIs
- Authentication Settings

---

## OAuth 2.0

OAuth 2.0 is an authorization framework that allows applications to securely access protected resources on behalf of a user without exposing the user's password.

### Benefits

- Secure delegated access
- Token-based authentication
- Supports Microsoft Graph API
- Used by cloud and enterprise applications

---

## OpenID Connect (OIDC)

OpenID Connect (OIDC) is an identity layer built on top of OAuth 2.0 that enables user authentication.

Microsoft Entra ID uses OIDC to verify a user's identity and provide Single Sign-On (SSO).

---

## Token Types

### ID Token

- Confirms the user's identity.
- Used during sign-in.
- Contains user profile information.

### Access Token

- Grants permission to access protected APIs.
- Used when calling Microsoft Graph or other APIs.

### Refresh Token

- Requests a new Access Token after the current one expires.
- Allows users to remain signed in without re-entering credentials.

---

## OAuth 2.0 Authorization Flow

```text
User
   │
   ▼
Application
   │
   ▼
Microsoft Entra ID
   │
User Authentication
   │
   ▼
Microsoft Entra ID Issues

• ID Token
• Access Token
• Refresh Token (optional)

   │
   ▼
Application accesses Microsoft Graph or another protected API
```

---

## Key Components Reviewed

- App Registration
- Authentication Settings
- Redirect URI
- Client ID
- Tenant ID
- OAuth 2.0
- OpenID Connect (OIDC)
- Access Tokens
- ID Tokens
- Refresh Tokens

---

## Skills Learned

- Microsoft Entra Application Registration
- Identity and Authentication
- OAuth 2.0 Fundamentals
- OpenID Connect (OIDC)
- Token-Based Authentication
- Microsoft Graph Authentication
- Enterprise Identity Management

---

## Key Takeaways

- Microsoft Entra ID acts as the Identity Provider (IdP).
- OAuth 2.0 provides secure authorization.
- OpenID Connect adds authentication to OAuth 2.0.
- Applications use tokens instead of passwords to securely access resources.
- App Registrations define how applications authenticate with Microsoft Entra ID.

---

## Conclusion

This lab demonstrated how Microsoft Entra ID manages application identities using App Registrations and supports secure authentication through OAuth 2.0 and OpenID Connect. Understanding these concepts is essential for implementing modern identity and access management solutions and preparing for the Microsoft SC-300 certification.
