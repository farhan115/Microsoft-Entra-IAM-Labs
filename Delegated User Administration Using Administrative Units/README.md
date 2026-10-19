# Microsoft Entra ID IAM Lab — Delegated User Administration Using Administrative Units

## Lab Overview

This hands-on lab demonstrates how to implement scoped administrative access in Microsoft Entra ID using Administrative Units (AUs) and Role-Based Access Control (RBAC).

The objective was to allow a designated administrator to manage two specific user accounts without granting administrative permissions across the entire Microsoft Entra tenant.

**Platform:** Microsoft Entra ID
**Environment:** Farhan Muntasir IT Labs
**Technology:** Microsoft Entra Administrative Units, RBAC
**Lab Status:** Completed and verified

---

## 1. Business Scenario

An organization has three employees:

- **Alex Wilber** — Delegated User Administrator
- **Bianca Pisani** — Managed User
- **Cameron White** — Managed User

The organization requires Alex to manage Bianca and Cameron's user accounts without granting him administrative access to other employees.

This requirement follows the principle of least privilege.

## 2. Lab Objectives

- Create an Administrative Unit in Microsoft Entra ID.
- Add two designated users to the Administrative Unit.
- Assign a scoped User Administrator role to Alex Wilber.
- Restrict administrative authority to the designated Administrative Unit.
- Verify permitted and restricted management operations.

## 3. Technologies Used

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Administrative Units (AU)
- Role-Based Access Control (RBAC)
- User Administrator role
- Identity and Access Management (IAM)

## 4. Implementation

### Step 1: Created Test User Accounts

Created or prepared three test user accounts:

| User          | Responsibility          |
| ------------- | ----------------------- |
| Alex Wilber   | Delegated administrator |
| Bianca Pisani | Managed user            |
| Cameron White | Managed user            |

These accounts were used to simulate a real organizational identity-management scenario.

### Step 2: Created an Administrative Unit

Navigated to:

Microsoft Entra Admin Center → Entra ID → Roles and administrators → Admin units

Created the Administrative Unit:

**Name:** `AU-Alex-Managed-Users`

**Purpose:** Restrict delegated administration to selected user accounts.

### Step 3: Added Administrative Unit Members

Opened the Administrative Unit and selected:

Users → Add member

Added:

- Bianca Pisani
- Cameron White

Verified that both users appeared in the Administrative Unit membership list.

Alex Wilber was not added as a managed user.

### Step 4: Assigned a Scoped Administrative Role

Navigated to:

Administrative Unit → Roles and administrators → User Administrator

Assigned the User Administrator role to Alex Wilber.

**Verified role configuration:**

| Property         | Configuration         |
| ---------------- | --------------------- |
| Principal        | Alex Wilber           |
| Role             | User Administrator    |
| Scope            | AU-Alex-Managed-Users |
| Assignment state | Active                |
| Membership       | Direct                |
| Duration         | Permanent             |

The assignment was scoped to the Administrative Unit rather than the entire tenant.

### Step 5: Tested Administrative Permissions

Signed in with the delegated administrator account and verified the permissions.

| Test scenario                                                  | Result                     |
| -------------------------------------------------------------- | -------------------------- |
| Manage Bianca Pisani                                           | Passed                     |
| Manage Cameron White                                           | Passed                     |
| Attempt restricted administration of an account outside the AU | Passed — access restricted |

The tests confirmed that Alex could perform permitted administrative operations on the designated accounts while the scoped role did not provide equivalent administrative authority outside the AU.

## 5. Security Concepts Demonstrated

**Role-Based Access Control (RBAC)**

Permissions were granted through the built-in User Administrator role rather than assigning unrestricted tenant-wide administrative access.

**Principle of Least Privilege**

Alex received only the administrative authority needed for the assigned management scope.

**Administrative Units**

The Administrative Unit provided a logical boundary for delegated administration of selected user accounts.

**Delegated Administration**

Administrative responsibilities were distributed without requiring the delegated administrator to hold the Global Administrator role.

**Access Verification**

Permissions were tested to confirm both authorized operations and restrictions.

## 6. Evidence and Screenshots

The following screenshots document the configuration and verification process:

1. Administrative Unit containing Bianca Pisani and Cameron White.
2. User Administrator role assignment for Alex Wilber.
3. Role assignment scope and active status.
4. User directory used during access verification.

Screenshots should be stored in a dedicated `Screenshots/` folder, with sensitive information redacted where appropriate.

## 7. Key Takeaways

Through this lab, I gained practical experience in:

- Configuring Microsoft Entra Administrative Units.
- Implementing scoped RBAC permissions.
- Delegating user administration securely.
- Understanding the difference between directory-wide and Administrative Unit-scoped roles.
- Testing administrative access boundaries.
- Applying least-privilege principles to identity administration.

## 8. Real-World Applications

This configuration is useful in organizations where administrative responsibilities need to be separated by department, team, region, or business unit.

For example, a regional IT administrator may need to manage employees within their region without receiving permissions to administer accounts across the entire organization.

Administrative Units help implement this type of delegated administration.

## 9. Conclusion

Successfully implemented and tested scoped user administration in Microsoft Entra ID using Administrative Units and the User Administrator role.

This lab demonstrates practical knowledge of IAM, access control, delegated administration, and least-privilege security design.

**Lab Result: Successfully Completed**

---

**Author:** Farhan Muntasir
**GitHub:** [https://github.com/farhan115](https://github.com/farhan115)
**Focus Areas:** Identity and Access Management | Microsoft Entra ID | RBAC | Privileged Access | Enterprise Security
