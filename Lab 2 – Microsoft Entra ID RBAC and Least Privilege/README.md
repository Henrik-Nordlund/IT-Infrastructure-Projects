# Lab 2 – Microsoft Entra ID RBAC and Least Privilege

## Project overview

This lab focuses on how administrative rights can be assigned with Microsoft Entra RBAC and how the principle of least privilege works.

Focus:

- Entra built-in roles
- Role assignment
- Administrative Units
- Scoped administration
- Separation of duties
- Verification of allowed and not allowed administrative tasks

## Tools
- Microsoft Entra ID
- Microsoft 365 Developer Program
- Microsoft Entra ID Premium P2
- 16 fictional corporate users and associated Administrative Units (created in lab 1) 

## Scenario

Nordlund Industries has a central IT department, but not all administrators should have access to the entire organization's digital environment. The company therefore wants to implement a model where administrators will receive the permissions they need to perform their duties but not more than that.

For instance, if we create an IT Helpdesk Administrator for an office, they should be able to manage user accounts within their AU only.

## RBAC design
| Administrative function | Entra-role             | Scope        |
| ----------------------- | ---------------------- | ------------ |
| Helpdesk                | Helpdesk Administrator | AU-West      |
| Identity administration | User Administrator     | AU-Central   |
| Security administration | Security Administrator | Tenant-wide* |

*Security Administrator is assigned at tenant scope in this lab. The role is intended to provide organization-wide security administration.

## Users 
The 16 fictional corporate users created in Lab 1 are used as test accounts in this lab.

## Administrative Units (AU) 
These Administrative Units were created in Lab 1. However, not all AUs created in Lab 1 are used in this lab.

### AU-West
- Adele Vance
- Alex Wilber
- Miriam Graham
- Nestor Wilke
<img width="1522" height="467" alt="AU-West users" src="https://github.com/user-attachments/assets/c4bacdba-70a4-46d2-8f8c-57ebd842d69f" />

### AU-Central
- Grady Archie
- Lee Gu
- Lidia Holloway
- Lynne Robbins
<img width="1497" height="475" alt="AU-central users" src="https://github.com/user-attachments/assets/a5c4fd26-8b79-4c27-8ee3-c346c5e3b552" />

See Lab 1 for further reference.

# Implementation

## Create administrative test accounts

I created three test accounts for the administrative roles used in this lab: a Helpdesk Administrator scoped to AU-West, a User Administrator scoped to AU-Central, and a Security Administrator assigned at tenant scope. 

| Person          | Job title                 | Purpose in Lab 2        |
| --------------- | ------------------------- | ---------------------- |
| **Henrik Berg** | IT Support Technician     | Helpdesk Administrator |
| **Anna Lind**   | IT Administrator          | User Administrator     |
| **Erik Holm**   | IT Security Administrator | Security Administrator |

<img width="1242" height="717" alt="Henrik Berg" src="https://github.com/user-attachments/assets/52f22f50-00fb-49df-b238-485a7a7695d5" />

<img width="1222" height="662" alt="Anna Lind" src="https://github.com/user-attachments/assets/ef6b5709-89af-4488-bf70-0effbecb9504" />

<img width="1237" height="626" alt="Erik Holm" src="https://github.com/user-attachments/assets/0d7d3bc8-99d1-4519-87d5-ffa841ac08c7" />

## Testing and Results

### Test 1 – Allowed action

I logged in as Henrik Berg, who was assigned the Helpdesk Administrator role scoped to AU-West.   

<img width="290" height="56" alt="inloggad Henrik Berg" src="https://github.com/user-attachments/assets/0af28714-7b63-4c61-bd13-65ea7cca1a2a" />

I reset the password for Adele Vance, another user assigned to AU-West.
<img width="1582" height="395" alt="reset password AU-west" src="https://github.com/user-attachments/assets/42ec5d74-8233-4da9-9f8c-7cdd3bf6ae29" />

**Expected result: Allowed → Passed**

### Test 2 – Restricted action
While still logged in as Henrik Berg, I attempted to reset the password for Lynne Robbins, who is assigned to AU-Central, a different Administrative Unit.   

<img width="1597" height="345" alt="cannot reset password" src="https://github.com/user-attachments/assets/3cf18733-f1f7-4883-9a18-61d26358aa7b" />

**Expected result: Denied → Passed**

### Test 3 – Role scope verification
Here, the RBAC assignments were verified using Microsoft Graph PowerShell to confirm the assigned role and administrative scope for each test account.

| Test account    | Entra role                | Scope       |
| --------------- | ------------------------- | ----------- |
| **Henrik Berg** | Helpdesk Administrator    | AU-West     |
| **Anna Lind**   | User Administrator        | AU-Centra   |
| **Erik Holm**   | Security Administrator    | Tenant-wide |


** Henrik Berg – Helpdesk Administrator ** 

<img width="1461" height="327" alt="helpdesk AU-west" src="https://github.com/user-attachments/assets/11a4ec5d-8f3a-45f6-9351-5add322d3dad" />

I queried the role assignment for Henrik Berg using Microsoft Graph PowerShell.

The steps are:
- Find his Entra ID using his email address.
- Use that ID to find his role assignment, which returns machine-readable IDs.
- Use the role definition ID to verify that it corresponds to the intended role, in this case Helpdesk Administrator.
- Finally, resolve the directory scope to identify the Administrative Unit.

**PowerShell:**  

```powershell
Get-MgRoleManagementDirectoryRoleAssignment `
    -Filter "principalId eq 'add50200-7217-4d6d-b8eb-84fe0dce19df'" |
    Format-List PrincipalId,RoleDefinitionId,DirectoryScopeId
```

Result:

PrincipalId      : add50200-7217-4d6d-b8eb-84fe0dce19df   
RoleDefinitionId : 729827e3-9c14-49f7-bb1b-9608f156bbb8    
DirectoryScopeId : /administrativeUnits/75d6878f-63ad-435d-b85b-544d94af4f5d   

For readability, I chose to include only one of those steps here – Step 2, finding his role assignment. Reading long sequences of PowerShell commands on a GitHub repo one after another becomes a pain for a human after a while...

**Expected result: Henrik Berg is Helpdesk Administrator with scope: AU-West → Passed**

<img width="1437" height="332" alt="User admin Anna Lind" src="https://github.com/user-attachments/assets/f15ff09a-3184-4644-a800-1776ff361a02" />

**PowerShell:**

Step 1. Identify the admnin account for Anna Lind         
```powershell
Get-MgUser -UserId "anna.lind@1s1mkr.onmicrosoft.com" |
    Select-Object Id,DisplayName,UserPrincipalName
```
Result:    
Id: 1520c1b3-321a-4498-a04e-febf9f0da685
DisplayName:  Anna Lind    
UserPrincipalName: anna.lind@1s1mkr.onmicrosoft.com

Step 2. Control RBAC role assignment for Anna.

```powershell
Get-MgRoleManagementDirectoryRoleAssignment `
    -Filter "principalId eq '1520c1b3-321a-4498-a04e-febf9f0da685'" |
    Format-List PrincipalId,RoleDefinitionId,DirectoryScopeId
```

Result:    
PrincipalId      : 1520c1b3-321a-4498-a04e-febf9f0da685   
RoleDefinitionId : fe930be7-5e62-47db-91af-98c3a49a38b1   
DirectoryScopeId : /administrativeUnits/961d8447-dc46-4dc3-871e-3be19f7f4bc3    

Step 3. Control RBAC id for Anna.

```powershell
Get-MgRoleManagementDirectoryRoleAssignment `
    -Filter "principalId eq '1520c1b3-321a-4498-a04e-febf9f0da685'" |
    Format-List PrincipalId,RoleDefinitionId,DirectoryScopeId
```

Result:   
Id:  fe930be7-5e62-47db-91af-98c3a49a38b1     
DisplayName:  User Administrator        

Step 4. Control scope for Anna.

```powershell
Get-MgDirectoryAdministrativeUnit `
    -AdministrativeUnitId "961d8447-dc46-4dc3-871e-3be19f7f4bc3" |
    Select-Object Id,DisplayName
```

Result:
Id: 961d8447-dc46-4dc3-871e-3be19f7f4bc3     
DisplayName:  AU-central

Conclusion: Henrik Berg is a User Administrator with scope: AU-central


<img width="1101" height="405" alt="image" src="https://github.com/user-attachments/assets/b70fae8c-8714-4986-b65b-2da475f013bb" />

**PowerShell:**

Step 1. Identify the id for Erik Holm         
```powershell
Get-MgUser -UserId "erik.holm@1s1mkr.onmicrosoft.com" |
    Select-Object Id,DisplayName,UserPrincipalName
```

Result:   
Id: 50284f0e-8886-4fc1-8efa-842e5a8364d2
DisplayName: Erik Holm    
UserPrincipalName: erik.holm@1s1mkr.onmicrosoft.com    

Step 2. Identify the scope for Erik Holm         
```powershell
Get-MgRoleManagementDirectoryRoleAssignment `
    -Filter "principalId eq '50284f0e-8886-4fc1-8efa-842e5a8364d2'" |
    Format-List PrincipalId,RoleDefinitionId,DirectoryScopeId
 ```   

Result:  
PrincipalId      : 50284f0e-8886-4fc1-8efa-842e5a8364d2    
RoleDefinitionId : 194ae4cb-b126-40b2-bd5b-6091b380977d    
DirectoryScopeId : /   

-> /. Scope is global.

Step 3. Identify the role name for Erik Holm         
```powershell
Get-MgRoleManagementDirectoryRoleDefinition `
    -UnifiedRoleDefinitionId "194ae4cb-b126-40b2-bd5b-6091b380977d" |
    Select-Object Id,DisplayName
```

Result:
Id: 194ae4cb-b126-40b2-bd5b-6091b380977d                                   
DisplayName: Security Administrator     

Conclusion: Erik Holm is the security administrator, and his scope is the entire tenant.

### Test 4 – Privilege comparison

| Test                                                    | Expected result | Result |
| ------------------------------------------------------- | --------------- | ------ |
| Admin can manage user in AU-West                        | Allowed         | Passed |
| Admin attempts to manage user outside AU-West           | Denied          | Passed |
| RBAC assignment has correct scope                       | AU-West         | Passed |
| Global Administrator retains unrestricted access        | Allowed         | Passed |
| Helpdesk account has no tenant-wide administrative role | Confirmed       | Passed |

## Lessons learned

- RBAC allows administrative permissions to be assigned according to job responsibilities.
- Administrative Units can be used to limit the scope of delegated administration.
- Least privilege reduces the impact of compromised or misused administrative accounts.
- Administrative role assignments should be reviewed regularly.
- Global Administrator should not be used for routine administrative tasks. This is an example of Separation of Duties in effect.
- Bit tedious to use powershell instead of GUI for single users.

