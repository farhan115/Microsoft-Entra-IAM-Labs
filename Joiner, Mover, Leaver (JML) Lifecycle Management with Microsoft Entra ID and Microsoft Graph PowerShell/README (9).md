# Enterprise IAM Lab: Joiner–Mover–Leaver (JML) Lifecycle Management

**Platform:** Microsoft Entra ID | **Automation & verification:** Microsoft Graph PowerShell | **Scenario:** Contoso Technologies employee lifecycle | **Lab date:** November 1–2, 2026

### Identity lifecycle at a glance

| 1. JOINER | 2. MOVER | 3. LEAVER |
|---|---|---|
| Create Alex Johnson and assign initial group access | Update job information and adjust group membership | Disable account, revoke sessions, and remove group access |
| **Evidence:** Add user, Add member to group | **Evidence:** Update user, Remove/Add member to group | **Evidence:** Disable account, Remove member from group, token-validity updates |

The complete visual workflow diagram is included in the downloadable HTML guide and the `images/` directory.

## Executive summary

This hands-on identity and access management (IAM) lab demonstrates an employee's lifecycle from **joining** an organization, through a **department change**, to **departure**. It combines Microsoft Entra ID portal actions with Microsoft Graph PowerShell and directory audit evidence. The test identity is **Alex Johnson**, initially assigned to IT, then moved to Finance, and finally disabled with access removed. This is a **manual, simulated enterprise workflow**—not an HR-driven automatic provisioning integration.

**Observed outcome:** A Microsoft Graph query filtering on Alex Johnson's **object ID** returned **13 audit records**, all reporting `success`. The events include account creation, group additions and removals, user changes, account disablement and token-validity timestamp updates. A separate portal notification showed successful revocation of sign-in sessions. These records establish that the operations were recorded as successful, but do not prove that every existing application access token stopped working immediately.

## Learning objectives

- Distinguish **identity lifecycle management** from authentication and authorization.
- Create an Entra ID user and provision membership in security groups.
- Update identity attributes and migrate access when a user changes departments.
- Disable an account, remove group memberships and revoke sign-in sessions.
- Authenticate Microsoft Graph PowerShell and request least-privilege delegated permissions.
- Query and interpret directory audit events; diagnose missing permissions and filter mismatches.

## Environment and prerequisites

| Component | Purpose |
|---|---|
| Microsoft Entra ID tenant | Identity directory and administrative portal |
| Test user `Alex Johnson` | Simulated employee |
| Security groups `SG-JML-IT`, `SG-JML-Finance` | Lifecycle access assignments |
| Windows PowerShell + `Microsoft.Graph` module | Read-only audit and identity verification |
| Appropriate Entra administrative role | Required for user/group management and reading audit logs |
| `AuditLog.Read.All` Graph scope | Delegated API permission to read directory audit events, subject to consent and role requirements |

> **Important:** The screenshots show an **Entra Free** tenant. This lab uses assigned security groups and manual steps. Advanced automated governance workflows (for example, some Lifecycle Workflows or dynamic group features) may require additional licensing. Do not treat the group named `IAM-Dynamic-Group` as proof of dynamic membership; the portal screenshot listed its membership type as **Assigned**.

## Architecture and access model

1. **Identity source (simulated):** An administrator enters employment and department changes directly into Entra ID; no live HR system is connected.
2. **Directory:** Entra ID stores Alex's profile, account status and group memberships.
3. **Authorization:** Membership in `SG-JML-IT` or `SG-JML-Finance` models department-based access. Actual downstream application permissions were not demonstrated.
4. **Administration:** The Entra portal performs lifecycle operations; Microsoft Graph PowerShell independently reads the resulting state and audit trail.
5. **Evidence:** Directory audit logs capture operations, actors, timestamps, targets and outcomes.

## Phase 1 — Joiner: provision a new employee

**Scenario:** Alex Johnson joins Contoso Technologies. Create the user and add them to the initial IT group.

**Portal walkthrough:**

1. Open **https://entra.microsoft.com** → **Entra ID** → **Users** → **All users** → **New user** → **Create new user**.
2. Enter Alex Johnson's name, username, initial password and relevant profile attributes. Use the tenant's own domain rather than copying an example domain.
3. Open **Entra ID** → **Groups** → **All groups** → `SG-JML-IT` → **Members** → **Add members** and select Alex.
4. Reopen Alex's profile and verify the object ID, department, job title and group memberships.
5. Open **Audit logs** and find `Add user` and `Add member to group` events.

**Observed audit evidence:** `Add user` (1:51:38 AM) and `Add member to group` (2:09:39 AM), both successful on November 2, 2026 as displayed by the PowerShell session. An additional `Update PasswordProfile` event appears at creation.

## Phase 2 — Mover: change departments and access

**Scenario:** Alex moves from IT to Finance and is assigned the **Financial Analyst** job title.

**Portal walkthrough:**

1. Open **Entra ID** → **Users** → **All users** → **Alex Johnson** → **Edit properties**.
2. Under job information, set **Department: Finance** and **Job title: Financial Analyst**; save.
3. Open `SG-JML-IT` → **Members**, select Alex and choose **Remove**; verify the group has no direct members.
4. Open `SG-JML-Finance` → **Members** → **Add members**, choose Alex and confirm.
5. Verify Finance membership and inspect audit logs for `Update user`, `Remove member from group` and `Add member to group`.

**Observed evidence:** Earlier portal screenshots showed Alex's department as Finance, job title as Financial Analyst, and membership in the Finance group. Directory audit output included `Update user`, `Remove member from group` and `Add member to group` events. Audit records by themselves do not specify the source/destination group unless their detailed target/modified properties are inspected.

## Phase 3 — Leaver: disable and deprovision

**Scenario:** Alex leaves the organization. Prevent new sign-ins, invalidate refresh sessions and remove remaining modeled group access.

**Portal walkthrough:**

1. Open **Entra ID** → **Users** → **Alex Johnson** → **Overview**.
2. Change **Account status** to **Disabled** and save. Verify the status is disabled.
3. Select **Revoke sessions** and confirm the portal success notification.
4. Open `SG-JML-Finance` → **Members**, select Alex and choose **Remove**. Refresh to verify **0 direct members**.
5. Inspect Alex's **Audit logs** and the Finance group's **Audit logs** for the disable and removal events.

**Observed evidence:** `Disable account` (6:21:59 AM), `Update StsRefreshTokenValidFrom Timestamp` (6:24:09 AM and 6:24:43 AM), and `Remove member from group` (6:28:11 AM) were successful. A portal screenshot separately confirmed sign-in session revocation. **Caution:** Revoke sessions invalidates refresh tokens and related sessions; previously issued access tokens and application-specific sessions may remain valid temporarily. Full enterprise offboarding would also address app assignments, privileged roles, devices, licenses, external accounts, ownership and data retention.

## Microsoft Graph PowerShell — setup and authentication

Run the following in **PowerShell**, not Command Prompt (`cmd.exe`):

```powershell
# Check installed Graph modules
Get-InstalledModule Microsoft.Graph -ErrorAction SilentlyContinue

# If needed, install from the official PowerShell Gallery
Install-Module Microsoft.Graph -Scope CurrentUser

# If scripts are blocked, review policy first
Get-ExecutionPolicy -List
# Change only your user scope if permitted by your organization
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

Import-Module Microsoft.Graph.Authentication
Get-Command Connect-MgGraph

# Use the device-code flow if the regular sign-in dialog is problematic
Connect-MgGraph -Scopes "User.Read.All","AuditLog.Read.All" -UseDeviceCode
Get-MgContext | Format-List Account,TenantId,Scopes
```

Sign in with an authorized account from the **correct Entra tenant** at the device-login URL shown in the console. Consent and a suitable directory role may be required for audit reads. `User.ReadWrite.All`, `Group.ReadWrite.All` and `User.RevokeSessions.All` are **not necessary** for the read-only audit commands in this section; request write permissions only when performing authorized write operations.

### Verify the test user

```powershell
Get-MgUser -Top 10 | Select-Object DisplayName,UserPrincipalName

# Use the stable object ID rather than display name or a copied UPN
$AlexId = "c6b01a7c-4923-4f3d-9b2c-863d9d194155"
Get-MgUser -UserId $AlexId -Property DisplayName,Department,JobTitle,AccountEnabled |
  Select-Object DisplayName,Department,JobTitle,AccountEnabled
```

The lab previously returned **Alex Johnson / Finance / Financial Analyst / False** for `AccountEnabled`. This is a point-in-time observation, not a continuous compliance check.

### Verify memberships

```powershell
# Direct group memberships only; empty output is expected after full removal
Get-MgUserMemberOf -UserId $AlexId -All |
  Select-Object Id,AdditionalProperties
```

**Interpretation:** A blank result is consistent with zero direct memberships at that moment; it does not by itself establish that every downstream application entitlement or session has been removed. If the output includes other directory objects, inspect their types and group details.

### Query audit records by stable object ID

```powershell
Get-MgAuditLogDirectoryAudit -All |
  Where-Object { $_.TargetResources.Id -contains $AlexId } |
  Sort-Object ActivityDateTime -Descending |
  Select-Object ActivityDateTime,ActivityDisplayName,Result |
  Format-Table -AutoSize
```

**Why object ID matters:** A first attempt filtered on `TargetResources.DisplayName -contains "Alex Johnson"` and returned no rows. Filtering on the target **Id** instead returned 13 successful records. `-All` may be expensive in large tenants; prefer server-side date/time filtering when practical.

![Actual screenshot of successful audit records filtered by Alex Johnson object ID](images/graph-audit-object-id.png)

*Evidence A — Object-ID filter: user creation, group changes, account disablement and token-validity updates appear with `success`.*

![Actual screenshot of audit events sorted by time](images/graph-audit-sorted.png)

*Evidence B — Chronological verification: 13 successful target-related directory audit events.*

## Audit evidence summary

| Recorded activity | Count | What it supports |
|---|---:|---|
| Add user | 1 | Joiner identity creation |
| Update PasswordProfile | 1 | Initial credential/profile handling |
| Add member to group | 2 | Joiner / Mover access assignments |
| Remove member from group | 2 | Mover / Leaver access removal |
| Update user | 4 | Profile and status changes |
| Disable account | 1 | Leaver account restriction |
| Update StsRefreshTokenValidFrom Timestamp | 2 | Token-validity timestamp change |
| **Total** | **13** | **All 13 target-related audit events reported success** |

**Evidence-count note:** The object-ID-filtered screenshot shows **13 records**: 1 add user, 1 password-profile update, 2 group additions, 2 group removals, 4 user updates, 1 account disable and 2 refresh-token-validity timestamp updates. The general tenant-wide audit list contains unrelated events and is not part of this count.

## Troubleshooting diary

| Symptom | Likely cause | Resolution / validation |
|---|---|---|
| `Get-ExecutionPolicy` not recognized | Command entered in **Command Prompt** | Run `powershell` first, then retry at the `PS>` prompt |
| `Connect-MgGraph` found but module cannot load | PowerShell script execution policy blocked `.psm1` | Inspect `Get-ExecutionPolicy -List`; if permitted set `RemoteSigned` for `CurrentUser`; re-import module |
| `Do you want to run software from this untrusted publisher?` | Publisher trust prompt | Verify Microsoft module origin and signature before choosing **Run once**; avoid blindly trusting all publishers |
| Interactive sign-in hangs or is unresponsive | Authentication window/WAM issue | Use `Connect-MgGraph -UseDeviceCode` and sign in through the displayed official device URL |
| Device login completes but commands fail | Wrong account, tenant or scopes | Inspect `Get-MgContext | Format-List Account,TenantId,Scopes` |
| `403 Authentication_MSGraphPermissionMissing` | `AuditLog.Read.All` missing or not consented | Reconnect requesting `AuditLog.Read.All`; confirm consent and directory role |
| Display-name audit filter returns blank | Nested target properties not matching the exact name | Use the immutable Entra **object ID** in `TargetResources.Id` |
| `Get-MgUserMemberOf` returns nothing | No current direct membership, or incorrect user/tenant | Check tenant and object ID; compare Entra portal group lists |
| Account disabled but sessions may persist | Access tokens/app sessions are not necessarily instantly revoked | Revoke sessions, validate app-specific behavior and check Conditional Access / downstream systems |

### Troubleshooting screenshots

![Graph PowerShell connection and permission troubleshooting screenshot](images/graph-permission.png)

*Evidence C — Authentication/permission troubleshooting captured during the lab.*

![Graph connection context screenshot](images/graph-connection.png)

*Evidence D — Microsoft Graph connection troubleshooting and context.*

![Display-name filter returned no results](images/display-name-filter-no-results.png)

*Evidence E — Initial display-name filter produced no rows; the object-ID filter resolved the lookup.*

## Security and production considerations

- **Least privilege:** Use a dedicated administrative identity, PIM/JIT access when available, and only the Graph scopes needed for each operation.
- **Separation of duties:** HR approves employment changes; IAM executes provisioning; application owners validate entitlements.
- **Approvals and evidence:** Keep change tickets, identity object IDs, timestamps, initiators, audit record IDs and review outcomes.
- **Lifecycle completeness:** Remove or transfer direct app assignments, privileged roles, service ownership, group ownership, devices, licenses and non-Entra accounts as required by policy.
- **Session risk:** Disabling an account and revoking sessions is not proof that every already-issued access token or SaaS session has been terminated.
- **Privacy:** Before publishing, redact real tenant identifiers, email addresses, object IDs, device codes and any sensitive organizational information. This example is a lab; do not publish credentials or tokens.
- **Automation gap:** The exercise is manual. An enterprise implementation should add an authoritative HR trigger, idempotent workflows, approvals, retry handling, monitoring and reconciliation.

## Acceptance criteria and evidence status

- [x] Test identity created — audit `Add user`
- [x] Group membership granted — audit `Add member to group`
- [x] Department/job title updated — portal profile and `Update user` audit
- [x] IT membership removed — portal evidence and group-removal audit
- [x] Finance membership assigned — portal evidence and group-add audit
- [x] Account disabled — portal status, Graph user query and `Disable account` audit
- [x] Finance membership removed — portal shows zero direct members and audit success
- [x] Sign-in sessions revoked — portal confirmation and token timestamp events
- [x] Audit events retrieved through Microsoft Graph PowerShell using object ID
- [ ] Prove application-level access is fully removed — **out of scope / not demonstrated**
- [ ] Integrate with an HR source or lifecycle automation engine — **future enhancement**

## Interview explanation

> I built a simulated employee identity lifecycle in Microsoft Entra ID. I created a test user, assigned initial IT access through a security group, updated the user's department and job title for a move to Finance, changed group membership, then disabled the account, revoked sessions and removed remaining group access. I validated the actions using Entra audit logs and Microsoft Graph PowerShell. I also troubleshot PowerShell execution policy, interactive authentication, Graph API consent and audit-log filtering, eventually using the immutable object ID to retrieve the relevant events.

## Suggested next labs

1. **Automated JML with Microsoft Graph:** Approved scripts with input validation, dry-run mode, idempotence and logging.
2. **HR-driven lifecycle:** Ingest mock HR events and map them to approved access rules.
3. **Access governance:** Periodic group access review, ownership and attestation.
4. **Application provisioning:** Connect Entra to a test SCIM 2.0 app and compare directory group removal to downstream deprovisioning.

## Project structure

```text
Enterprise_IAM_JML_GitHub/
├── README.md
└── images/
    ├── jml-lifecycle.svg
    ├── graph-audit-object-id.png
    ├── graph-audit-sorted.png
    ├── graph-audit-general.png
    ├── graph-permission.png
    ├── graph-connection.png
    └── display-name-filter-no-results.png
```

**Scope statement:** The screenshots demonstrate an educational tenant and specific point-in-time operations. The audit result count, role assumptions and session behavior should not be generalized to production without independent validation.
