# Microsoft Entra ID Offboarding and Access Verification

**Author:** Farhan Muntasir  
**Environment:** Microsoft Entra ID lab tenant  
**Lab date:** November 18, 2026

## Objective

Simulate an employee departure and verify two outcomes for a dedicated test account. A fresh sign-in is blocked, and the account is removed from its assigned security group. Use sign-in and audit logs to document what happened.

## Lab setup

| Component | Configuration |
|---|---|
| Test identity | `lab-offboard01` |
| Security group | `SG-Lab-Offboarding-Access` with Assigned membership |
| Sign-in application | Microsoft My Apps |
| Evidence | Interactive sign-in logs, directory audit logs, and group membership count |

## Procedure

1. Add the test user to the security group and confirm **1 direct member**.
2. Open My Apps in a private browser window as the test user. Complete Microsoft Authenticator registration when prompted and verify that the dashboard loads.
3. In the Entra admin center, disable the test account, revoke its sessions, and remove it from the security group.
4. Close all private windows. Open a fresh private window and attempt to sign in to My Apps again.
5. Open the failed interactive sign-in and inspect **Authentication details** for the cause. In **Audit logs**, review the administrative events. Match the group object ID in the removal event to the ID on the group overview and verify the updated member count.

## Validation results

| Check | Before offboarding | After offboarding |
|---|---|---|
| My Apps sign-in | Successful sign-in and visible dashboard | Fresh sign-in failed. Authentication details identified a disabled account. |
| Security group membership | 1 direct member, the test user | 0 direct members. The removal event named the test user, and its group object ID matched the group overview. |

### Timeline

Times are Houston local time (CST) on November 18, 2026.

| Time | Source | Event |
|---|---|---|
| 10:24:59 and 10:25:00 a.m. | Sign-in logs | Successful My Apps sign-ins for the test account. |
| 10:30:42 a.m. | Audit logs | **Disable account** succeeded. |
| 10:31:56 a.m. | Audit logs | **Update StsRefreshTokenValidFrom Timestamp** succeeded. This records the refresh-token validity update associated with session revocation. |
| 10:47:16 a.m. | Audit logs | **Remove member from group** succeeded. Event details showed the test account and matching group object ID. |
| 10:51:20 a.m. | Sign-in logs | Fresh My Apps sign-in failed. Authentication details identified a disabled account. |

The fresh sign-in test verified the account block. Ending a previously active application session was outside the scope of the test. The recorded group removal verifies directory membership. No application entitlement was tested through the group.

## Troubleshooting and interview questions

**Why check sign-in logs if the browser already reported a locked account?**  
The browser showed a generic lock message. The authentication details identified the disabled account as the reason for the failed sign-in.

**How did you verify the correct group was changed when the audit event displayed no group name?**  
I matched the group's object ID in the audit event to the group overview, confirmed the event targeted the test user, and checked that direct membership dropped from one to zero.

**Why use a fresh private window after disabling the account?**  
It tests a new authentication attempt without reusing the browser session that established the baseline.

## Resume bullet

- Validated employee offboarding in a Microsoft Entra ID lab by correlating successful and blocked My Apps sign-ins with directory audit logs and verifying security group membership dropped from 1 to 0.

## Publishing evidence

Before adding screenshots to a public repository, redact full user principal names, tenant details, IP addresses, request IDs, and object IDs. The event labels, timestamps, and member counts can remain visible.
