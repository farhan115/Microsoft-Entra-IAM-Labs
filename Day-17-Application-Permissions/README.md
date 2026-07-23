# Day 17 – Microsoft Entra ID Application Permissions

## Objective

Explore API permissions for a Microsoft Entra application and understand how delegated and application permissions are used.

---

## Lab Environment

- Microsoft Entra ID
- App Registration: IAM-Lab-App

---

## Tasks Performed

- Opened the IAM-Lab-App registration.
- Reviewed existing API permissions.
- Added the Microsoft Graph delegated permission `User.ReadBasic.All`.
- Compared delegated and application permissions.

---

## Key Concepts

### API Permissions

API permissions define what resources an application can access.

### Delegated Permissions

Delegated permissions allow an application to access data on behalf of a signed-in user. The effective permissions are limited by both the user's privileges and the permissions granted to the application.

### Application Permissions

Application permissions allow an application to access resources without a signed-in user. They are commonly used by background services and usually require administrator consent.

---

## Skills Learned

- API Permissions
- Microsoft Graph
- App Registration Management
- Delegated vs Application Permissions
- Microsoft Entra Administration

---

## Screenshots

1. IAM-Lab-App Overview
2. API Permissions
3. Added User.ReadBasic.All Permission

---

## Conclusion

Reviewed Microsoft Graph API permissions and learned how delegated and application permissions support different authentication and authorization scenarios.
