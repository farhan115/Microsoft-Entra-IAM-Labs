Day-29-Microsoft-Graph-Create-User/
│
├── README.md
├── 01-ReadWrite-Permission.png
├── 02-Request-Body.png
├── 03-User-Created.png
├── 04-User-Verified.png
└── 05-Graph-Verification.png

# Day 29 – Microsoft Graph API: Create a User

## Objective

Learn how to create a Microsoft Entra ID user using Microsoft Graph Explorer and the Microsoft Graph REST API.

---

## Scenario

The HR department requested a new employee account for **David Miller**, a Marketing Specialist. Instead of using the Microsoft Entra admin center, the account was created through Microsoft Graph.

---

## Lab Environment

- Microsoft Entra ID Free
- Microsoft Graph Explorer
- Microsoft Graph API
- OAuth 2.0
- JSON

---

## Tasks Performed

### 1. Granted Microsoft Graph Permissions

Granted the delegated permission:

- `User.ReadWrite.All`

This permission allows creating and managing user objects through Microsoft Graph.

### 2. Configured a POST Request

Changed the HTTP method from `GET` to `POST` and targeted the `/users` endpoint.

### 3. Created a JSON Request Body

Defined the new user's attributes, including:

- Display name
- User principal name
- Mail nickname
- Initial password
- Password change requirement

### 4. Created the User

Submitted the request and received a `201 Created` response.

### 5. Verified the User

Confirmed the account appeared in Microsoft Entra ID and verified it using a Microsoft Graph query.

---

## Skills Demonstrated

- Microsoft Graph API
- REST API
- HTTP POST
- JSON
- OAuth 2.0
- Microsoft Entra ID
- User Provisioning
- Identity Lifecycle Management

---

## Business Outcome

Successfully automated user provisioning through Microsoft Graph, demonstrating how IAM administrators can create user accounts programmatically while following standard identity lifecycle processes.

---

## Key Learning Points

- POST requests create new resources.
- User provisioning can be automated through Microsoft Graph.
- Delegated permissions control API access.
- JSON request bodies define the properties of new directory objects.
- Verification ensures successful provisioning.

---

## Technologies Used

- Microsoft Entra ID
- Microsoft Graph Explorer
- Microsoft Graph API
- JSON
- OAuth 2.0

---

## Conclusion

This lab demonstrated how Microsoft Graph API can automate user provisioning in Microsoft Entra ID. Creating users through API requests is a common IAM task used in enterprise automation and identity lifecycle management.
