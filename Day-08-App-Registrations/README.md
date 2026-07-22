# Day 08 – Microsoft Entra ID App Registrations

## Objective

Create an application registration and explore authentication settings, client secrets, and API permissions.

---

## Lab Environment

- Microsoft Entra ID
- Administrator Account

---

## Tasks Performed

- Created a new App Registration named **IAM-Lab-App**.
- Reviewed the Application (Client) ID and Directory (Tenant) ID.
- Configured a Redirect URI.
- Created a Client Secret for application authentication.
- Reviewed Microsoft Graph API permissions.

---

## Key Concepts

### Application (Client) ID
A unique identifier assigned to the application.

### Directory (Tenant) ID
Identifies the Microsoft Entra tenant where the application is registered.

### Redirect URI
The location where users are redirected after successful authentication.

### Client Secret
A confidential credential that allows the application to authenticate itself.

### API Permissions
Permissions that define what Microsoft Graph resources the application can access.

---

## Skills Learned

- App Registrations
- OAuth 2.0
- OpenID Connect (OIDC)
- Client Secrets
- Redirect URI Configuration
- API Permissions
- Microsoft Graph

---

## Screenshots

- App Registration Overview
- Authentication Configuration
- Certificates & Secrets (secret value hidden)
- API Permissions

---

## Security Note

The client secret value was **not uploaded to GitHub** because it is a sensitive credential. In production environments, secrets should be stored securely using solutions such as Azure Key Vault rather than being exposed in source code or documentation.

---

## Conclusion

Successfully created and configured a Microsoft Entra ID App Registration, explored authentication settings, generated a client secret, and reviewed Microsoft Graph API permissions.
