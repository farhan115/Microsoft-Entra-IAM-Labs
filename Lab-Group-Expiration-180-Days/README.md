# Microsoft Entra ID Lab — Microsoft 365 Group Expiration Policy

## Lab Overview

This lab demonstrates how to configure a Microsoft 365 group expiration policy using the Microsoft Entra admin center.

The objective is to automate group lifecycle management, notify group owners before expiration, and automatically delete groups that are not renewed.

This hands-on project demonstrates Identity and Access Management (IAM), identity governance, and Microsoft 365 group lifecycle management skills.

## Lab Environment

| Component | Description |
|---|---|
| Platform | Microsoft Entra ID |
| Tenant | Farhan Muntasir IT Labs |
| License | Microsoft Entra ID P2 Trial |
| Admin Center | Microsoft Entra Admin Center |
| Feature | Microsoft 365 Group Expiration |
| Group Lifetime | 180 Days |
| Policy Scope | All Microsoft 365 Groups |

## Lab Objectives

- Configure a 180-day Microsoft 365 group expiration policy.
- Enable expiration for all Microsoft 365 groups.
- Configure email notifications for groups without owners.
- Understand automated group renewal and deletion.
- Improve identity governance through group lifecycle management.

## Implementation Steps

### Step 1: Access Microsoft Entra Admin Center

1. Signed in to the Microsoft Entra admin center.
2. Navigated to **Identity → Groups → Expiration**.
3. Opened the Microsoft 365 group expiration configuration page.

### Step 2: Configure Group Lifetime

Configured the following setting:

**Group lifetime (in days): 180**

This establishes a 180-day expiration period for Microsoft 365 groups covered by the policy.

### Step 3: Configure Notifications

Entered an email contact for Microsoft 365 groups without assigned owners.

Microsoft Entra sends renewal notifications to group owners:

- 30 days before expiration
- 15 days before expiration
- 1 day before expiration

For ownerless groups, renewal notifications are directed to the configured email contact.

### Step 4: Enable Group Expiration

Under **Enable expiration for these Microsoft 365 groups**, selected:

**All**

This applies the expiration policy to all Microsoft 365 groups in the tenant, including eligible groups created in the future.

### Step 5: Save and Verify Configuration

Saved the policy and reviewed the resulting configuration.

| Policy Setting | Configured Value |
|---|---|
| Group Lifetime | 180 Days |
| Expiration Scope | All Microsoft 365 Groups |
| Ownerless Group Notification | Configured Email Contact |
| Renewal Notifications | 30, 15, and 1 Day Before Expiration |
| Unrenewed Groups | Automatically Deleted |

## Expected Behavior

When a Microsoft 365 group approaches expiration, Microsoft Entra sends renewal reminders to its owners.

If the group is renewed, its expiration period is extended.

If the group is not renewed before expiration, Microsoft Entra automatically deletes the group. Deleted Microsoft 365 groups can generally be restored during the applicable recovery period.

**Note:** Group owners require suitable Exchange email capabilities to receive notification messages.

## Lab Validation

The final configuration page displayed:

- Group lifetime set to 180 days.
- Expiration enabled for all Microsoft 365 groups.
- An accepted email address for ownerless group notifications.
- No pending configuration changes.

**Validation scope:** The configuration was verified in the admin portal. Actual email delivery, group renewal, and automatic deletion were not tested over a full expiration cycle.

## Skills Demonstrated

- Microsoft Entra ID Administration
- Identity and Access Management (IAM)
- Microsoft 365 Group Lifecycle Management
- Identity Governance
- Group Expiration Policies
- Automated Renewal Notifications
- Administrative Policy Configuration
- Access Governance

## Security and Governance Benefits

**Reduced stale access:** Helps remove Microsoft 365 groups that are no longer needed.

**Automated governance:** Reduces the need for manual group lifecycle tracking.

**Owner accountability:** Encourages group owners to review whether groups remain necessary.

**Reduced administrative overhead:** Automates reminders and expiration processes.

## Evidence

Add screenshots to an `images/` folder:

- `01-group-expiration-settings.png` — Expiration configuration page.
- `02-group-expiration-policy.png` — Saved 180-day expiration policy.

Avoid publishing usernames, email addresses, tenant identifiers, or other sensitive information in screenshots.

## Conclusion

Successfully configured and reviewed a Microsoft 365 group expiration policy in Microsoft Entra ID with a 180-day lifetime, automatic expiration for all Microsoft 365 groups, and renewal notifications for group owners and an ownerless-group contact.

This project demonstrates practical experience with Microsoft Entra ID governance and Microsoft 365 group lifecycle management.

**Technology:** Microsoft Entra ID P2 | Microsoft 365 Groups | Identity Governance

**Certification Alignment:** Microsoft SC-300 — Identity and Access Administrator
