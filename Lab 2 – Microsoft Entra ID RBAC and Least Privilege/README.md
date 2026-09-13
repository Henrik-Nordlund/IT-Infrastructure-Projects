# Lab 2 – Microsoft Entra ID RBAC and Least Privilege

## Project overview

This lab focuses on how administrative rights can be assigned with Microsoft Entra RBAC and how the principle of least privilege works.

Focus:

Entra built-in roles
rollassignment
Administrative Units
scoped administration
separation of duties
verification of allowed and not allowed administrative tasks

## Tools
- Microsoft Entra ID
- Microsoft 365 Developer Program
- Microsoft Entra ID Premium P2
- 16 developer users and associated administrative units (created in lab 1) 

## Scenario

Nordlund Industries has a central IT-department, but not all IT-admininistrators should have access to the entire organizations digital environment. The company therefore would to implement a model where administrators will receive the permissions they need to perform their duties but not more than that.

For instance, if we create an IT Helpdesk Administrator for an office he/she should be able to manage user accounts within his/her AU only.

# Implementation
Users (16 fictional corporate users created in lab 1)

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
