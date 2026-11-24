# Lab 6 – Microsoft Entra Privileged Identity Management

## Overview

This lab explores the implementation of Microsoft Entra Privileged
Identity Management (PIM) to manage and control privileged access
within a Microsoft-based enterprise environment.

The lab builds upon the identity infrastructure, administrative
roles and security controls established in previous labs within
the fictional company Nordlund Industries. See for instance the lab focused on RBAC and Least Privilege as well the one on Conditional Access for reference. 

The primary focus is to reduce standing administrative privileges
and implement time-limited access to privileged roles to increase control and minimize risk for over-extension.

## Business Scenario

Nordlund Industries has established a corporate identity structure,
role-based access control (RBAC), endpoint management and
Conditional Access policies.

However, permanent administrative role assignments may expose
the organisation to unnecessary security risks.

To reduce these risks, the organisation intends to introduce
Privileged Identity Management, allowing administrators to
activate privileged roles when required rather than maintaining
permanent active access.

## Objectives

- Understand the difference between eligible and active role
  assignments.
- Reduce standing administrative privileges.
- Implement Just-in-Time (JIT) privileged access.
- Configure role activation requirements.
- Evaluate approval-based role activation.
- Verify the activation and expiration of privileged access.
- Monitor privileged role assignments and activation events
  through audit logs.
- Maintain emergency administrative access.

## Lab Environment

- Fictional organisation: Nordlund Industries
- Microsoft 365 E5 Developer tenant
- Microsoft Entra ID
- Microsoft Entra Privileged Identity Management
- Existing administrative test accounts
- Existing Conditional Access policies

## Planned Implementation

### 1. Review Existing Administrative Role Assignments

Review the administrative roles and assignments established
in previous labs.

Identify suitable role assignments for conversion from
permanently active to eligible assignments.

### 2. Configure Eligible Role Assignments

Configure selected administrative accounts with eligible
role assignments using Microsoft Entra PIM.

### 3. Configure Role Activation Requirements

Evaluate and configure relevant activation requirements,
including:

- Multifactor authentication (MFA)
- Justification
- Maximum activation duration
- Approval requirements

### 4. Test Privileged Role Activation

Evaluate administrative access before, during and after
role activation.

Verify that privileged operations are only available
while the required role is active.

### 5. Test Approval-Based Activation

Evaluate a scenario in which privileged role activation
requires approval from an authorised approver.

### 6. Monitoring and Audit Logs

Review available PIM audit history and Microsoft Entra
audit logs to verify privileged role assignments,
activation requests and activation events.

### 7. Emergency Access

Review the existing emergency access configuration to
ensure that administrative recovery remains possible
independently of the PIM activation process.
