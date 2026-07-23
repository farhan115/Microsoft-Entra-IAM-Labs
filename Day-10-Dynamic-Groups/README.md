# Day 10 – Microsoft Entra ID Dynamic Groups

## Objective

Explore Dynamic Groups in Microsoft Entra ID and understand how rule-based membership automates user management.

---

## Lab Environment

- Microsoft Entra ID
- Administrator Account
- Microsoft Entra ID Free Tenant

---

## Tasks Performed

- Opened Microsoft Entra Groups.
- Attempted to create a Dynamic Security Group.
- Reviewed available membership types.
- Verified tenant licensing capabilities.

---

## Lab Result

The tenant only displayed the **Assigned** membership type.

The **Dynamic User** and **Dynamic Device** options were unavailable.

---

## Technical Analysis

Dynamic Groups require **Microsoft Entra ID Premium P1 or P2**.

Because the tenant is using Microsoft Entra ID Free, rule-based membership cannot be configured.

This is a common licensing consideration when planning identity lifecycle automation.

---

## Skills Learned

- Dynamic Groups
- Group Membership Types
- Identity Lifecycle Management
- Microsoft Entra Licensing
- Group Administration

---

## Screenshots

### Groups Overview

(Add screenshot)

### New Group Configuration

(Add screenshot showing only "Assigned" membership type)

---

## Conclusion

Explored Microsoft Entra Dynamic Groups and identified the Microsoft Entra ID Premium licensing requirement for rule-based group membership.
