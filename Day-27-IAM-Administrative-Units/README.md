Day-27-IAM-Administrative-Units/
│
├── README.md
├── 01-Administrative-Units.png
├── 02-New-Administrative-Unit.png
├── 03-Members.png
├── 04-Roles-and-Administrators.png
├── 05-Overview.png
└── 06-Final-Verification.png

# Day 27 – IAM Ticket #005: Administrative Units and Delegated Administration

## Objective

Learn how Administrative Units can be used to delegate administrative responsibilities to specific departments or business units within Microsoft Entra ID.

---

## Scenario

The HR department requested delegated administration so that HR IT staff could manage only HR users without receiving tenant-wide administrative permissions.

---

## Lab Environment

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Microsoft Entra ID Free Developer Tenant

---

## Tasks Performed

### 1. Reviewed Administrative Units

Navigated to the Administrative Units section within Microsoft Entra ID.

### 2. Attempted Administrative Unit Creation

Attempted to create an Administrative Unit named **HR-Administrative-Unit**.

### 3. Reviewed Membership

Reviewed how users can be assigned to Administrative Units.

### 4. Reviewed Administrative Roles

Explored how administrative roles can be scoped to an Administrative Unit instead of the entire tenant.

### 5. Documented Tenant Capabilities

If Administrative Units were unavailable, documented the feature limitations of the Microsoft Entra ID Free developer tenant.

---

## Skills Demonstrated

- Delegated Administration
- Administrative Units
- Microsoft Entra ID
- Role-Based Access Control (RBAC)
- Identity Governance Concepts
- Least Privilege

---

## Business Outcome

Reviewed how Administrative Units help organizations delegate administrative responsibilities while limiting administrative scope to specific departments or organizational units.

---

## Key Learning Points

- Administrative Units reduce administrative scope.
- Scoped administrators follow the principle of least privilege.
- Administrative Units are commonly used in large enterprises.
- Delegated administration improves operational security.

---

## Technologies Used

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Role-Based Access Control (RBAC)

---

## Conclusion

This lab introduced Administrative Units as a method for implementing delegated administration in Microsoft Entra ID. Even when full functionality is unavailable in a development tenant, understanding how Administrative Units support least privilege and organizational separation is valuable for enterprise IAM environments.
