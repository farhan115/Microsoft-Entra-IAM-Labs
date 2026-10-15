# Day 31 — Microsoft Entra ID RBAC Permission Validation

## Objective
Implement and validate Role-Based Access Control (RBAC) in Microsoft Entra ID using the Helpdesk Administrator role. Demonstrate least privilege through successful and denied administrative operations and verify activity through audit logs.

## Lab Environment
- Platform: Microsoft Entra ID
- Tools: Microsoft Entra Admin Center
- Administrator: Tenant administrator account
- Test administrator: helpdesk1 (Helpdesk Administrator)
- Test users: Alex Brown, John Smith, Emma Wilson

## Scenario
An organization needs an IT Helpdesk Technician to reset passwords for eligible employees without granting broad tenant administration privileges.

## Implementation

### 1. Configure RBAC
- Created or verified the Helpdesk Technician test account.
- Assigned the built-in Helpdesk Administrator role to helpdesk1.
- Verified that the assignment was direct and scoped to the directory.

### 2. Validate Helpdesk Access
- Signed in as helpdesk1 using a separate browser session.
- Confirmed access to the directory user list.

### 3. Test Privileged Account Restrictions
- Attempted to reset Alex Brown's password.
- The request was denied due to insufficient administrative privileges.
- Verified that Alex Brown had the User Administrator role.

### 4. Test Regular User Password Reset
- Verified that Emma Wilson had no assigned directory roles.
- Reset Emma Wilson's password successfully using helpdesk1.
- Microsoft Entra ID generated a temporary password requiring a change at next sign-in.

### 5. Test Audit Log Access
- Attempted to access audit logs using helpdesk1.
- Received a 401 access-denied response.
- Signed in using a privileged administrator account to inspect the audit logs.

### 6. Verify Audit Evidence
- Located the successful Reset password (by admin) event.
- Target: Emma Wilson
- Initiated by: helpdesk1
- Result: Success
- Date: October 15, 2026

## Test Results

| Test | Result |
|---|---|
| Helpdesk Administrator role assignment | Passed |
| Helpdesk account sign-in | Passed |
| View directory users | Passed |
| Reset User Administrator password | Denied as expected |
| Reset regular user password | Passed |
| Access audit logs as Helpdesk Administrator | Denied |
| Verify password reset in audit logs | Passed |

## Security Concepts Demonstrated
- Role-Based Access Control (RBAC)
- Principle of Least Privilege (PoLP)
- Administrative role hierarchy
- Privileged account protection
- Access control testing
- Identity audit logging
- IAM troubleshooting

## Screenshots
Add sanitized evidence screenshots to a screenshots folder:
1. Helpdesk Administrator role assignment
2. Helpdesk account signed in
3. Privileged user password reset denied
4. Regular user role verification
5. Successful password reset (temporary password hidden)
6. Helpdesk audit log access denied
7. Administrator audit log showing successful reset

## Key Takeaways
This lab demonstrated how Microsoft Entra ID enforces delegated administrative permissions. A Helpdesk Administrator could reset an eligible regular user's password but was restricted from resetting a more privileged account and accessing directory audit logs. An authorized administrator verified the completed password reset through audit records.

## Security Note
All testing was conducted using lab identities. Passwords, tokens, and other sensitive authentication data should never be included in screenshots or repository documentation.
