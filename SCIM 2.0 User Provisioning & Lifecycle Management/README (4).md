# Enterprise IAM Lab — SCIM 2.0 Provisioning, Authentication & Lifecycle Operations

**Farhan Muntasir** · Python 3.13 · Flask · SQLite · PowerShell · SCIM 2.0 · REST API

![Status](https://img.shields.io/badge/Local%20Lab-Verified-198754) ![API](https://img.shields.io/badge/SCIM-2.0-2563eb) ![Backend](https://img.shields.io/badge/Backend-Flask-64748b)

> **Project outcome:** Built a locally running SCIM-style user provisioning service, verified bearer-token access control, created and retrieved identities, implemented a PATCH surname update, and independently confirmed the resulting database change. This is a **local API simulation**, not yet a Microsoft Entra ID-connected or production-ready SCIM service.

## Contents

1. [What problem this solves](#1-what-problem-this-solves)
2. [Architecture and request flow](#2-architecture-and-request-flow)
3. [Project files and environment](#3-project-files-and-environment)
4. [Authentication and server process isolation](#4-authentication-and-server-process-isolation)
5. [SCIM resources and endpoints](#5-scim-resources-and-endpoints)
6. [Create and retrieve a user](#6-create-and-retrieve-a-user)
7. [PATCH a user's surname](#7-patch-a-users-surname)
8. [Verify SQLite persistence](#8-verify-sqlite-persistence)
9. [Troubleshooting: symptoms, diagnosis, fixes](#9-troubleshooting-symptoms-diagnosis-fixes)
10. [Results and limitations](#10-results-and-limitations)
11. [Security and next steps](#11-security-and-next-steps)
12. [Complete screenshot evidence gallery](#12-complete-screenshot-evidence-gallery)

---

## 1. What problem this solves

When an employee joins, changes their name, or leaves, their accounts must be synchronized across applications. **SCIM (System for Cross-domain Identity Management)** standardizes how a provisioning client creates, reads, updates, and removes identity resources using HTTP and JSON.

**Example:** Jane Smith's surname changes to Johnson. Instead of deleting her account, a provisioning client sends a PATCH request for `name.familyName`. The target application validates the request and saves the new value.

**What we built:** PowerShell/curl as the test client → Flask SCIM API → SQLite database. **What we did not build:** an actual Microsoft Entra provisioning connection. Entra is a future integration target.

## 2. Architecture and request flow

![SCIM architecture](screenshots/architecture.png)

```text
PowerShell / curl.exe (test provisioning client)
        │ HTTP + Authorization: Bearer <token>
        ▼
Flask app.py (127.0.0.1:5004)
        ├─ Validate bearer token → 401 if missing/wrong
        ├─ Validate SCIM JSON / PATCH path → 400 if unsupported
        ├─ Route GET, POST, PATCH, DELETE
        ▼
SQLite scim_users.db
        └─ users(id, userName, active, givenName, familyName, email)
```

**Lifecycle of a PATCH request:** (1) client sends JSON → (2) Flask authenticates → (3) handler validates SCIM PatchOp and the path → (4) handler finds the UUID → (5) parameterized SQL UPDATE → (6) `commit()` → (7) Flask returns updated user → (8) separate SQLite query confirms persistence.

## 3. Project files and environment

| File | Role |
|---|---|
| `app.py` | Flask routes, validation, auth, SQLite operations |
| `employee.json` | SCIM User payload used for POST |
| `update_employee.json` | SCIM PatchOp payload for surname change |
| `scim_users.db` | Local test database (do not publish actual data) |
| `venv/` | Local Python virtual environment (do not commit) |
| `screenshots/` | Chronological troubleshooting and test evidence |

**Windows PowerShell setup** (from the project directory):

```powershell
cd C:\Users\FOXTEC\entra-scim-lab
py -m venv venv
.\venv\Scripts\python.exe -m pip install flask
.\venv\Scripts\python.exe -m py_compile app.py
```

If the project already has a working virtual environment, skip recreating it. Start the Flask process **after** configuring the token in that same shell:

```powershell
$env:SCIM_BEARER_TOKEN = 'replace-with-your-own-local-secret'
.\venv\Scripts\python.exe -m flask --app app run --host 127.0.0.1 --port 5004 --no-reload --no-debugger
```

Keep this server terminal open. In a **second** PowerShell window, set the **same** secret before testing. Do not reuse the example secret for any public deployment.

![Flask routes listing, confirming registered SCIM endpoints](screenshots/lab-20261101-204937.png)

*Evidence: Flask routes listing, confirming registered SCIM endpoints. Screenshot captured November 1, 2026.*
## 4. Authentication and server process isolation

The protected API expects `Authorization: Bearer <token>`. The test client and Flask process must agree on the token; setting an environment variable in one terminal **does not change another running process**.

```powershell
# In the second PowerShell window, use the same secret as the server:
$env:SCIM_BEARER_TOKEN = 'replace-with-your-own-local-secret'
$env:SCIM_BEARER_TOKEN.Length  # check presence, not secret contents
curl.exe -i -H "Authorization: Bearer $env:SCIM_BEARER_TOKEN" http://127.0.0.1:5004/scim/v2/Users
```

**Expected:** `HTTP/1.1 200 OK`. Invalid token test:

```powershell
curl.exe -i -H 'Authorization: Bearer invalid-test-token' http://127.0.0.1:5004/scim/v2/Users
```

**Expected:** `HTTP/1.1 401 UNAUTHORIZED` and `WWW-Authenticate: Bearer`. These two tests prove the local authentication gate accepts a valid credential and rejects an invalid one.

![Successful GET with bearer authentication, returning two users](screenshots/lab-20261101-210207.png)

*Evidence: Successful GET with bearer authentication, returning two users. Screenshot captured November 1, 2026.*
![Earlier 401 Unauthorized response during mismatched-token troubleshooting](screenshots/lab-20261101-205719.png)

*Evidence: Earlier 401 Unauthorized response during mismatched-token troubleshooting. Screenshot captured November 1, 2026.*
## 5. SCIM resources and endpoints

| HTTP method | Route | Meaning | Observed outcome |
|---|---|---|---|
| GET | `/` | Local application status | Running status JSON |
| GET | `/scim/v2/Users` | List users | 200 OK |
| POST | `/scim/v2/Users` | Create user | 201 Created |
| GET | `/scim/v2/Users/{id}` | Read one user | 200 OK |
| PATCH | `/scim/v2/Users/{id}` | Modify selected attributes | 200 OK |
| DELETE | `/scim/v2/Users/{id}` | Delete a user | Route present; final success not verified |
| GET | `/scim/v2/ServiceProviderConfig` | Report capabilities | Route present |

A SCIM User resource uses the schema `urn:ietf:params:scim:schemas:core:2.0:User`; a PATCH request uses `urn:ietf:params:scim:api:messages:2.0:PatchOp`. `active: false` is a logical deactivation; DELETE may permanently remove the local record. This lab did **not** prove end-to-end deprovisioning.

![Flask application status JSON in browser](screenshots/lab-20261101-205122.png)

*Evidence: Flask application status JSON in browser. Screenshot captured November 1, 2026.*
## 6. Create and retrieve a user

Create `employee.json`:

```json
{
  "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
  "userName": "jane.smith@example.com",
  "name": {"givenName": "Jane", "familyName": "Smith"},
  "emails": [{"value": "jane.smith@example.com", "primary": true}],
  "active": true
}
```

Send a POST from the client terminal:

```powershell
curl.exe -i -X POST 'http://127.0.0.1:5004/scim/v2/Users' -H "Authorization: Bearer $env:SCIM_BEARER_TOKEN" -H 'Content-Type: application/scim+json' --data-binary '@employee.json'
```

**Observed:** `201 CREATED` with a generated UUID for Jane: `27ed20f2-94cf-4654-9385-805bc8e8c01a`.

Retrieve that identity:

```powershell
curl.exe -i -H "Authorization: Bearer $env:SCIM_BEARER_TOKEN" 'http://127.0.0.1:5004/scim/v2/Users/27ed20f2-94cf-4654-9385-805bc8e8c01a'
```

**Observed:** `200 OK` and Jane's SCIM JSON resource. The GET collection contained **two** users: Jane Smith (active) and John Doe (inactive).

![POST user creation returning HTTP 201 Created](screenshots/lab-20261101-202916.png)

*Evidence: POST user creation returning HTTP 201 Created. Screenshot captured November 1, 2026.*
![GET created user by UUID returning HTTP 200 OK](screenshots/lab-20261101-203014.png)

*Evidence: GET created user by UUID returning HTTP 200 OK. Screenshot captured November 1, 2026.*
## 7. PATCH a user's surname

**Scenario:** Jane Smith → Jane Johnson. Preserve the user ID and account status; update only `name.familyName`.

Create `update_employee.json`:

```json
{
  "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
  "Operations": [
    {"op": "replace", "path": "name.familyName", "value": "Johnson"}
  ]
}
```

```powershell
curl.exe -i -X PATCH 'http://127.0.0.1:5004/scim/v2/Users/27ed20f2-94cf-4654-9385-805bc8e8c01a' -H "Authorization: Bearer $env:SCIM_BEARER_TOKEN" -H 'Content-Type: application/scim+json' --data-binary '@update_employee.json'
```

**Observed:** `HTTP/1.1 200 OK`; response contained `"familyName": "Johnson"`.

### What changed in the Python code?

Initially, the handler only recognized paths like `active` and `userName`. It rejected `name.familyName` with `400 Unsupported PATCH path`. The fix required **two coordinated changes**:

1. Recognize and validate the incoming path, using lowercase comparison for matching while preserving the actual database column name:

```python
elif path.lower() == 'name.familyname':
    if not isinstance(value, str) or not value.strip():
        return scim_error('Invalid familyName', 400)
    changes['familyName'] = value.strip()
```

2. In the database update loop, handle the new field with **parameterized SQL**:

```python
elif field == 'familyName':
    db.execute('UPDATE users SET familyName = ? WHERE id = ?', (value, user_id))
```

Then commit and re-read the record. These snippets show the relevant behavior, not a replacement for the full `app.py`.

![Editing Python PATCH handler to support familyName](screenshots/lab-20261101-203808.png)

*Evidence: Editing Python PATCH handler to support familyName. Screenshot captured November 1, 2026.*
![Final successful PATCH response returning HTTP 200 OK](screenshots/lab-20261101-210349.png)

*Evidence: Final successful PATCH response returning HTTP 200 OK. Screenshot captured November 1, 2026.*
## 8. Verify SQLite persistence

The response alone is not enough: verify the database contains the changed surname.

```powershell
.\venv\Scripts\python.exe -c "import sqlite3; db=sqlite3.connect('scim_users.db'); print(db.execute('SELECT userName, givenName, familyName, active FROM users').fetchall()); db.close()"
```

**Observed rows:**

```text
('john.doe@example.com', 'John', 'Doe', 0)
('jane.smith@example.com', 'Jane', 'Johnson', 1)
```

| Record | Before | After | Account active? |
|---|---|---|---|
| Jane | Smith | **Johnson** | Yes (`1`) |
| John | Doe | Doe | No (`0`) |

Jane's email and username remained unchanged; changing a surname does not automatically rename a login identifier.

![Terminal evidence of PATCH success and database verification](screenshots/lab-20261101-210349.png)

*Evidence: Terminal evidence of PATCH success and database verification. Screenshot captured November 1, 2026.*
## 9. Troubleshooting: symptoms, diagnosis, fixes

### A. `401 Unauthorized` despite setting a token

**Symptom:** Flask responds but rejects the request. **Diagnosis:** client/server token mismatch or stale process environment. **Investigation:** check `$env:SCIM_BEARER_TOKEN.Length` in each relevant shell (do not print the secret); inspect port ownership; restart the intended Flask process after setting its token. **Resolution:** matching values in server and client yielded 200. **Lesson:** process environment variables are not shared dynamically.

```powershell
Get-NetTCPConnection -LocalPort 5004 -State Listen -ErrorAction SilentlyContinue | Select-Object OwningProcess
Get-CimInstance Win32_Process -Filter 'ProcessId = 27384' | Select-Object ProcessId, ParentProcessId, Name, CommandLine
```

`27384` is a **historical example PID**, not a value to reuse. Inspect your current PID before stopping any process.

![PowerShell test showing invalid-token 401](screenshots/lab-20261101-205600.png)

*Evidence: PowerShell test showing invalid-token 401. Screenshot captured November 1, 2026.*
![Process and port inspection during authentication troubleshooting](screenshots/lab-20261101-210039.png)

*Evidence: Process and port inspection during authentication troubleshooting. Screenshot captured November 1, 2026.*
### B. Multiple Flask instances on ports 5001–5004

**Symptom:** correct code appears to fail because curl hits an old process. **Diagnosis:** `Get-NetTCPConnection` plus `Get-CimInstance` showed Python processes still listening. **Fix:** stop only the confirmed obsolete process, then run a single no-reload Flask instance on the chosen port. **Lesson:** confirm both process and port, not just the URL.

### C. `400 Unsupported PATCH path`

**Symptom:** `name.familyName` rejected. **Cause:** PATCH validation lacked the path; the SQLite update loop also needed a `familyName` branch. **Fix:** implement both, then re-test with the same SCIM PatchOp body.

![Unsupported PATCH path response before code correction](screenshots/lab-20261101-203131.png)

*Evidence: Unsupported PATCH path response before code correction. Screenshot captured November 1, 2026.*
### D. `IndentationError: unexpected indent` around line 522

**Symptom:** Python could not compile `app.py`. **Cause:** inconsistent indentation around `elif` and the `changes` assignment. **Fix:** align sibling `if`/`elif` branches and use four-space indentation consistently. Run `py_compile` before starting Flask.

```powershell
.\venv\Scripts\python.exe -m py_compile app.py
```

A successful compile prints no output; it does not guarantee the runtime behavior is correct.

![Python code indentation and syntax checking](screenshots/lab-20261101-204451.png)

*Evidence: Python code indentation and syntax checking. Screenshot captured November 1, 2026.*
### E. `python.exe` not recognized

**Symptom:** `./venv/Scripts/python.exe` cannot be found from `C:\Users\FOXTEC`. **Cause:** wrong current working directory. **Fix:** `cd C:\Users\FOXTEC\entra-scim-lab` and use `.\venv\Scripts\python.exe`. Use `Test-Path .\venv\Scripts\python.exe` to verify.

### F. HTTP `404 Not Found`

**Symptom:** browser displays a missing route. **Cause:** wrong URL/port or unregistered endpoint. **Fix:** list registered routes:

```powershell
.\venv\Scripts\python.exe -m flask --app app routes
```

### G. `Set-Clipboard -Value ""` fails

PowerShell rejects a null/empty argument for `-Value` in this context. This is a shell command error, **not** a SCIM API error. The later token problem was solved by configuring the environment variable correctly in the relevant process.

### H. HTTP 400 vs 401 vs 404 vs 409

| Status | Meaning | Diagnostic question |
|---|---|---|
| 200 | Successful read/update | Is the returned resource correct? |
| 201 | Created | Was a new UUID generated? |
| 204 | Success, no body | Was the requested deletion applied? |
| 400 | Invalid request | Is the JSON schema, operation or path supported? |
| 401 | Authentication failed | Does the server expect this bearer token? |
| 404 | Resource/route missing | Is the ID, method, port or path correct? |
| 409 | Conflict | Does the username already exist? |
| 500 | Server failure | What does the Flask traceback show? |

## 10. Results and limitations

| Check | Evidence | Status |
|---|---|---|
| Flask running | Status endpoint, server terminal | Verified |
| Route registration | `flask routes` | Verified |
| Valid bearer token | GET `/Users` → 200 | Verified |
| Invalid bearer token | GET `/Users` → 401 | Verified |
| POST user | `201 Created` | Verified |
| GET user | `200 OK` | Verified |
| PATCH surname | `200 OK`, Johnson in response | Verified |
| SQLite commit | Johnson in SELECT result | Verified |
| DELETE workflow | Route registered | **Not end-to-end verified** |
| Entra ID provisioning | Not connected | **Future work** |
| Full SCIM 2.0 compliance | Not assessed | **Future work** |

## 11. Security and next steps

**This is a local development server.** Do not publish real bearer tokens, real employee information, or the SQLite database. Before uploading screenshots, review them for secrets, personally identifiable information, or internal URLs. Use `.gitignore`:

```gitignore
venv/
.venv/
__pycache__/
*.pyc
.env
scim_users.db
*.sqlite
*.sqlite3
```

**Next engineering milestones:** deploy behind HTTPS; implement secret rotation, audit logging and rate limiting; verify filtering/pagination and SCIM edge cases; test PATCH deactivate/reactivate; add Groups support; use a test Entra enterprise app to configure provisioning, attribute mapping, scoping and logs; run automated tests. The Flask development server is not suitable for production.

**Interview takeaway:** I built and debugged a local SCIM-style identity API, traced requests through bearer authentication and Flask routing to SQLite, corrected unsupported PATCH attribute handling, and independently verified persistence. I have not yet implemented live Microsoft Entra provisioning.

## 12. Complete screenshot evidence gallery

The gallery below contains **every available screenshot from this lab session** in chronological filename order. The most important results are explained above. Early screenshots show experiments, failed attempts and debugging; they are not all successful test evidence. Click each image to inspect it at full resolution on GitHub.


### Evidence set 1 — screenshots 1–15

<details>
<summary>Screenshot 001 — 130453</summary>

![SCIM lab screenshot 20261101-130453](screenshots/lab-20261101-130453.png)

Original capture: `20261101-130453`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 002 — 130629</summary>

![SCIM lab screenshot 20261101-130629](screenshots/lab-20261101-130629.png)

Original capture: `20261101-130629`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 003 — 131501</summary>

![SCIM lab screenshot 20261101-131501](screenshots/lab-20261101-131501.png)

Original capture: `20261101-131501`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 004 — 131716</summary>

![SCIM lab screenshot 20261101-131716](screenshots/lab-20261101-131716.png)

Original capture: `20261101-131716`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 005 — 132947</summary>

![SCIM lab screenshot 20261101-132947](screenshots/lab-20261101-132947.png)

Original capture: `20261101-132947`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 006 — 133147</summary>

![SCIM lab screenshot 20261101-133147](screenshots/lab-20261101-133147.png)

Original capture: `20261101-133147`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 007 — 133315</summary>

![SCIM lab screenshot 20261101-133315](screenshots/lab-20261101-133315.png)

Original capture: `20261101-133315`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 008 — 133411</summary>

![SCIM lab screenshot 20261101-133411](screenshots/lab-20261101-133411.png)

Original capture: `20261101-133411`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 009 — 134523</summary>

![SCIM lab screenshot 20261101-134523](screenshots/lab-20261101-134523.png)

Original capture: `20261101-134523`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 010 — 134703</summary>

![SCIM lab screenshot 20261101-134703](screenshots/lab-20261101-134703.png)

Original capture: `20261101-134703`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 011 — 134914</summary>

![SCIM lab screenshot 20261101-134914](screenshots/lab-20261101-134914.png)

Original capture: `20261101-134914`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 012 — 134955</summary>

![SCIM lab screenshot 20261101-134955](screenshots/lab-20261101-134955.png)

Original capture: `20261101-134955`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 013 — 135155</summary>

![SCIM lab screenshot 20261101-135155](screenshots/lab-20261101-135155.png)

Original capture: `20261101-135155`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 014 — 135329</summary>

![SCIM lab screenshot 20261101-135329](screenshots/lab-20261101-135329.png)

Original capture: `20261101-135329`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 015 — 135543</summary>

![SCIM lab screenshot 20261101-135543](screenshots/lab-20261101-135543.png)

Original capture: `20261101-135543`. Review this screenshot in context with the walkthrough above.

</details>


### Evidence set 2 — screenshots 16–30

<details>
<summary>Screenshot 016 — 135657</summary>

![SCIM lab screenshot 20261101-135657](screenshots/lab-20261101-135657.png)

Original capture: `20261101-135657`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 017 — 155843</summary>

![SCIM lab screenshot 20261101-155843](screenshots/lab-20261101-155843.png)

Original capture: `20261101-155843`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 018 — 161343</summary>

![SCIM lab screenshot 20261101-161343](screenshots/lab-20261101-161343.png)

Original capture: `20261101-161343`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 019 — 161501</summary>

![SCIM lab screenshot 20261101-161501](screenshots/lab-20261101-161501.png)

Original capture: `20261101-161501`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 020 — 161616</summary>

![SCIM lab screenshot 20261101-161616](screenshots/lab-20261101-161616.png)

Original capture: `20261101-161616`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 021 — 162617</summary>

![SCIM lab screenshot 20261101-162617](screenshots/lab-20261101-162617.png)

Original capture: `20261101-162617`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 022 — 162908</summary>

![SCIM lab screenshot 20261101-162908](screenshots/lab-20261101-162908.png)

Original capture: `20261101-162908`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 023 — 163242</summary>

![SCIM lab screenshot 20261101-163242](screenshots/lab-20261101-163242.png)

Original capture: `20261101-163242`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 024 — 164535</summary>

![SCIM lab screenshot 20261101-164535](screenshots/lab-20261101-164535.png)

Original capture: `20261101-164535`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 025 — 164701</summary>

![SCIM lab screenshot 20261101-164701](screenshots/lab-20261101-164701.png)

Original capture: `20261101-164701`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 026 — 164801</summary>

![SCIM lab screenshot 20261101-164801](screenshots/lab-20261101-164801.png)

Original capture: `20261101-164801`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 027 — 164932</summary>

![SCIM lab screenshot 20261101-164932](screenshots/lab-20261101-164932.png)

Original capture: `20261101-164932`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 028 — 165047</summary>

![SCIM lab screenshot 20261101-165047](screenshots/lab-20261101-165047.png)

Original capture: `20261101-165047`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 029 — 165200</summary>

![SCIM lab screenshot 20261101-165200](screenshots/lab-20261101-165200.png)

Original capture: `20261101-165200`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 030 — 165331</summary>

![SCIM lab screenshot 20261101-165331](screenshots/lab-20261101-165331.png)

Original capture: `20261101-165331`. Review this screenshot in context with the walkthrough above.

</details>


### Evidence set 3 — screenshots 31–45

<details>
<summary>Screenshot 031 — 165441</summary>

![SCIM lab screenshot 20261101-165441](screenshots/lab-20261101-165441.png)

Original capture: `20261101-165441`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 032 — 165525</summary>

![SCIM lab screenshot 20261101-165525](screenshots/lab-20261101-165525.png)

Original capture: `20261101-165525`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 033 — 165634</summary>

![SCIM lab screenshot 20261101-165634](screenshots/lab-20261101-165634.png)

Original capture: `20261101-165634`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 034 — 170107</summary>

![SCIM lab screenshot 20261101-170107](screenshots/lab-20261101-170107.png)

Original capture: `20261101-170107`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 035 — 170339</summary>

![SCIM lab screenshot 20261101-170339](screenshots/lab-20261101-170339.png)

Original capture: `20261101-170339`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 036 — 170711</summary>

![SCIM lab screenshot 20261101-170711](screenshots/lab-20261101-170711.png)

Original capture: `20261101-170711`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 037 — 170834</summary>

![SCIM lab screenshot 20261101-170834](screenshots/lab-20261101-170834.png)

Original capture: `20261101-170834`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 038 — 171057</summary>

![SCIM lab screenshot 20261101-171057](screenshots/lab-20261101-171057.png)

Original capture: `20261101-171057`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 039 — 171153</summary>

![SCIM lab screenshot 20261101-171153](screenshots/lab-20261101-171153.png)

Original capture: `20261101-171153`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 040 — 171251</summary>

![SCIM lab screenshot 20261101-171251](screenshots/lab-20261101-171251.png)

Original capture: `20261101-171251`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 041 — 171345</summary>

![SCIM lab screenshot 20261101-171345](screenshots/lab-20261101-171345.png)

Original capture: `20261101-171345`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 042 — 171439</summary>

![SCIM lab screenshot 20261101-171439](screenshots/lab-20261101-171439.png)

Original capture: `20261101-171439`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 043 — 171619</summary>

![SCIM lab screenshot 20261101-171619](screenshots/lab-20261101-171619.png)

Original capture: `20261101-171619`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 044 — 175833</summary>

![SCIM lab screenshot 20261101-175833](screenshots/lab-20261101-175833.png)

Original capture: `20261101-175833`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 045 — 180121</summary>

![SCIM lab screenshot 20261101-180121](screenshots/lab-20261101-180121.png)

Original capture: `20261101-180121`. Review this screenshot in context with the walkthrough above.

</details>


### Evidence set 4 — screenshots 46–60

<details>
<summary>Screenshot 046 — 180358</summary>

![SCIM lab screenshot 20261101-180358](screenshots/lab-20261101-180358.png)

Original capture: `20261101-180358`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 047 — 180448</summary>

![SCIM lab screenshot 20261101-180448](screenshots/lab-20261101-180448.png)

Original capture: `20261101-180448`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 048 — 180707</summary>

![SCIM lab screenshot 20261101-180707](screenshots/lab-20261101-180707.png)

Original capture: `20261101-180707`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 049 — 194447</summary>

![SCIM lab screenshot 20261101-194447](screenshots/lab-20261101-194447.png)

Original capture: `20261101-194447`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 050 — 200255</summary>

![SCIM lab screenshot 20261101-200255](screenshots/lab-20261101-200255.png)

Original capture: `20261101-200255`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 051 — 200356</summary>

![SCIM lab screenshot 20261101-200356](screenshots/lab-20261101-200356.png)

Original capture: `20261101-200356`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 052 — 200444</summary>

![SCIM lab screenshot 20261101-200444](screenshots/lab-20261101-200444.png)

Original capture: `20261101-200444`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 053 — 200612</summary>

![SCIM lab screenshot 20261101-200612](screenshots/lab-20261101-200612.png)

Original capture: `20261101-200612`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 054 — 200650</summary>

![SCIM lab screenshot 20261101-200650](screenshots/lab-20261101-200650.png)

Original capture: `20261101-200650`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 055 — 200944</summary>

![SCIM lab screenshot 20261101-200944](screenshots/lab-20261101-200944.png)

Original capture: `20261101-200944`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 056 — 201022</summary>

![SCIM lab screenshot 20261101-201022](screenshots/lab-20261101-201022.png)

Original capture: `20261101-201022`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 057 — 201103</summary>

![SCIM lab screenshot 20261101-201103](screenshots/lab-20261101-201103.png)

Original capture: `20261101-201103`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 058 — 201227</summary>

![SCIM lab screenshot 20261101-201227](screenshots/lab-20261101-201227.png)

Original capture: `20261101-201227`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 059 — 201346</summary>

![SCIM lab screenshot 20261101-201346](screenshots/lab-20261101-201346.png)

Original capture: `20261101-201346`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 060 — 201419</summary>

![SCIM lab screenshot 20261101-201419](screenshots/lab-20261101-201419.png)

Original capture: `20261101-201419`. Review this screenshot in context with the walkthrough above.

</details>


### Evidence set 5 — screenshots 61–75

<details>
<summary>Screenshot 061 — 201503</summary>

![SCIM lab screenshot 20261101-201503](screenshots/lab-20261101-201503.png)

Original capture: `20261101-201503`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 062 — 201548</summary>

![SCIM lab screenshot 20261101-201548](screenshots/lab-20261101-201548.png)

Original capture: `20261101-201548`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 063 — 201621</summary>

![SCIM lab screenshot 20261101-201621](screenshots/lab-20261101-201621.png)

Original capture: `20261101-201621`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 064 — 201656</summary>

![SCIM lab screenshot 20261101-201656](screenshots/lab-20261101-201656.png)

Original capture: `20261101-201656`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 065 — 201743</summary>

![SCIM lab screenshot 20261101-201743](screenshots/lab-20261101-201743.png)

Original capture: `20261101-201743`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 066 — 201809</summary>

![SCIM lab screenshot 20261101-201809](screenshots/lab-20261101-201809.png)

Original capture: `20261101-201809`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 067 — 201854</summary>

![SCIM lab screenshot 20261101-201854](screenshots/lab-20261101-201854.png)

Original capture: `20261101-201854`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 068 — 201939</summary>

![SCIM lab screenshot 20261101-201939](screenshots/lab-20261101-201939.png)

Original capture: `20261101-201939`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 069 — 202003</summary>

![SCIM lab screenshot 20261101-202003](screenshots/lab-20261101-202003.png)

Original capture: `20261101-202003`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 070 — 202212</summary>

![SCIM lab screenshot 20261101-202212](screenshots/lab-20261101-202212.png)

Original capture: `20261101-202212`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 071 — 202501</summary>

![SCIM lab screenshot 20261101-202501](screenshots/lab-20261101-202501.png)

Original capture: `20261101-202501`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 072 — 202607</summary>

![SCIM lab screenshot 20261101-202607](screenshots/lab-20261101-202607.png)

Original capture: `20261101-202607`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 073 — 202723</summary>

![SCIM lab screenshot 20261101-202723](screenshots/lab-20261101-202723.png)

Original capture: `20261101-202723`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 074 — 202827</summary>

![SCIM lab screenshot 20261101-202827](screenshots/lab-20261101-202827.png)

Original capture: `20261101-202827`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 075 — 202916</summary>

![SCIM lab screenshot 20261101-202916](screenshots/lab-20261101-202916.png)

Original capture: `20261101-202916`. Review this screenshot in context with the walkthrough above.

</details>


### Evidence set 6 — screenshots 76–90

<details>
<summary>Screenshot 076 — 203014</summary>

![SCIM lab screenshot 20261101-203014](screenshots/lab-20261101-203014.png)

Original capture: `20261101-203014`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 077 — 203131</summary>

![SCIM lab screenshot 20261101-203131](screenshots/lab-20261101-203131.png)

Original capture: `20261101-203131`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 078 — 203242</summary>

![SCIM lab screenshot 20261101-203242](screenshots/lab-20261101-203242.png)

Original capture: `20261101-203242`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 079 — 203335</summary>

![SCIM lab screenshot 20261101-203335](screenshots/lab-20261101-203335.png)

Original capture: `20261101-203335`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 080 — 203411</summary>

![SCIM lab screenshot 20261101-203411](screenshots/lab-20261101-203411.png)

Original capture: `20261101-203411`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 081 — 203445</summary>

![SCIM lab screenshot 20261101-203445](screenshots/lab-20261101-203445.png)

Original capture: `20261101-203445`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 082 — 203515</summary>

![SCIM lab screenshot 20261101-203515](screenshots/lab-20261101-203515.png)

Original capture: `20261101-203515`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 083 — 203555</summary>

![SCIM lab screenshot 20261101-203555](screenshots/lab-20261101-203555.png)

Original capture: `20261101-203555`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 084 — 203643</summary>

![SCIM lab screenshot 20261101-203643](screenshots/lab-20261101-203643.png)

Original capture: `20261101-203643`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 085 — 203808</summary>

![SCIM lab screenshot 20261101-203808](screenshots/lab-20261101-203808.png)

Original capture: `20261101-203808`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 086 — 203928</summary>

![SCIM lab screenshot 20261101-203928](screenshots/lab-20261101-203928.png)

Original capture: `20261101-203928`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 087 — 204020</summary>

![SCIM lab screenshot 20261101-204020](screenshots/lab-20261101-204020.png)

Original capture: `20261101-204020`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 088 — 204339</summary>

![SCIM lab screenshot 20261101-204339](screenshots/lab-20261101-204339.png)

Original capture: `20261101-204339`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 089 — 204451</summary>

![SCIM lab screenshot 20261101-204451](screenshots/lab-20261101-204451.png)

Original capture: `20261101-204451`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 090 — 204650</summary>

![SCIM lab screenshot 20261101-204650](screenshots/lab-20261101-204650.png)

Original capture: `20261101-204650`. Review this screenshot in context with the walkthrough above.

</details>


### Evidence set 7 — screenshots 91–103

<details>
<summary>Screenshot 091 — 204905</summary>

![SCIM lab screenshot 20261101-204905](screenshots/lab-20261101-204905.png)

Original capture: `20261101-204905`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 092 — 204937</summary>

![SCIM lab screenshot 20261101-204937](screenshots/lab-20261101-204937.png)

Original capture: `20261101-204937`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 093 — 205035</summary>

![SCIM lab screenshot 20261101-205035](screenshots/lab-20261101-205035.png)

Original capture: `20261101-205035`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 094 — 205122</summary>

![SCIM lab screenshot 20261101-205122](screenshots/lab-20261101-205122.png)

Original capture: `20261101-205122`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 095 — 205326</summary>

![SCIM lab screenshot 20261101-205326](screenshots/lab-20261101-205326.png)

Original capture: `20261101-205326`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 096 — 205456</summary>

![SCIM lab screenshot 20261101-205456](screenshots/lab-20261101-205456.png)

Original capture: `20261101-205456`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 097 — 205600</summary>

![SCIM lab screenshot 20261101-205600](screenshots/lab-20261101-205600.png)

Original capture: `20261101-205600`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 098 — 205719</summary>

![SCIM lab screenshot 20261101-205719](screenshots/lab-20261101-205719.png)

Original capture: `20261101-205719`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 099 — 210039</summary>

![SCIM lab screenshot 20261101-210039](screenshots/lab-20261101-210039.png)

Original capture: `20261101-210039`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 100 — 210207</summary>

![SCIM lab screenshot 20261101-210207](screenshots/lab-20261101-210207.png)

Original capture: `20261101-210207`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 101 — 210314</summary>

![SCIM lab screenshot 20261101-210314](screenshots/lab-20261101-210314.png)

Original capture: `20261101-210314`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 102 — 210349</summary>

![SCIM lab screenshot 20261101-210349](screenshots/lab-20261101-210349.png)

Original capture: `20261101-210349`. Review this screenshot in context with the walkthrough above.

</details>

<details>
<summary>Screenshot 103 — 211439</summary>

![SCIM lab screenshot 20261101-211439](screenshots/lab-20261101-211439.png)

Original capture: `20261101-211439`. Review this screenshot in context with the walkthrough above.

</details>


---

**Author:** Farhan Muntasir · **Lab date:** November 1, 2026 · **Standards:** [RFC 7643](https://www.rfc-editor.org/rfc/rfc7643) / [RFC 7644](https://www.rfc-editor.org/rfc/rfc7644)
