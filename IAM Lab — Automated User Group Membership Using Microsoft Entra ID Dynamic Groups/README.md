# IAM Lab — Automated Group Membership Using Microsoft Entra ID Dynamic Groups

**Lab Status:** Successfully Configured and Verified  
**Platform:** Microsoft Entra ID  
**Environment:** Farhan Muntasir IT Labs  
**Technology:** Dynamic Security Groups, Attribute-Based Membership, Identity and Access Management (IAM)

## 1. Lab Overview

This hands-on lab demonstrates how to automate security group membership in Microsoft Entra ID using dynamic membership rules.

Instead of manually adding users to security groups, Microsoft Entra ID automatically evaluates user attributes and manages group membership based on predefined conditions.

In this lab, I created a dynamic security group named **Bellevue**, configured a city-based membership rule, and verified that a qualifying user appeared in the group.

## 2. Business Scenario

An organization has employees working across multiple cities.

The IT department needs to automatically organize employees based on their office location without manually maintaining security group memberships.

**Business Requirement:**

Create a security group named `Bellevue` that automatically includes users whose City attribute is set to Bellevue.

**Solution:**

Implement a Microsoft Entra ID Dynamic User Security Group with an attribute-based membership rule.

## 3. Lab Objectives

- Create a dynamic security group in Microsoft Entra ID.
- Configure automatic membership based on the City attribute.
- Implement a dynamic membership rule.
- Verify group membership.
- Understand how attribute changes affect membership.
- Reduce manual identity administration.

## 4. Technologies Used

- Microsoft Entra ID
- Microsoft Entra Admin Center
- Microsoft Entra ID P2
- Dynamic Security Groups
- Dynamic Membership Rules
- Attribute-Based Group Membership
- Identity and Access Management (IAM)

## 5. Lab Implementation

### Step 1: Access Microsoft Entra Admin Center

Signed in to the Microsoft Entra admin center:

https://entra.microsoft.com

Navigated to:

**Entra ID → Groups → All groups → New group**

### Step 2: Create a Dynamic Security Group

Configured the following settings:

| Setting | Configuration |
|---|---|
| Group type | Security |
| Group name | Bellevue |
| Group description | Dynamic security group for Bellevue users |
| Membership type | Dynamic User |
| Microsoft Entra roles can be assigned | No |

Created the security group to support automatic user membership based on location.

### Step 3: Configure the Dynamic Membership Rule

Opened:

**Bellevue → Dynamic membership rules**

Configured the following conditions:

| Property | Operator | Value |
|---|---|---|
| city | Equals | Bellevue |

**Dynamic membership rule:**

```text
(user.city -eq "Bellevue")
```

**Rule Explanation:**

- `user.city` identifies the user's City attribute.
- `-eq` is the equality comparison operator.
- `"Bellevue"` is the required attribute value.

Any user whose City attribute matches Bellevue qualifies for membership.

### Step 4: Save the Membership Rule

Saved the dynamic membership configuration.

Microsoft Entra ID evaluates user attributes and updates group membership automatically.

Membership changes are processed asynchronously and may not appear immediately.

### Step 5: Verify Group Membership

Navigated to:

**Entra ID → Groups → All groups → Bellevue → Members**

Confirmed that the following user appeared:

| User | Membership |
|---|---|
| Bianca Pisani | Confirmed |

The Bellevue group displayed one member during verification.

### Step 6: Verify the Dynamic Rule

Opened:

**Bellevue → Dynamic membership rules**

Verified that the saved rule matched the business requirement:

```text
(user.city -eq "Bellevue")
```

The rule builder displayed:

- Property: city
- Operator: Equals
- Value: Bellevue

**Verification Result: Passed**

The dynamic rule was correctly configured, and Bianca Pisani appeared in the group's membership list.

## 6. Lab Evidence

Screenshots captured during the lab:

**Screenshot 1 — Bellevue Group Membership**

Shows Bianca Pisani listed as a member of the Bellevue security group.

**Screenshot 2 — Dynamic Membership Rule Configuration**

Shows the configured City attribute, Equals operator, Bellevue value, and rule syntax.

Recommended repository structure:

```text
Entra-ID-Dynamic-Group-Bellevue/
├── README.md
└── Screenshots/
    ├── 01-Bellevue-Group-Members.png
    └── 02-Dynamic-Membership-Rule.png
```

## 7. Security and IAM Concepts

### Dynamic Group Membership

Automatically manages group membership based on user attributes.

### Attribute-Based Membership

Uses identity properties such as City, Department, or Job Title to determine membership.

### Identity Lifecycle Automation

Reduces manual administrative work when employees join, move between departments or locations, or leave an organization.

### Consistent Access Management

Dynamic groups can support consistent access assignments when combined with appropriate authorization policies.

### Reduced Administrative Overhead

Eliminates the need to manually add or remove users when their qualifying attributes change.

## 8. Real-World Applications

Organizations can use dynamic groups to:

- Organize employees by office location.
- Manage department-based group membership.
- Support group-based application access.
- Automate Microsoft 365 license assignments.
- Support onboarding and internal transfers.
- Maintain location-specific security groups.

**Example:**

If an employee's City changes from Houston to Bellevue, Microsoft Entra ID can automatically add the employee to the Bellevue group after membership processing.

If the employee's City no longer matches Bellevue, the employee no longer qualifies for membership under this rule.

## 9. Lab Results

| Objective | Result |
|---|---|
| Create Bellevue security group | Completed |
| Configure Dynamic User membership | Completed |
| Create City-based membership rule | Completed |
| Verify Bianca Pisani membership | Completed |
| Verify saved rule syntax | Completed |
| Test automatic membership changes | Not yet tested |

## 10. Key Takeaways

Through this hands-on lab, I gained practical experience in:

- Creating dynamic security groups.
- Configuring attribute-based membership rules.
- Understanding Microsoft Entra ID user attributes.
- Verifying group membership.
- Implementing identity automation.
- Applying dynamic groups to real-world IAM scenarios.

## 11. Conclusion

Successfully configured and verified a Microsoft Entra ID dynamic security group using a City-based membership rule.

This lab demonstrates practical knowledge of identity administration, attribute-based group membership, and IAM automation.

**Final Result: Configuration and initial membership verification completed successfully.**

---

**Author:** Farhan Muntasir  
**GitHub:** https://github.com/farhan115

**Focus Areas:** Identity and Access Management | Microsoft Entra ID | Dynamic Groups | Identity Automation | Cloud Security
