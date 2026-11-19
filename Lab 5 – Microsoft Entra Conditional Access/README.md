# Lab 5 – Microsoft Entra Conditional Access

**Status:** In progress

## Project Overview

This lab explores identity and access security using Microsoft Entra Conditional Access, including MFA enforcement, device compliance, location-based access controls, and policy evaluation.

The lab documents the configuration, testing, verification, and troubleshooting of identity-based access controls in a Microsoft 365 E5 Developer environment.

## Scenario

Nordlund Industries has implemented Microsoft Entra ID for identity management and Microsoft Intune for endpoint management. These implementations are documented in previous infrastructure projects.

Users access Microsoft 365 services from different devices and network locations.

The IT security team has been tasked with strengthening access controls using Microsoft Entra Conditional Access, following Zero Trust principles.

The objective is to evaluate how access policies can:

- Require multi-factor authentication (MFA) for selected users.
- Restrict access based on device compliance and network location.
- Apply different access requirements to specific users and groups.
- Protect administrative access while maintaining emergency access.
- Be evaluated and troubleshot using Report-only mode, What If, and sign-in logs.

Policies will initially be tested in Report-only mode and scoped to designated test accounts to minimize the risk of unintended access restrictions.

## Lab Environment

| Component | Configuration |
|---|---|
| Organization | Nordlund Industries (fictional) |
| Environment | Microsoft 365 E5 Developer tenant |
| Identity platform | Microsoft Entra ID |
| Endpoint management | Microsoft Intune |
| Identity licensing | Microsoft Entra ID P1 and P2 capabilities |
| Initial security configuration | Security Defaults enabled |
| Conditional Access policies | None configured at baseline |

## Implementation

### 1. Initial Security Assessment

Before configuring Conditional Access, the existing identity security configuration was reviewed.

The assessment established that:

- No Conditional Access policies had been configured.
- Microsoft Entra Security Defaults were enabled.
- Security Defaults provided baseline identity protection, including MFA requirements.
- A controlled transition to Conditional Access would be necessary before custom policies could be enforced.

<img width="1270" height="660" alt="conditional-access-initial-state" src="https://github.com/user-attachments/assets/8fa3f5b1-33b4-4e2d-aeb1-6e474fb9b33f" />

The initial Conditional Access policy inventory was documented before making changes.

### 2. Emergency Access Account Configuration

A dedicated emergency access account was created to reduce the risk of administrative lockout during Conditional Access configuration.

The account was configured with:

- A separate Microsoft Entra user identity.
- A directly assigned, permanently active Global Administrator role.
- Microsoft Authenticator for multi-factor authentication.
- Verified access to Microsoft Entra Admin Center.

The following checks were performed:

| Verification | Result |
|---|---|
| Emergency access account created and enabled | Passed |
| Global Administrator role directly assigned | Passed |
| Role assignment permanently active | Passed |
| Microsoft Authenticator registered | Passed |
| Successful interactive sign-in with MFA requirement | Passed |
| Administrative portal access | Passed |

**Result:** Passed – The emergency access account was configured and its administrative access verified.

<img width="1057" height="668" alt="emergency-access-account-ny" src="https://github.com/user-attachments/assets/b1644e17-634a-4efb-b7ba-378b2ee60b9d" />   

<img width="1258" height="585" alt="emergency-access-permanent-global-admin" src="https://github.com/user-attachments/assets/25edeb5b-f2fd-488f-ab69-faccce557d36" />   

<img width="1321" height="568" alt="emergency-access-authentication" src="https://github.com/user-attachments/assets/79e981ef-7ebd-4c6d-a580-735d10aec039" />  

<img width="1361" height="274" alt="emergency-access-signed-in-mfa" src="https://github.com/user-attachments/assets/4856e328-ac54-4bdf-bc32-20d36947234e" />



**Limitation:** The account uses Microsoft Authenticator rather than an independent hardware security key. This is sufficient for the current lab exercises but does not represent a fully resilient production emergency access configuration. The account must also be explicitly excluded from applicable Conditional Access policies when these are introduced.

### 3. Conditional Access Policy Configuration

*Pending – Policy configuration and testing have not yet started.*

## Testing and Results

Detailed policy verification results will be documented as the lab progresses.

## Lessons Learned

*To be completed after implementation and testing.*
