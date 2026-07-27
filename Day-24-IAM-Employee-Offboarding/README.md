# Day 24 – IAM Ticket #002: Employee Offboarding and Access Review

## Objective

Simulate a real-world Identity and Access Management (IAM) employee offboarding request by reviewing user access, removing available permissions, and documenting tenant limitations within Microsoft Entra ID.

---

## Scenario

**Help Desk Ticket**

Employee **Emma Wilson** has left the organization. The IAM team has been instructed to review and revoke the employee's access to organizational resources while documenting any platform limitations.

---

## Lab Environment

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Microsoft Entra ID Free Developer Tenant
- Security Groups
- Enterprise Applications

---

## Tasks Performed

### 1. Reviewed User Account

Verified the employee account before beginning the offboarding process.

---

### 2. Reviewed Group Membership

Reviewed the user's security group memberships.

Removed the user from the **Finance-Users** security group where applicable.

---

### 3. Reviewed Enterprise Application Access

Reviewed enterprise application assignments.

**Observation**

The **IAM-Lab-App** enterprise application had:

- Assignment Required = **No**
- No users assigned

Because user assignment was not required, no application access removal was necessary.

---

### 4. Reviewed License Assignment

Checked Microsoft 365 licensing.

**Result**

No Microsoft 365 licenses were assigned to the user.

---

### 5. Attempted Session Revocation

Reviewed available authentication and session management options.

**Observation**

The Microsoft Entra ID Free developer tenant does not provide the **Revoke Sessions** feature available in some Microsoft Entra premium environments.

---

### 6. Documented Tenant Limitations

The following administrative actions were unavailable in the Microsoft Entra ID Free tenant:

- Block Sign-in
- Revoke Active Sessions

These limitations were documented as part of the offboarding process.

---

## Skills Demonstrated

- Identity Lifecycle Management
- User Offboarding
- Security Group Management
- Enterprise Application Review
- License Verification
- Access Review
- Microsoft Entra ID Administration
- IAM Documentation

---

## Screenshots

1. User Overview
2. Security Group Review
3. Enterprise Application Review
4. License Review
5. Authentication Methods Review
6. Final Access Verification

---

## Business Outcome

Successfully performed an employee access review and completed all available offboarding tasks supported by the Microsoft Entra ID Free developer tenant. Verified user access, reviewed enterprise applications, confirmed licensing status, and documented platform limitations following IAM best practices.

---

## Key Learning Points

- Review user access before deprovisioning.
- Remove unnecessary security group memberships.
- Verify enterprise application assignments.
- Review license assignments.
- Document tenant limitations when administrative features are unavailable.
- Maintain accurate IAM documentation for auditing purposes.

---

## Technologies Used

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Microsoft Entra ID Free Developer Tenant
- Identity and Access Management (IAM)

---

## Conclusion

This lab simulated an employee offboarding request in Microsoft Entra ID. Although certain administrative features such as **Block Sign-in** and **Revoke Sessions** were unavailable in the Microsoft Entra ID Free developer tenant, the access review process was completed by verifying group memberships, enterprise application assignments, and licensing status while documenting environmental limitations. This exercise reflects the importance of auditing and documenting access during the identity lifecycle.
