# Lab 1 – Microsoft Entra ID: Corporate Identity Structure

## Project overview
This lab focuses on building a fictional corporate identity structure in Microsoft Entra ID. It covers user and group management, Microsoft 365 groups, Administrative Units, and basic identity administration across different departments.

## Environment

- Microsoft Entra ID
- Microsoft 365 Developer Program
- Microsoft Entra ID Premium P2
- 16 available developer users

## Scenario

Nordlund Industries is a fictional company with several departments: HR, Sales, Marketing, R&D, Engineering, Manufactoring, and Finance.

The objective of this lab is to build a corporate identity environment in Microsoft Entra ID. The 16 fictional users are assigned to appropriate departments and job titles, and organized according to their roles and responsibilities within the company.

The lab includes creating and managing security groups for access and administration, as well as Microsoft 365 groups for collaboration. Users are assigned to the appropriate groups based on their department and role.

The scenario is designed to demonstrate practical identity administration, including user management, group-based organization, and the use of Microsoft Entra ID to manage identities in a small corporate environment.


## Implementation

### Users (16 fictional corporate users + 1 personal administrative account)
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
<img width="822" height="707" alt="Userlist" src="https://github.com/user-attachments/assets/1bd8fb72-7794-49ef-a4ca-431db0767964" />

## Planned Structure
| User              | Job title           | Department           | Manager         | State/Country  |
| ----------------- | ------------------- | -------------------- | --------------- | -------------- |
| Adele Vance       | Retail Manager      | Sales                | Miriam Graham   | Washington     |
| Alex Wilber       | Marketing Assistant | Marketing            | Miriam Graham   | California     |
| Diego Siciliani   | HR Manager          | HR                   | Nestor Wilke    | Alabama        |
| Grady Archie      | Designer            | R&D                  | Lee Gu          | Illinois       |
| Henrietta Mueller | Developer           | R&D                  | Lee Gu          | Florida        |
| Isaiah Langer     | Sales Rep           | Sales                | Miriam Graham   | Oklahoma       |
| Johanna Lorenz    | Senior Engineer     | Engineering          | Lee Gu          | Kentucky       |
| Joni Sherman      | Paralegal           | Legal                | Nestor Wilke    | North Carolina |
| Lee Gu            | Director            | Manufacturing        | Patti Fernandez | Kansas         |
| Lidia Holloway    | Product Manager     | Engineering          | Lee Gu          | Oklahoma       |
| Lynne Robbins     | Planner             | Sales                | Miriam Graham   | Oklahoma       |
| Megan Bowen       | Marketing Manager   | Marketing            | Miriam Graham   | Pennsylvania   |
| Miriam Graham     | Director            | Sales & Marketing    | Patti Fernandez | California     |
| Nestor Wilke      | Director            | Operations           | Patti Fernandez | Washington     |
| Patti Fernandez   | President           | Executive Management | —               | Kentucky       |
| Pradeep Gupta     | Accountant          | Finance              | Nestor Wilke    | Egypt          |


### Security Groups
Intital state
<img width="1537" height="472" alt="initial state" src="https://github.com/user-attachments/assets/db024d85-564f-429f-b38c-0cd42e325c54" />

SEC- groups are used for access control and permissions. These are the security groups I created for the users.
<img width="1460" height="737" alt="Security Groups" src="https://github.com/user-attachments/assets/c8bfedc6-662b-44d2-93f7-5a2ca92137b7" />



### Microsoft 365 Groups
M365- groups are used for collaboration. These are the M365 groups I created for the users.
<img width="1507" height="607" alt="M365 grupper" src="https://github.com/user-attachments/assets/01baadac-1386-450b-8bc2-6c041b418ebe" />


### Administrative Units
<img width="1417" height="491" alt="administrative units" src="https://github.com/user-attachments/assets/21d39102-7546-44d4-a79a-3416461f20d6" />



## Validation - Test Results

| Test                              | Expected result                                                        | Result |
| --------------------------------- | ---------------------------------------------------------------------- | ------ |
| User structure                    | User attributes match the planned organizational structure.            | Passed |
| Security group membership         | Users are members of the appropriate security groups.                  | Passed |
| Multiple group membership         | Miriam Graham is a member of both `SEC-Sales` and `SEC-Marketing`.     | Passed |
| Microsoft 365 collaboration group | `M365-Sales-Marketing` contains members from both Sales and Marketing. | Passed |
| Administrative Units              | Users are assigned to the appropriate Administrative Unit.             | Passed |

### Test 1 – User Structure

Miriam Graham was selected as a test user. Her job title, department, state and manager were verified against the planned organizational structure.  
<img width="1312" height="742" alt="Miriam Graham user information" src="https://github.com/user-attachments/assets/841d589f-9a94-43f1-8e3d-17ec67ae4ad5" />
**Result:** Passed

**PowerShell:**

```powershell
Update-MgUser -UserId "miriam.graham@1s1mkr.onmicrosoft.com" `
    -JobTitle "Director" `
    -Department "Sales & Marketing" `
    -State "CA"
```

### Test 2 – Security Group Membership
The membership of `SEC-Sales` and `SEC-Marketing` was checked to verify that users were assigned to the appropriate groups.

<img width="1462" height="537" alt="sales group" src="https://github.com/user-attachments/assets/f721bb76-0d84-406f-a3f3-f5296f626064" />
<img width="1267" height="566" alt="Marketing group" src="https://github.com/user-attachments/assets/f551b990-220e-44a7-959d-907d83c95d24" />

**Result:** Passed

**PowerShell:**

```powershell
$user = Get-MgUser -Filter "displayName eq 'Isaiah Langer'"
$group = Get-MgGroup -Filter "displayName eq 'SEC-Sales'"

New-MgGroupMember -GroupId $group.Id -DirectoryObjectId $user.Id
```

### Test 3 – Multiple Group Membership

Miriam Graham is assigned as the owner of both SEC-Sales and SEC-Marketing. This demonstrates that a user can belong to multiple security groups based on their responsibilities.

<img width="1246" height="377" alt="owner sales" src="https://github.com/user-attachments/assets/8885ba77-76dd-4061-91a6-b380a45ebb83" />
<img width="1251" height="405" alt="owner marketing" src="https://github.com/user-attachments/assets/88d8744a-7429-44d9-be99-6c80312e9f98" /> 
 
 **Result:** Passed

**PowerShell:**
```powershell
$user = Get-MgUser -Filter "displayName eq 'Miriam Graham'"

$group = Get-MgGroup -Filter "displayName eq 'SEC-Sales'"
New-MgGroupOwnerByRef -GroupId $group.Id -OdataId "https://graph.microsoft.com/v1.0/users/$($user.Id)"

$group = Get-MgGroup -Filter "displayName eq 'SEC-Marketing'"
New-MgGroupOwnerByRef -GroupId $group.Id -OdataId "https://graph.microsoft.com/v1.0/users/$($user.Id)"
```

### Test 4 – Microsoft 365 Collaboration Group

The membership of `M365-Sales-Marketing` was verified. The group contains users from both Sales and Marketing for collaboration purposes.

**Result:** Passed

### Test 5 – Administrative Units

The membership of the Administrative Units was checked. Users were verified against their assigned regional Administrative Unit.

**Result:** Passed



### Test 6 – Block sign-in
Expected:
Result:

## Lessons Learned

...

## References

- Microsoft Learn
