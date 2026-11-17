# Microsoft Entra ID — FIDO2 Synced Passkey Authentication Lab

**Author:** Farhan Muntasir  
**GitHub:** [farhan115](https://github.com/farhan115)  
**Lab date:** November 17, 2026  
**Suggested repository or folder:** `entra-id-fido2-passkey-lab`

## Overview

Configured Microsoft Entra ID passkey authentication for a dedicated test group, registered a synced passkey in Google Password Manager for a standard test user, and verified a successful passkey authentication step in Entra sign-in logs.

This report documents the FIDO2/passkey portion of a broader IAM project. Certificate-based authentication (CBA), certificate authority trust, and physical PIV authentication were not implemented in this portion.

## Verified outcomes

- 1 dedicated policy target group: `IAM-Passkey-Lab`.
- 1 test user: Bianca Pisani.
- 1 synced passkey registered through Google Password Manager.
- 1 explicit passkey authentication step verified with `Succeeded: Yes` and `User approved`.
- Standard-user access restrictions observed when attempting to read administrative sign-in logs.

**Evidence limitation:** The event containing the successful passkey step had an overall status of **Interrupted** in the event list. The screenshots prove successful credential verification within that event, not successful completion of every application sign-in step. Other successful application events showed **Previously satisfied**, which does not independently identify a fresh passkey operation.

## Environment

| Component | Lab configuration |
|---|---|
| Identity provider | Microsoft Entra ID |
| Tenant | Farhan Muntasir IT Labs |
| Tenant plan shown | Entra ID Premium P2 |
| Administrator | Farhan Muntasir |
| Test identity | Bianca Pisani, no administrative roles shown |
| Policy target | `IAM-Passkey-Lab` |
| Credential provider | Google Password Manager |
| Registered method | Passkey (Synced) |
| Browser | Google Chrome |

## Objectives

1. Scope passkey availability to a test group.
2. Explain passkey profile settings and their effect on registration.
3. Register a credential through the user's Security info page.
4. Test the credential using the provider's PIN unlock prompt.
5. Distinguish authentication success, session reuse, and overall event status.
6. Review tenant sign-in evidence using the administrator account.

## Implementation and why each step matters

### 1. Create a dedicated security group

Navigate to **Entra ID → Groups → New group**.

| Field | Intended lab setting | Why |
|---|---|---|
| Group type | Security | Provides a policy target for test users |
| Name | `IAM-Passkey-Lab` | Clearly identifies its purpose |
| Description | Test users for Microsoft Entra FIDO2/passkey authentication | Documents the group's use |
| Microsoft Entra roles can be assigned | No | Authentication method targeting does not require a role-assignable group |
| Membership type | Assigned | Allows deliberate selection of test accounts |
| Members | Bianca Pisani | Places the test identity in policy scope |

The initial group creation screen showed the role-assignable option enabled. The lab guidance was to turn it off before creating the group. A final group-properties screenshot was not supplied, so this report does not assert that property was independently verified.

**Why:** A pilot group provides controlled deployment. Group ownership and group membership have different purposes: ownership enables group management; membership is used for targeting this lab policy.

### 2. Enable the passkey method for the group

Navigate to **Authentication methods → Policies → Passkey (FIDO2)**.

Set **Enable** to **On**, remove the broad **All users** target, and include **IAM-Passkey-Lab** with the **Default passkey profile**. Save the policy.

**Observed:** Later screenshots showed the method enabled, the dedicated group targeted, and no pending edits.

**Why:** This permits eligible users to register and use allowed passkeys. It does not itself create a credential or require passkey use for every sign-in.

### 3. Inspect the default passkey profile

Open **Configure → Default passkey profile**.

| Setting | Observed value | Meaning |
|---|---|---|
| Allow self-service set-up | Checked | Eligible users can register their credentials |
| Enforce attestation | Unchecked | Authenticator attestation is not required by this profile |
| Passkey types | Device-bound, Synced | Both listed storage types are permitted |
| Target specific AAGUIDs | Unchecked | No model-specific restrictions configured here |

An **AAGUID** identifies an authenticator model or type, rather than an individual user. Device-bound credentials stay with one authenticator; synced credentials are managed through a provider that supports synchronization.

The profile editor displayed a compatibility warning that some setting combinations can prevent registration or sign-in. That warning alone was not evidence of a failed configuration. Registration subsequently succeeded.

### 4. Sign in with the test user's full username

Open [Security info](https://mysignins.microsoft.com/security-info) as the test user, using a separate browser session from the administrator.

An initial attempt used the tenant name by itself and failed with an account-not-found message. The correction was to use the user's full **User principal name (UPN)** from **Entra ID → Users → user profile**.

**Why:** A tenant display name is not a user sign-in identifier. The UPN identifies the account and its domain.

### 5. Register the synced passkey

In Security info:

1. Select **Add sign-in method**.
2. Select **Passkey**.
3. Continue through the passkey introduction.
4. Complete the Google Password Manager credential creation prompts.
5. Confirm the new method appears in Security info.

**Observed registration result:**

```text
Passkey (Synced)
Google Password Manager
```

The account also had a password and an authenticator app TOTP method. The TOTP method is separate from the newly registered passkey.

**Why:** Enabling the policy only establishes eligibility. User registration creates the credential associated with that identity.

### 6. Test passkey authentication

Use the Microsoft sign-in page and select the passkey sign-in option. Complete the Google Password Manager PIN prompt and confirm the resulting Microsoft session belongs to the intended test user.

**Observed:** A Google Password Manager prompt asked for the existing PIN for `login.microsoft.com`; a subsequent screenshot showed Bianca signed into Entra.

The Google account shown in the password manager prompt identifies the credential vault account. It need not have the same display name as the Microsoft test identity. The selected Microsoft credential and resulting session must belong to the intended test user.

**Why:** The provider PIN unlocks use of the stored credential. It is different from the Microsoft account password. There was no need to reset the PIN or register another passkey.

### 7. Review authentication evidence as administrator

In the separate administrator session, open **Entra ID → Monitoring & health → Sign-in logs**. Filter for the test user and inspect **Authentication details** on relevant events.

**Verified authentication record:**

| Field | Screenshot evidence |
|---|---|
| Date | November 17, 2026 |
| Authentication time | `2026-11-17T16:09:05Z` |
| Chicago local time | 10:09:05 AM CST |
| Authentication method | `Passkey (synced)` |
| Succeeded | `Yes` |
| Result detail | `User approved` |
| Requirement | Displayed with truncated text beginning `Phishing-resist...` |
| Overall event status from list | `Interrupted` |

**Conclusion:** The passkey authentication step succeeded. The reason the overall event was interrupted is not established by the supplied screenshots.

The explicit passkey record predates the later PIN-prompt screenshot; it is evidence of a successful earlier passkey operation, rather than a proven timestamp match to that later screenshot.

## Understanding the troubleshooting results

| Observation | Interpretation | Resolution or next action |
|---|---|---|
| Tenant name rejected at sign-in | Account identifier was incomplete | Use the test user's full UPN |
| Test user has no admin roles | Standard test identity | Keep administrator and user sessions separate |
| Unauthorized high-risk-user card | User lacks access to that administrative report | Does not imply authentication failure |
| Insufficient privileges on tenant sign-in logs | Test user cannot access tenant-wide reporting | Review logs with the administrator |
| Authentication method says Previously satisfied | Event accepted earlier authentication evidence | Find an event with an explicit method; do not label it a fresh passkey test |
| Successful event lacks explicit passkey details | Overall success alone does not prove the credential used | Inspect Authentication details |
| Event list appears behind recent activity | Desired record is not yet visible in the supplied list | Refresh and correlate user, application, and timestamps; avoid assuming exact screenshot-to-event timing |
| Passkey step succeeds but event is Interrupted | Credential verification and end-to-end event completion are separate | Record both facts and inspect Basic info for the interruption reason if needed |
| Existing provider PIN requested | Credential provider requires unlock | Use the existing PIN privately |

## Validation checklist

- [x] Passkey method enabled for the dedicated test group.
- [x] Default profile settings inspected.
- [x] Synced passkeys allowed by the profile.
- [x] Test user's synced passkey visible in Security info.
- [x] Provider PIN prompt observed.
- [x] Test-user session observed after the prompt.
- [x] Explicit `Passkey (synced)` step with `Succeeded: Yes` verified.
- [x] Overall Interrupted status documented separately.
- [ ] Optional: capture one overall Success event that also explicitly records a fresh passkey step.
- [ ] Optional: verify final group role-assignable property in group settings.

## Interview questions and answers

**1. What did you implement?**  
I configured a group-scoped Entra passkey policy, registered one synced passkey for a test user, and verified successful passkey credential authentication in sign-in logs.

**2. Why use a dedicated group?**  
It limits the rollout to selected users and makes the policy scope easier to review.

**3. Does enabling the method force everyone to use it?**  
No. Availability, registration, and enforcement are distinct. This lab demonstrated availability and registration, not a new Conditional Access enforcement policy.

**4. How are synced and device-bound passkeys different?**  
Synced credentials can be available through the provider's synchronization system. A device-bound credential stays with its authenticator.

**5. What is attestation?**  
It provides evidence about the authenticator. The lab profile did not require it.

**6. What does an AAGUID identify?**  
An authenticator model or type. It can support model-based credential restrictions.

**7. Is the password manager PIN the Entra password?**  
No. It unlocks use of the provider's credentials; the Microsoft account password is a separate credential.

**8. Why can a Google vault hold a passkey for a Microsoft account?**  
The credential provider stores and manages credentials for relying services. The vault account and the service account are separate identities.

**9. What does Previously satisfied mean for this investigation?**  
The event reused prior authentication evidence. That row alone does not identify a fresh passkey operation.

**10. What proved the authentication method?**  
The explicit `Passkey (synced)` row, with `Succeeded: Yes` and `User approved`.

**11. Can an authentication step succeed in an Interrupted event?**  
Yes. The screenshots demonstrate successful passkey verification within an event whose overall completion was interrupted. Both statuses must be reported.

**12. Did you implement PIV or certificate authentication?**  
No. This portion implemented FIDO2/passkeys. CBA requires certificate issuance, trust configuration, and user mapping; physical PIV testing additionally requires suitable card or token hardware.

## Resume bullet

- Configured 1 group-scoped Microsoft Entra ID passkey policy and registered 1 synced FIDO2 credential; verified successful passkey authentication in sign-in logs and investigated session reuse and standard-user reporting restrictions.

## Suggested GitHub description

Microsoft Entra ID FIDO2 lab with group-scoped passkey policy, Google Password Manager registration, verified authentication evidence, troubleshooting, and interview Q&A.

## Screenshots for the repository

This README is self-contained and does not require image files. If adding screenshots, prioritize the policy target, profile settings, registered passkey, and explicit successful authentication-step record. Mask personal contact details and unnecessary account or tenant identifiers before publishing. Never publish PINs, passwords, private keys, or recovery codes.

## Next project phase

Implement Entra certificate-based authentication with a lab CA, user certificate, configured trust, identity mapping, and sign-in verification. Document physical PIV separately if compatible hardware is available. These are future tasks and are not claimed as completed here.
