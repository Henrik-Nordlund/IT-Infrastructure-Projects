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

