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
- 16 developer users and associated administrative units (created in lab 1) 

## Scenario

Nordlund Industries has a central IT-department, but not all IT-admininistrators should have access to the entire organizations digital environment. The company therefore would to implement a model where administrators will receive the permissions they need to perform their duties but not more than that.

For instance, if we create an IT Helpdesk Administrator for an office he/she should be able to manage user accounts within his/her AU only.

## RBAC design
| Administrative function | Entra-role             | Scope        |
| ----------------------- | ---------------------- | ------------ |
| Helpdesk                | Helpdesk Administrator | AU-West      |
| Identity administration | User Administrator     | AU-Central   |
| Security administration | Security Administrator | Tenant-wide* |


## Users (16 fictional corporate users created in lab 1)

- Adele Vance
- Alex Wilber
- Diego Siciliani
- Grady Archie
- Henrietta Mueller
- Isaiah Langer
- Johanna Lorenz
- Joni Sherman
- Lee Gu
- Lidia Holloway
- Lynne Robbins
- Megan Bowen
- Miriam Graham
- Nestor Wilke
- Patti Fernandez
- Pradeep Gupta

### Administrative Units (AU) 

#### AU-West
- Adele Vance
- Alex Wilber
- Miriam Graham
- Nestor Wilke
<img width="1522" height="467" alt="AU-West users" src="https://github.com/user-attachments/assets/c4bacdba-70a4-46d2-8f8c-57ebd842d69f" />

#### AU-Central
- Grady Archie
- Lee Gu
- Lidia Holloway
- Lynne Robbins
<img width="1497" height="475" alt="AU-central users" src="https://github.com/user-attachments/assets/a5c4fd26-8b79-4c27-8ee3-c346c5e3b552" />

These AU:s along with other AU:s, and the users assigned to them, were created in Lab 1. See Lab 1 for further reference.

# Implementation

## Create administrative test accounts 

I create a new user for the helpdesk role in AU-West, for the management of user accounts in AU-central as well as a security administrator for the entire organization, respectively. 

| Person          | Job title                 | Purpose i Lab 2        |
| --------------- | ------------------------- | ---------------------- |
| **Henrik Berg** | IT Support Technician     | Helpdesk Administrator |
| **Anna Lind**   | IT Administrator          | User Administrator     |
| **Erik Holm**   | IT Security Administrator | Security Administrator |

<img width="1242" height="717" alt="Henrik Berg" src="https://github.com/user-attachments/assets/52f22f50-00fb-49df-b238-485a7a7695d5" />

<img width="1222" height="662" alt="Anna Lind" src="https://github.com/user-attachments/assets/ef6b5709-89af-4488-bf70-0effbecb9504" />

<img width="1237" height="626" alt="Erik Holm" src="https://github.com/user-attachments/assets/0d7d3bc8-99d1-4519-87d5-ffa841ac08c7" />


## Test 1 – Allowed action

Logging in as Henrik Berg
<img width="290" height="56" alt="inloggad Henrik Berg" src="https://github.com/user-attachments/assets/0af28714-7b63-4c61-bd13-65ea7cca1a2a" />

Reseetting password for user Adele Vance, another member in AU-West.
<img width="1582" height="395" alt="reset password AU-west" src="https://github.com/user-attachments/assets/42ec5d74-8233-4da9-9f8c-7cdd3bf6ae29" />

Result: Allowed -> Passed

## Test 2 – Restricted action
Attempting to reset password for Lynne Robbins, located within AU-central.  
<img width="1597" height="345" alt="cannot reset password" src="https://github.com/user-attachments/assets/3cf18733-f1f7-4883-9a18-61d26358aa7b" />

Result: Denied - Passed

## Test 3 – Role scope verification
<img width="1461" height="327" alt="helpdesk AU-west" src="https://github.com/user-attachments/assets/11a4ec5d-8f3a-45f6-9351-5add322d3dad" />

**PowerShell:**
Step 1. Find the Entra ID for Henrik Berg
```powershell
Get-MgUser -UserId "henrik.berg@1s1mkr.onmicrosoft.com" |
    Select-Object Id,DisplayName,UserPrincipalName
```
Result:   

DisplayName       : Henrik Berg    
UserPrincipalName : henrik.berg@1s1mkr.onmicrosoft.com    
Id                : add50200-7217-4d6d-b8eb-84fe0dce19df    

Step 2. Find his RBAC roleassignment.  
```powershell
Get-MgRoleManagementDirectoryRoleAssignment `
    -Filter "principalId eq 'add50200-7217-4d6d-b8eb-84fe0dce19df'" |
    Format-List PrincipalId,RoleDefinitionId,DirectoryScopeId
```
Result:  

PrincipalId      : add50200-7217-4d6d-b8eb-84fe0dce19df  
RoleDefinitionId : 729827e3-9c14-49f7-bb1b-9608f156bbb8     
DirectoryScopeId : /administrativeUnits/75d6878f-63ad-435d-b85b-544d94af4f5d   

Step 3. Verify the rolename.
```powershell
Get-MgRoleManagementDirectoryRoleDefinition `
    -UnifiedRoleDefinitionId "729827e3-9c14-49f7-bb1b-9608f156bbb8" |
    Select-Object Id,DisplayName
```
Result:

Id: 729827e3-9c14-49f7-bb1b-9608f156bbb8   
DisplayName: Helpdesk Administrator     

Step 4. Verify which admninistrative unit (AU)
```powershell
Get-MgDirectoryAdministrativeUnit `
    -AdministrativeUnitId "75d6878f-63ad-435d-b85b-544d94af4f5d" |
    Select-Object Id,DisplayName
```
Result:  

Id: 75d6878f-63ad-435d-b85b-544d94af4f5d      
DisplayName: AU-West    

Conclusion: Henrik Berg is Helpdesk Administrator with scope: AU-West

<img width="1437" height="332" alt="User admin Anna Lind" src="https://github.com/user-attachments/assets/f15ff09a-3184-4644-a800-1776ff361a02" />

<img width="1101" height="405" alt="image" src="https://github.com/user-attachments/assets/b70fae8c-8714-4986-b65b-2da475f013bb" />


## Test 4 – Privilege comparison




## Validation

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
- Global Administrator should not be used for routine administrative tasks.This is an example of Seperation of Duties in effect.

