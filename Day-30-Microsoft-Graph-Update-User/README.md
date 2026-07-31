Day-30-Microsoft-Graph-Update-User/
│
├── README.md
├── 01-Find-User.png
├── 02-PATCH-Request.png
├── 03-PATCH-Success.png
├── 04-Verification.png
└── 05-Entra-Verification.png

# Day 30 – Microsoft Graph API: Update a User

## Objective

Learn how to update Microsoft Entra ID user properties using Microsoft Graph API and the HTTP PATCH method.

---

## Scenario

HR notified the IAM team that David Miller transferred from Marketing to Sales. The user's profile needed to be updated to reflect the organizational change.

---

## Tasks Performed

1. Retrieved the user's Object ID using Microsoft Graph.
2. Changed the HTTP method to PATCH.
3. Updated the display name and office location.
4. Verified the successful API response.
5. Confirmed the updated values using Microsoft Graph and the Microsoft Entra admin center.

---

## Skills Demonstrated

- Microsoft Graph API
- HTTP PATCH
- JSON
- REST APIs
- User Lifecycle Management
- Microsoft Entra ID
- Identity Administration

---

## Business Outcome

Successfully updated a Microsoft Entra ID user through Microsoft Graph API, demonstrating how IAM teams automate user lifecycle changes while maintaining accurate identity information.

---

## Key Learning Points

- PATCH updates existing resources.
- Object IDs uniquely identify directory objects.
- A successful PATCH request commonly returns `204 No Content`.
- Verification should be performed after every identity change.

---

## Technologies Used

- Microsoft Entra ID
- Microsoft Graph Explorer
- Microsoft Graph API
- JSON
- OAuth 2.0

---

## Conclusion

This lab demonstrated how Microsoft Graph API can be used to modify existing user accounts in Microsoft Entra ID. Updating user attributes programmatically is a common IAM task that supports employee transfers, promotions, and profile maintenance.
