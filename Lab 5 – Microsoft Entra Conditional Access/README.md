# Lab 5 – Microsoft Entra Conditional Access

**Status:** In progress

## Project Overview

This lab explores identity and access security using Microsoft Entra Conditional Access, including MFA enforcement, device compliance, location-based access controls, and policy evaluation.

The lab will document policy configuration, testing, verification, and troubleshooting in a Microsoft 365 E5 Developer environment.


## Scenario

Nordlund Industries has implemented Microsoft Entra ID for identity management and Microsoft Intune for endpoint management. Further information about these efforts are available in previous projects. Users access Microsoft 365 services from different devices and network locations.

The IT security team has been tasked with strengthening access controls using Microsoft Entra Conditional Access, following Zero Trust principles.

The objective is to evaluate how access policies can:

- Require multi-factor authentication (MFA) for selected users.
- Restrict access based on device compliance and network location.
- Apply different access requirements to specific users and groups.
- Protect administrative access while maintaining emergency access.
- Be evaluated and troubleshot using Report-only mode, What If, and sign-in logs.

Policies will initially be tested in Report-only mode and scoped to designated test accounts to minimize the risk of unintended access restrictions.

### Emergency Access Account Configuration

A dedicated emergency access account was created to reduce the risk of administrative lockout during Conditional Access configuration.

The account was configured with:

- A separate Microsoft Entra user identity.
- A directly assigned, permanently active Global Administrator role.
- Microsoft Authenticator for multi-factor authentication.
- Verified access to Microsoft Entra Admin Center.

**Verification:** A successful interactive sign-in with an MFA requirement was recorded in Microsoft Entra sign-in logs. Administrative portal access was also verified.

**Result:** Passed.

**Limitation:** The account uses Microsoft Authenticator rather than an independent hardware security key. It is suitable for this lab but does not represent a fully resilient production emergency access configuration.
