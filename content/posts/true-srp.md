---
title: "The True Single Responsibility Principle"
date: "2024-06-12"
description: "This post explores the first principle in SOLID."
categories: ["Software Design","SOLID"]
---



"A software module (class, function, package, ...) should have one and only one reason to change. One actor that is the source of change." - Uncle Bob

On the surface, this principle might remind you of the Linux philosophy: "do one thing and do it well." However, this is not Uncle Bob's interpretation. The challenge lies in defining what constitutes a single responsibility. According to Uncle Bob, a module's changes should be initiated by only one type of actor group. For example, a class that includes business logic for both administrators and managers clearly violates SRP. Therefore, SRP dictates that a module should be influenced by only one actor group, as each actor has well-defined business responsibilities. A user might belong to multiple actor groups, acting as an admin at one time and a regular user at another.

Let's go through an example to illustrate the Single Responsibility Principle and how to determine if a module adheres to it by identifying the actors that might request changes to its functions.

### Example Scenario

Consider a type called `UserManagement` in Go which handles various operations related to users in a system. Here's a sample implementation of the struct:

```go
package main

type UserManagement struct {}

func (um *UserManagement) CreateUser(userData map[string]string) {
    // Logic to create a user
}

func (um *UserManagement) DeleteUser(userID string) {
    // Logic to delete a user
}

func (um *UserManagement) UpdateUserDetails(userID string, newData map[string]string) {
    // Logic to update user details
}

func (um *UserManagement) GrantAdminPrivileges(userID string) {
    // Logic to grant admin privileges to a user
}

func (um *UserManagement) RevokeAdminPrivileges(userID string) {
    // Logic to revoke admin privileges from a user
}

func (um *UserManagement) GenerateUserReport(userID string) {
    // Logic to generate a report for a user
}
```

### Analyzing the type

To determine if this type adheres to SRP, we need to identify the different actors who might request changes to these methods.

1. **Create, Delete, and Update User Details**: These methods are likely to be requested by **User Management** teams or **HR** departments, whose primary responsibility is to manage user information.

2. **Grant and Revoke Admin Privileges**: These methods are likely to be requested by **System Administrators** or **Security** teams, who are responsible for managing user roles and permissions.

3. **Generate User Report**: This method might be requested by **Reporting** teams or **Auditors**, who need to generate and analyze user-related data.

### SRP Violation

From the analysis, it's clear that there are multiple distinct actor groups involved:

- **User Management/HR** for creating, deleting, and updating user details.
- **System Administrators/Security** for granting and revoking admin privileges.
- **Reporting/Auditors** for generating user reports.

This indicates that the `UserManagement` struct violates the SRP because it has multiple reasons to change, each driven by a different actor group.

### Refactoring to Adhere to SRP

To adhere to SRP, we should split this type into multiple types, each with a single responsibility:

```go
package main

type UserManager struct {}

func (um *UserManager) CreateUser(userData map[string]string) {
    // Logic to create a user
}

func (um *UserManager) DeleteUser(userID string) {
    // Logic to delete a user
}

func (um *UserManager) UpdateUserDetails(userID string, newData map[string]string) {
    // Logic to update user details
}

type AdminPrivilegesManager struct {}

func (apm *AdminPrivilegesManager) GrantAdminPrivileges(userID string) {
    // Logic to grant admin privileges to a user
}

func (apm *AdminPrivilegesManager) RevokeAdminPrivileges(userID string) {
    // Logic to revoke admin privileges from a user
}

type UserReportGenerator struct {}

func (urg *UserReportGenerator) GenerateUserReport(userID string) {
    // Logic to generate a report for a user
}
```

### Conclusion

By refactoring the `UserManagement` struct into `UserManager`, `AdminPrivilegesManager`, and `UserReportGenerator`, we now have each type with a single reason to change, aligned with a specific actor group. This adheres to the Single Responsibility Principle, making the system more maintainable and understandable.

When evaluating whether methods belong to a type and adhere to SRP, always ask: **"Who are the actors that will ask for changes to these functions?"** If the answer includes multiple actor groups with distinct responsibilities, it's a sign that the struct or module should be refactored to better align with SRP.