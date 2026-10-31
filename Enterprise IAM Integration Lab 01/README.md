# Enterprise IAM Integration Lab 01
## SAML 2.0 Single Sign-On with Microsoft Entra ID and Python Flask

### Project Overview

This hands-on Identity and Access Management (IAM) lab demonstrates the implementation of **SAML 2.0 Single Sign-On (SSO)** using Microsoft Entra ID as the Identity Provider (IdP) and a custom Python Flask application as the Service Provider (SP).

The project demonstrates enterprise identity federation, secure authentication, SAML assertion validation, HTTPS configuration, session management, and troubleshooting.

**Lab Status:** Successfully completed and tested.

---

## 1. Lab Objectives

- Configure Microsoft Entra ID as a SAML Identity Provider.
- Register a non-gallery Enterprise Application.
- Develop a Python Flask SAML Service Provider.
- Configure SAML 2.0 federation metadata.
- Implement SP-initiated SSO authentication.
- Configure HTTPS and secure session cookies.
- Validate SAML authentication responses.
- Troubleshoot SAML authentication failures.
- Verify successful sign-in and local logout.

## 2. Technologies Used

| Technology | Purpose |
|---|---|
| Microsoft Entra ID | Identity Provider |
| SAML 2.0 | Federation protocol |
| Python 3.13 | Application development |
| Flask | Web application framework |
| python3-saml | SAML authentication toolkit |
| python-dotenv | Environment variable management |
| cryptography | Local HTTPS certificate support |
| Windows CMD | Development and administration |
| JSON / XML | SAML configuration and metadata |
| GitHub | Project documentation |

## 3. Authentication Architecture

```text
             User / Web Browser
                     |
                     v
          Python Flask Application
             (Service Provider)
                     |
                     | SAML AuthnRequest
                     v
             Microsoft Entra ID
              (Identity Provider)
                     |
                     | User Authentication
                     |
                     | SAML Response
                     v
          Flask ACS Endpoint (/acs)
                     |
                     | Validate SAML Response
                     | Verify Request Correlation
                     v
           Authenticated Session
                     |
                     v
           Authentication Successful
```

The application uses **SP-initiated SAML SSO**, meaning the authentication request originates from the Flask application.

## 4. Project Structure

```text
Enterprise-IAM-SAML-Lab01/
|
|-- app.py
|-- configure_saml.py
|-- .env
|-- .gitignore
|-- README.md
|
|-- saml/
|   |-- settings.json
|   |-- advanced_settings.json
|   |-- entra-id-metadata.xml
|
|-- screenshots/
|   |-- 01-saml-authentication-success.png
|   |-- 02-local-logout-success.png
|
|-- .venv/
```

The `.env` and `.venv` directories/files must not be committed to GitHub.

## 5. Configure the Python Environment

Open Windows Command Prompt.

Navigate to the project directory:

```cmd
cd /d C:\Users\FOXTEC\Documents\Enterprise-IAM-SAML-Lab01
```

Verify Python:

```cmd
python --version
```

Create a virtual environment:

```cmd
python -m venv .venv
```

Activate the environment:

```cmd
.venv\Scripts\activate
```

Install dependencies:

```cmd
python -m pip install Flask python3-saml python-dotenv cryptography
```

Verify installed packages:

```cmd
python -m pip list
```

## 6. Configure Microsoft Entra ID

Navigate to:

**Microsoft Entra admin center → Entra ID → Enterprise applications → All applications**

Create a non-gallery application named:

`Enterprise-IAM-SAML-Lab01`

Navigate to:

**Single sign-on → SAML → Basic SAML Configuration**

Configure:

| Configuration | Value |
|---|---|
| Identifier (Entity ID) | https://127.0.0.1:5000/metadata |
| Reply URL (ACS) | https://127.0.0.1:5000/acs |
| Authentication Protocol | SAML 2.0 |
| Identity Provider | Microsoft Entra ID |

Download the Federation Metadata XML from the SAML configuration page.

The metadata contains the Identity Provider entity ID, authentication endpoint, and public signing certificate.

## 7. Configure the SAML Service Provider

Open the SAML configuration file:

```cmd
notepad saml\settings.json
```

Example SP configuration:

```json
{
  "sp": {
    "entityId": "https://127.0.0.1:5000/metadata",
    "assertionConsumerService": {
      "url": "https://127.0.0.1:5000/acs",
      "binding": "urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
    },
    "NameIDFormat": "urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified",
    "x509cert": "",
    "privateKey": ""
  }
}
```

This is an excerpt; the complete settings file also requires the IdP configuration and signing certificate.

The Entity ID uniquely identifies the Service Provider.

The ACS endpoint receives the SAML authentication response from Microsoft Entra ID.

Validate the JSON configuration:

```cmd
python -m json.tool saml\settings.json
```

## 8. Configure Secure Flask Sessions

The Flask application uses the following session configuration:

```python
app.config.update(
    SESSION_COOKIE_HTTPONLY=True,
    SESSION_COOKIE_SAMESITE="None",
    SESSION_COOKIE_SECURE=True
)
```

Security purposes:

- **HTTPOnly:** Prevents JavaScript from reading the session cookie.
- **SameSite=None:** Allows cookies during cross-site SAML POST authentication.
- **Secure=True:** Restricts session cookies to HTTPS connections.

HTTPS is enabled locally with:

```python
app.run(
    host="127.0.0.1",
    port=5000,
    debug=False,
    ssl_context="adhoc"
)
```

This generates a temporary development certificate and is not a production HTTPS deployment.

## 9. Implement SAML Authentication

The Flask application exposes the following endpoints:

| Route | Function |
|---|---|
| `/` | Application homepage |
| `/login` | Initiates SAML authentication |
| `/acs` | Processes SAML response |
| `/metadata` | Publishes SP metadata |
| `/logout` | Clears local application session |

The login endpoint generates a SAML authentication request and stores its request ID:

```python
redirect_url = auth.login()
session["saml_request_id"] = auth.get_last_request_id()
return redirect(redirect_url)
```

The ACS endpoint processes the returned response:

```python
auth.process_response(request_id=request_id)
```

The SAML toolkit validates the response according to the configured security settings, including request correlation and signature requirements.

## 10. Start the Flask Application

Validate Python syntax:

```cmd
python -m py_compile app.py
```

Start the application:

```cmd
python app.py
```

Open:

https://127.0.0.1:5000/

The homepage displays:

**Sign in with Microsoft Entra ID**

Click the sign-in link to initiate authentication.

## 11. Troubleshooting

### Issue: Missing SAML Request Correlation

During initial testing, the application returned:

```text
400 Bad Request
Missing SAML request correlation
```

**Cause:** The browser did not return the expected Flask session cookie during the cross-site SAML POST.

**Resolution:**

1. Enabled local HTTPS.
2. Configured `SESSION_COOKIE_SAMESITE="None"`.
3. Enabled `SESSION_COOKIE_SECURE=True`.
4. Updated the SAML Entity ID and ACS URL to HTTPS.
5. Updated Microsoft Entra ID to use the matching HTTPS endpoints.
6. Retested SP-initiated authentication.

**Result:** The Flask application successfully received and validated the SAML response.

### Issue: HTTPS Certificate Warning

The Flask development server uses a temporary self-signed certificate.

**Resolution:** For local testing only, verify the address is the intended loopback server before proceeding through the browser warning.

Production environments require a properly trusted TLS certificate.

## 12. Authentication Verification

The following tests were completed:

| Test Case | Result |
|---|---|
| Flask HTTPS application loads | Passed |
| SAML authentication initiated | Passed |
| Microsoft Entra ID sign-in | Passed |
| SAML response processed | Passed |
| Request correlation validated | Passed |
| Authenticated user identity displayed | Passed |
| Flask authenticated session created | Passed |
| Local session logout | Passed |

### Successful SAML Authentication

![Successful SAML Authentication](screenshots/01-saml-authentication-success.png)

The application displayed:

**Authentication Successful**

**Microsoft Entra ID SAML SSO completed successfully.**

### Local Logout Verification

![Local Logout Verification](screenshots/02-local-logout-success.png)

After logout, the application returned to the unauthenticated homepage and displayed the Microsoft Entra ID sign-in link.

**Note:** The logout functionality currently clears the local Flask session. Full SAML Single Logout (SLO) has not been implemented.

## 13. Security Considerations

- Use HTTPS to protect authentication traffic.
- Validate SAML signatures and assertions.
- Verify request correlation using SAML request IDs.
- Protect Flask session cookies.
- Keep application secrets outside source control.
- Do not commit private keys or sensitive authentication data.
- Use a trusted TLS certificate in production.
- Do not advertise SAML Single Logout until implemented.
- Apply least-privilege access and enterprise application assignment policies where appropriate.

Recommended `.gitignore`:

```gitignore
.venv/
.env
__pycache__/
*.pyc
*.key
*.pem
```

Review configuration files and screenshots for sensitive information before publishing.

## 14. IAM Skills Demonstrated

This lab provides practical experience with:

- Identity federation
- Microsoft Entra Enterprise Applications
- SAML 2.0 Single Sign-On
- Identity Provider and Service Provider integration
- SAML metadata configuration
- Authentication assertion validation
- Session security
- HTTPS configuration
- Identity integration troubleshooting
- Python-based authentication workflows

## 15. Conclusion

Successfully implemented and tested SP-initiated SAML 2.0 Single Sign-On between Microsoft Entra ID and a custom Python Flask application.

The integration demonstrated centralized identity authentication, SAML response validation, secure session handling, and successful local logout.

This project forms part of an enterprise IAM integration lab series covering identity federation and application provisioning technologies.

### Future Labs

- Lab 02 — OAuth 2.0 Authorization with Microsoft Entra ID
- Lab 03 — OpenID Connect Authentication
- Lab 04 — SCIM 2.0 Automated User Provisioning
- Lab 05 — Enterprise SSO Integration with Conditional Access

---

**Author:** Farhan Muntasir

**Certifications:** Microsoft Certified: Identity and Access Administrator Associate (SC-300) | CompTIA Security+

**GitHub:** https://github.com/muntasir-cyber-labs

**Project:** Enterprise IAM Integration Lab Series
