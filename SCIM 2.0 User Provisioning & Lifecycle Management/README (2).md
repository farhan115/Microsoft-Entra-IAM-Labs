# Enterprise IAM Integration Lab — SCIM 2.0 User Provisioning

**Author:** Farhan Muntasir  
**Date:** November 1, 2026  
**Stack:** Python 3.13 · Flask · SQLite · SCIM 2.0 · REST · PowerShell  
**Status:** Local lab completed; Microsoft Entra ID integration planned

![SCIM 2.0 lab architecture](screenshots/scim-architecture.png)

## Overview

Built and tested a local SCIM 2.0 provisioning API using Flask and SQLite. The application accepts authenticated HTTP requests to create, list, retrieve, update, and delete user records. PowerShell and `curl.exe` acted as the provisioning client. **Microsoft Entra ID was not connected** in this lab.

## Architecture

PowerShell/curl client → Bearer-token authentication → Flask SCIM endpoints → SQLite `scim_users.db`.

In a future integration, Microsoft Entra ID provisioning would replace the manual curl client, subject to endpoint security and SCIM compatibility requirements.

## API endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/` | Application status |
| GET | `/scim/v2/Users` | List users |
| POST | `/scim/v2/Users` | Create user |
| GET | `/scim/v2/Users/{id}` | Retrieve user |
| PATCH | `/scim/v2/Users/{id}` | Update supported attributes |
| DELETE | `/scim/v2/Users/{id}` | Delete user (route implemented; final end-to-end test not documented) |
| GET | `/scim/v2/ServiceProviderConfig` | Service capabilities |

## Setup

From PowerShell in the project directory:

```powershell
cd C:\Users\FOXTEC\entra-scim-lab
.\venv\Scripts\python.exe -m pip install flask
$env:SCIM_BEARER_TOKEN = 'replace-with-a-local-test-secret'
.\venv\Scripts\python.exe -m py_compile app.py
.\venv\Scripts\python.exe -m flask --app app run --host 127.0.0.1 --port 5004 --no-reload --no-debugger
```

Open a **second PowerShell session** and set the same local test token there before sending requests. Do not expose this development server to the internet.

## 1. Test authentication and list users

```powershell
$env:SCIM_BEARER_TOKEN = 'replace-with-a-local-test-secret'
curl.exe -i -H "Authorization: Bearer $env:SCIM_BEARER_TOKEN" http://127.0.0.1:5004/scim/v2/Users
```

**Verified:** valid token returned `HTTP/1.1 200 OK`; invalid token returned `401 Unauthorized`.

## 2. Create a user

Save as `employee.json`:

```json
{
  "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
  "userName": "jane.smith@example.com",
  "name": {"givenName": "Jane", "familyName": "Smith"},
  "emails": [{"value": "jane.smith@example.com", "primary": true}],
  "active": true
}
```

```powershell
curl.exe -i -X POST 'http://127.0.0.1:5004/scim/v2/Users' -H "Authorization: Bearer $env:SCIM_BEARER_TOKEN" -H 'Content-Type: application/scim+json' --data-binary '@employee.json'
```

**Verified:** `201 Created` with a generated user UUID.

## 3. Update the surname with SCIM PATCH

Save as `update_employee.json`:

```json
{
  "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
  "Operations": [{"op": "replace", "path": "name.familyName", "value": "Johnson"}]
}
```

```powershell
curl.exe -i -X PATCH 'http://127.0.0.1:5004/scim/v2/Users/27ed20f2-94cf-4654-9385-805bc8e8c01a' -H "Authorization: Bearer $env:SCIM_BEARER_TOKEN" -H 'Content-Type: application/scim+json' --data-binary '@update_employee.json'
```

**Verified:** `200 OK`, with Jane's `familyName` updated from `Smith` to `Johnson`.

## 4. Verify SQLite persistence

```powershell
.\venv\Scripts\python.exe -c "import sqlite3; db=sqlite3.connect('scim_users.db'); print(db.execute('SELECT userName, givenName, familyName, active FROM users').fetchall()); db.close()"
```

Observed records:

| Username | Given name | Family name | Active |
|---|---|---|---|
| `jane.smith@example.com` | Jane | Johnson | 1 |
| `john.doe@example.com` | John | Doe | 0 |

## Troubleshooting journal

| Symptom | Diagnosis | Fix / verification |
|---|---|---|
| `401 Unauthorized` | Client token and server token differed, or the Flask process inherited an older environment | Set matching `SCIM_BEARER_TOKEN` in both sessions; restart Flask; retest valid and invalid tokens |
| `400 Unsupported PATCH path` | Handler did not support `name.familyName` | Add path validation and a parameterized SQL update for `familyName` |
| `IndentationError` | Misaligned Python code in PATCH handler | Correct block indentation; run `python -m py_compile app.py` |
| `404 Not Found` | Incorrect endpoint/port or unregistered route | Inspect `python -m flask --app app routes` |
| Python executable not found | PowerShell working directory was wrong | `cd C:\Users\FOXTEC\entra-scim-lab` before using relative paths |
| Wrong Flask instance | Several Python servers had been launched on different ports | Use `Get-NetTCPConnection -LocalPort 5004 -State Listen` and inspect the owning process |
| API response not enough | Needed proof the change was committed | Query SQLite directly and verify Jane Johnson |

### Useful troubleshooting commands

```powershell
$env:SCIM_BEARER_TOKEN.Length
.\venv\Scripts\python.exe -m py_compile app.py
.\venv\Scripts\python.exe -m flask --app app routes
Get-NetTCPConnection -LocalPort 5004 -State Listen | Select-Object OwningProcess
```

## Verified test results

- [x] Flask server started locally
- [x] Authenticated `GET /Users`: `200 OK`
- [x] Invalid bearer token rejected: `401 Unauthorized`
- [x] User created with `POST`: `201 Created`
- [x] User retrieved by UUID: `200 OK`
- [x] Surname updated with `PATCH`: `200 OK`
- [x] Updated surname verified in SQLite
- [ ] Entra ID automatic provisioning integration
- [ ] Complete deletion and deactivation workflow validation

## Security and limitations

This is a learning implementation, **not a production-ready SCIM service**. Production use requires HTTPS, protected and rotated secrets, robust schema validation, audit logs, least privilege, appropriate error handling, and additional interoperability testing. Do not commit live tokens or identity data.

Suggested `.gitignore`:

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

## Future enhancements

Connect Microsoft Entra ID to a securely hosted SCIM endpoint, test provisioning mappings, add group provisioning, verify account deactivation/reactivation, and implement automated API tests.

## References

- [RFC 7643 — SCIM Core Schema](https://www.rfc-editor.org/rfc/rfc7643)
- [RFC 7644 — SCIM Protocol](https://www.rfc-editor.org/rfc/rfc7644)

---

**Author:** Farhan Muntasir · IAM / Identity Integration Lab Portfolio
