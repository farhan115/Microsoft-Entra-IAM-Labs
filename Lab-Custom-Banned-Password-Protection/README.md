# Microsoft Entra ID Lab — Custom Banned Password Protection

## Lab Overview

This hands-on IAM lab demonstrates how to configure Microsoft Entra Password Protection using a custom banned password list.

The objective is to prevent users from creating or changing passwords that contain the word **Falcon** or variations detected by Microsoft Entra Password Protection.

This lab focuses on password security, identity protection, authentication security, and centralized password policy management.

## Lab Environment

| Component | Configuration |
|---|---|
| Platform | Microsoft Entra ID |
| Tenant | Farhan Muntasir IT Labs |
| License | Microsoft Entra ID P2 Trial |
| Admin Center | Microsoft Entra Admin Center |
| Feature | Password Protection |
| Custom Banned Word | Falcon |
| Custom List Enforcement | Enabled |
| Smart Lockout Threshold | 10 |
| Smart Lockout Duration | 60 Seconds |
| Windows Server AD Protection | Enabled |
| Windows Server AD Mode | Audit |

## Lab Objectives

- Configure Microsoft Entra Password Protection.
- Create and enforce a custom banned password list.
- Prevent weak passwords based on prohibited terms.
- Understand password normalization and common character substitutions.
- Review Microsoft Entra Smart Lockout settings.
- Understand the difference between Audit and Enforced modes for on-premises Active Directory.

## Implementation Steps

### Step 1: Access Microsoft Entra Admin Center

1. Signed in to the Microsoft Entra admin center.
2. Navigated to **Entra ID → Authentication methods → Password protection**.
3. Opened the Password Protection configuration page.

### Step 2: Review Smart Lockout Settings

Verified the following settings:

| Setting | Value |
|---|---|
| Lockout Threshold | 10 |
| Lockout Duration | 60 seconds |

Smart Lockout helps protect user accounts from repeated unsuccessful authentication attempts.

### Step 3: Configure Custom Banned Passwords

Under **Custom banned passwords**, configured:

**Enforce custom list:** Yes

**Custom banned password list:**

`Falcon`

Microsoft Entra Password Protection uses password normalization, common substitutions, and fuzzy matching to identify passwords related to banned terms.

Examples of variations that may be detected include:

- Falcon
- FALCON
- Falc0n
- Falcon123

Actual rejection depends on Microsoft's password evaluation algorithm and the complete password being tested.

### Step 4: Review Windows Server Active Directory Protection

Verified the existing settings:

| Setting | Value |
|---|---|
| Enable password protection on Windows Server Active Directory | Yes |
| Mode | Audit |

Audit mode allows organizations to evaluate password protection behavior in supported on-premises Active Directory environments without rejecting passwords solely because of the on-premises password protection policy.

**Note:** Effective on-premises protection requires the appropriate Microsoft Entra Password Protection agents and infrastructure. These were not deployed as part of this lab.

### Step 5: Save and Verify Configuration

Reviewed the saved configuration in Microsoft Entra.

The final configuration displayed:

- Custom banned password enforcement enabled.
- Falcon added to the custom banned password list.
- Smart Lockout threshold set to 10.
- Smart Lockout duration set to 60 seconds.
- Windows Server Active Directory protection configured in Audit mode.

## Lab Validation

**Configuration status:** Completed.

The Microsoft Entra admin center displayed the expected policy settings with no pending changes.

**Functional testing:** Not performed.

A future validation test can use a disposable test account to attempt a password change containing the banned term and observe whether Microsoft Entra rejects the password.

## Security Benefits

### Stronger Password Security

Helps prevent users from choosing passwords based on prohibited organizational terms.

### Reduced Password Guessing Risk

Discourages predictable password patterns that attackers may attempt during password guessing attacks.

### Centralized Policy Management

Allows administrators to manage banned password terms from the Microsoft Entra admin center.

### Hybrid Identity Security

Supports extending password protection to on-premises Active Directory when the required agents are deployed and configured.

## Skills Demonstrated

- Microsoft Entra ID Administration
- Identity and Access Management (IAM)
- Microsoft Entra Password Protection
- Custom Banned Password Policies
- Authentication Security
- Microsoft Entra Smart Lockout
- Hybrid Identity Security Concepts
- Security Policy Configuration
- Identity Governance Fundamentals

## Screenshots and Evidence

Store screenshots in the `images/` directory.

| Screenshot | Description |
|---|---|
| `01-password-protection-before.png` | Initial password protection configuration |
| `02-custom-banned-password-policy.png` | Falcon custom banned password and enforcement enabled |
| `03-password-protection-final.png` | Final saved configuration |

Avoid including passwords, authentication tokens, or sensitive tenant information in screenshots.

## Key Takeaways

- Microsoft Entra Password Protection supports custom banned password lists.
- Custom banned terms help reduce predictable password choices.
- Password protection applies when supported password changes or resets are evaluated.
- Smart Lockout helps protect accounts against repeated unsuccessful sign-in attempts.
- Audit mode and Enforced mode serve different purposes in on-premises Active Directory deployments.
- Microsoft Entra ID P2 includes the P1 password protection capabilities used in this lab.

## Conclusion

Successfully configured a custom banned password policy in Microsoft Entra ID using the term Falcon.

Enabled custom list enforcement, reviewed Smart Lockout settings, and examined Windows Server Active Directory password protection configuration.

This lab demonstrates practical knowledge of identity security controls and centralized password protection policy management.

**Technology:** Microsoft Entra ID P2 | Password Protection | Smart Lockout | IAM

**Certification Alignment:** Microsoft SC-300 — Identity and Access Administrator
