# Day 20 – Managed Identities in Microsoft Entra ID

## Objective

Learn how Managed Identities provide secure authentication between Azure resources without requiring passwords or secrets.

---

## Lab Environment

- Microsoft Entra ID
- Azure (Conceptual Lab)

---

## Topics Covered

- Managed Identities
- System-assigned Managed Identity
- User-assigned Managed Identity
- Microsoft Entra ID Authentication
- Azure Key Vault Integration

---

## Types of Managed Identities

### System-assigned Managed Identity

- Created automatically for an Azure resource.
- Linked to only one resource.
- Deleted automatically when the resource is deleted.

### User-assigned Managed Identity

- Created independently.
- Can be attached to multiple Azure resources.
- Exists until manually deleted.

---

## Authentication Flow

Azure Resource

↓

Managed Identity

↓

Microsoft Entra ID

↓

Access Token

↓

Azure Resource (Example: Key Vault)

---

## Benefits

- No stored passwords
- No client secrets
- Automatic credential rotation
- Improved security
- Simplified authentication

---

## Real-World Use Cases

- Azure Virtual Machine accessing Azure Key Vault
- Azure App Service connecting to Azure Storage
- Azure Function calling Microsoft Graph
- Azure Automation accessing Azure SQL Database

---

## Skills Learned

- Managed Identities
- Secretless Authentication
- Microsoft Entra ID
- Azure Security
- Azure Key Vault Authentication

---

## Key Takeaways

- Managed Identities eliminate the need to store credentials in code or configuration.
- Microsoft Entra ID issues access tokens for Azure resources.
- System-assigned identities are tied to one resource.
- User-assigned identities can be shared across multiple resources.

---

## Conclusion

Managed Identities improve the security of Azure workloads by allowing Azure resources to authenticate to other services using Microsoft Entra ID without storing passwords or secrets.
