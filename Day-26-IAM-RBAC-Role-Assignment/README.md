Day-26-IAM-RBAC-Role-Assignment/
│
├── README.md
├── 01-Roles-and-Admins.png
├── 02-User-Administrator.png
├── 03-Role-Details.png
├── 04-Add-Assignment.png
├── 05-Role-Assigned.png
└── 06-User-Roles.png

# Day 26 – IAM Ticket #004: Administrative Role Assignment (RBAC)

## Objective

Simulate a real-world IAM request by assigning a built-in Microsoft Entra administrative role using Role-Based Access Control (RBAC).

---

## Scenario

The IT Manager requested that a support engineer be granted the **User Administrator** role to manage users without providing full Global Administrator privileges.

---

## Lab Environment

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Microsoft Entra ID Free Developer Tenant

---

## Tasks Performed

### 1. Reviewed Built-in Roles

Accessed the **Roles & administrators** section and reviewed available built-in roles.

### 2. Opened the User Administrator Role

Reviewed the role description and permissions to understand its responsibilities.

### 3. Assigned the Role

Assigned the **User Administrator** role to a test user.

### 4. Verified the Assignment

Confirmed that the role assignment appeared under the role's assignments and on the user's assigned roles page.

---

## Skills Demonstrated

- Role-Based Access Control (RBAC)
- Microsoft Entra Built-in Roles
- Administrative Role Assignment
- Least Privilege Principle
- Microsoft Entra Administration

---

## Business Outcome

Successfully delegated user management responsibilities using the **User Administrator** role while following the principle of least privilege. This approach allows routine administrative tasks without granting full tenant-wide administrative access.

---

## Key Learning Points

- Use RBAC to delegate administrative tasks securely.
- Assign only the permissions required for a user's responsibilities.
- Verify role assignments after configuration.
- Understand the difference between Global Administrator and User Administrator.

---

## Technologies Used

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Role-Based Access Control (RBAC)

---

## Conclusion

This lab demonstrated how Microsoft Entra ID uses Role-Based Access Control (RBAC) to delegate administrative responsibilities securely. Assigning built-in roles based on least privilege is a fundamental IAM practice used in enterprise environments.
