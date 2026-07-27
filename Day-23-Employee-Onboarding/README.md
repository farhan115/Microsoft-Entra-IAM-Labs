# Day 23 – IAM Ticket #001: Employee Onboarding

## Objective

Simulate a real-world Identity and Access Management (IAM) onboarding request by creating a new employee account, assigning group membership, reviewing license availability, and preparing application access in Microsoft Entra ID.

---

## Scenario

**Help Desk Ticket**

A new employee has joined the Finance department and requires access to company resources.

**Employee Details**

- **Name:** Emma Wilson
- **Department:** Finance
- **Job Title:** Financial Analyst
- **Manager:** Alex Brown

---

## Lab Environment

- Microsoft Entra ID (Azure Active Directory)
- Microsoft Entra Admin Center
- Security Groups
- Enterprise Applications
- Microsoft 365 Developer Tenant

---

## Tasks Performed

### 1. Created a New User

Created a new Microsoft Entra ID user account with the required profile information.

**Information Configured**

- Display Name
- User Principal Name (UPN)
- Department
- Job Title

---

### 2. Verified User Account

Confirmed the user account was successfully created and reviewed the account properties.

---

### 3. Created a Security Group

Created a security group named:

**Finance-Users**

---

### 4. Added User to Security Group

Added **Emma Wilson** as a member of the **Finance-Users** security group.

---

### 5. Reviewed Microsoft 365 Licensing

Navigated to the Licenses section to assign a Microsoft 365 license.

**Result**

No Microsoft 365 licenses were available in the Microsoft Entra developer tenant. The user account was successfully created without a license.

---

### 6. Assigned Enterprise Application (If Available)

Assigned the user to an enterprise application available in the tenant (or documented if unavailable).

---

## Skills Demonstrated

- Identity Lifecycle Management
- User Provisioning
- Security Group Management
- Microsoft Entra ID Administration
- License Management
- Enterprise Application Assignment
- IAM Access Provisioning

---

## Screenshots

1. New User Creation
2. User Overview
3. Finance Security Group
4. Group Membership
5. License Review
6. Enterprise Application Assignment

---

## Business Outcome

Successfully completed a simulated employee onboarding request by provisioning a new identity, assigning group membership, reviewing license availability, and preparing application access. This lab demonstrates common IAM analyst responsibilities performed during employee onboarding.

---

## Key Learning Points

- Create and manage Microsoft Entra ID users.
- Organize users with security groups.
- Verify license availability before assignment.
- Understand licensing limitations within a developer tenant.
- Follow a structured IAM onboarding workflow.

---

## Technologies Used

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Microsoft 365 Developer Tenant
- Identity and Access Management (IAM)

---

## Conclusion

This lab simulated a real-world employee onboarding request in Microsoft Entra ID. The user account was successfully provisioned, assigned to the appropriate security group, and evaluated for Microsoft 365 licensing. Although no licenses were available in the lab tenant, the onboarding process followed standard enterprise IAM practices and demonstrates practical identity administration skills.
