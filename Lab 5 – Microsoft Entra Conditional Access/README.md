# Lab 5 – Microsoft Entra Conditional Access

**Status:** In progress

## Project Overview

This lab explores identity and access security using Microsoft Entra Conditional Access, focusing on multi-factor authentication (MFA), legacy authentication restrictions, group-based access controls, and Microsoft Intune device compliance.

The project documents the assessment, configuration, and verification of Conditional Access policies in a Microsoft 365 E5 Developer environment.

A key consideration throughout the lab is protecting administrative access and maintaining the availability of the development tenant.

## Scenario

Nordlund Industries is a fictional organization that has implemented Microsoft Entra ID for identity and access management and Microsoft Intune for endpoint management. These implementations are documented in previous infrastructure projects.

Employees access Microsoft 365 services using different identities and devices. The IT security team has been tasked with designing additional access controls based on Zero Trust principles.

The objectives are to:

- Configure an organization-wide MFA policy.
- Configure a policy to block legacy authentication.
- Apply device compliance requirements to a designated security group.
- Integrate Conditional Access configuration with existing Intune device management.
- Protect administrative access through account exclusions and emergency access arrangements.
- Examine policy configuration, authentication activity, and available verification methods.
- Evaluate the operational risks associated with introducing Conditional Access.

**Implementation approach:** Conditional Access policies are initially created in the **Off** state. The existing Security Defaults configuration remains enabled to preserve the tenant's baseline identity protection.

This approach allows policy configuration to be documented without introducing untested access restrictions. Actual Conditional Access enforcement is outside the scope of the implementation completed so far.

## Lab Environment

| Component | Configuration |
|---|---|
| Organization | Nordlund Industries (fictional) |
| Environment | Microsoft 365 E5 Developer tenant |
| Identity platform | Microsoft Entra ID |
| Endpoint management | Microsoft Intune |
| Identity licensing | Microsoft Entra ID P1 and P2 capabilities |
| User and group structure | Existing identities and security groups from previous labs |
| Target security group | SEC-Engineering |
| Engineering test users | Johanna Lorenz and Lidia Holloway |
| Reference device | WIN11-INTUNE-01 (Windows 11 virtual machine) |
| Initial security configuration | Security Defaults enabled |
| Initial Conditional Access configuration | No policies configured |

## Implementation

### 1. Initial Security Assessment

Before configuring Conditional Access, the tenant's existing identity security configuration was reviewed.

The assessment established that:

- No custom Conditional Access policies had been configured.
- Microsoft Entra Security Defaults were enabled.
- Security Defaults provided baseline identity protection, including MFA-related requirements.
- Custom Conditional Access policies could not be enabled while Security Defaults remained active.
- A controlled migration would be necessary before enforcing custom Conditional Access policies.

<img width="1270" height="660" alt="Initial Conditional Access policy inventory" src="https://github.com/user-attachments/assets/8fa3f5b1-33b4-4e2d-aeb1-6e474fb9b33f" />

The initial policy inventory was documented before making changes.

### 2. Emergency Access Account Configuration

A dedicated emergency access account was created to reduce the risk of administrative lockout during Conditional Access administration.

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

<img width="1057" height="668" alt="Emergency access account creation" src="https://github.com/user-attachments/assets/b1644e17-634a-4efb-b7ba-378b2ee60b9d" />

<img width="1258" height="585" alt="Permanent Global Administrator role assignment" src="https://github.com/user-attachments/assets/25edeb5b-f2fd-488f-ab69-faccce557d36" />

<img width="1321" height="568" alt="Emergency access authentication method" src="https://github.com/user-attachments/assets/79e981ef-7ebd-4c6d-a580-735d10aec039" />

<img width="1361" height="274" alt="Successful emergency administrator sign-in with MFA" src="https://github.com/user-attachments/assets/4856e328-ac54-4bdf-bc32-20d36947234e" />

**Limitation:** The account uses Microsoft Authenticator rather than an independent hardware security key. This provides a separate administrative identity but does not represent a fully resilient production emergency access configuration.

A production deployment should include independently recoverable emergency access arrangements, appropriate authentication methods, secure credential storage, and monitoring.

### 3. Conditional Access Policy Configuration

Three Conditional Access policies were created manually in Microsoft Entra.

All policies were saved in the **Off** state to avoid affecting authentication or access to the development tenant.

#### 3.1 Organization-Wide MFA Baseline

**Policy:** `CA-BASE-Require-MFA-AllUsers`

**Purpose:** Define a baseline MFA requirement for organizational identities accessing cloud resources.

| Setting | Configuration |
|---|---|
| Users – Include | All users |
| Users – Exclude | Tenant administration account and Emergency Access Administrator |
| Target resources | All resources |
| Grant control | Require multifactor authentication |
| Policy state | Off |

The policy was designed to require MFA for organizational users while excluding the two administrative identities used to maintain access to the development environment.

The exclusions were reviewed before the policy was saved.

**Result:** Policy created and verified in the Off state. MFA enforcement by this policy was not tested.

**Evidence to include:**

<img width="817" height="678" alt="ca-baseline-mfa-exclusions" src="https://github.com/user-attachments/assets/377a6317-424f-49b1-a86c-8da866dfcc55" />   
<img width="842" height="686" alt="ca-baseline-mfa-configuration" src="https://github.com/user-attachments/assets/9175b670-1928-4a94-afc6-5c08a0e8cbbd" />

#### 3.2 Block Legacy Authentication

**Policy:** `CA-BASE-Block-Legacy-Authentication`

**Purpose:** Define a restriction against legacy authentication clients that do not support modern authentication controls.

| Setting | Configuration |
|---|---|
| Users – Include | All users |
| Users – Exclude | Tenant administration account and Emergency Access Administrator |
| Target resources | All resources |
| Conditions – Client apps | Exchange ActiveSync clients and Other clients |
| Grant control | Block access |
| Session controls | Not configured |
| Policy state | Off |

The policy targets legacy authentication client categories without restricting modern authentication clients.

**Result:** Policy created and saved in the Off state. Actual blocking of legacy authentication was not tested.

<img width="1615" height="877" alt="ca-baseline-block-legacy-authentication" src="https://github.com/user-attachments/assets/0e8ea210-a86d-4ce1-9a5a-1148f256e218" />


#### 3.3 Group-Based Device Compliance Requirement

**Policy:** `CA-LAB5-Require-Compliant-Device-Engineering`

**Purpose:** Require members of the Engineering security group to use compliant devices when accessing designated Microsoft 365 resources.

| Setting | Configuration |
|---|---|
| Users – Include | SEC-Engineering |
| Users – Exclude | Tenant administration account and Emergency Access Administrator |
| Target resources | Office 365 |
| Conditions | Not configured |
| Grant control | Require device to be marked as compliant |
| Session controls | Not configured |
| Policy state | Off |

The existing security group `SEC-Engineering` was selected rather than creating a separate group solely for this lab.

The group contains two standard user accounts:

- Johanna Lorenz
- Lidia Holloway

Both accounts have Microsoft 365 E5 Developer licenses and no administrative roles.

The policy demonstrates how access requirements can be assigned through existing security group membership.

Users outside the target group are not included solely because they share other group memberships with Engineering users.

**Result:** Policy created and saved in the Off state. Device-based access enforcement was not tested.

<img width="1017" height="850" alt="ca-engineering-device-assignment" src="https://github.com/user-attachments/assets/53635511-611c-4f1f-950f-79debb7fb041" />  

<img width="1611" height="862" alt="ca-engineering-device-grant" src="https://github.com/user-attachments/assets/a0e18815-cb2c-4a41-9fb2-997767c8885d" />   <img width="1615" height="877" alt="ca-baseline-block-legacy-authentication" src="https://github.com/user-attachments/assets/a8055503-18d7-42be-8a28-e6acc7ac4adc" />


### 4. Microsoft Intune Device Compliance Verification

The Engineering policy was designed to use device compliance information supplied by Microsoft Intune.

The reference environment includes two Windows 11 virtual machines enrolled in Intune during Lab 3 – Intune Endpoint Management.

For this lab, `WIN11-INTUNE-01` was examined.

Initially, Intune reported the device as **Noncompliant**. Investigation of the Default Device Compliance Policy identified the following result:

| Compliance setting | Initial result |
|---|---|
| Has a compliance policy assigned | Compliant |
| Is active | Not compliant |
| Enrolled user exists | Compliant |

The virtual machine had been inactive for an extended period, preventing regular device check-ins.

After the Hyper-V virtual machine was started and communication with Intune resumed, the default compliance policy reported **Compliant**.

A subsequent evaluation of the Windows 11 compliance policy initially displayed errors for Firewall and Antivirus, together with an outdated Microsoft Defender security intelligence result. The reported issues cleared following a refresh.

This exercise demonstrated the relationship between device activity, compliance reporting, and the device state used by Conditional Access.

**Result:** Intune device compliance reporting was investigated and refreshed. The final device compliance state should be documented separately from the Conditional Access policy's enforcement state.

<img width="1550" height="646" alt="Intune-device-compliant" src="https://github.com/user-attachments/assets/ead9ede6-7bf1-4305-855e-d7ea882045ba" />


### 5. Final Policy Inventory and Security Defaults Verification

Following policy configuration, the Conditional Access inventory was reviewed.

Three user-created policies were present:

| Policy | State |
|---|---|
| CA-BASE-Require-MFA-AllUsers | Off |
| CA-BASE-Block-Legacy-Authentication | Off |
| CA-LAB5-Require-Compliant-Device-Engineering | Off |

The inventory confirmed that all three policies had been successfully saved without enabling enforcement.

<img width="1617" height="815" alt="ca-policy-overview" src="https://github.com/user-attachments/assets/ca02d1d7-3b5d-47b7-bb9d-5ee93398c593" />

Microsoft Entra tenant properties were also reviewed to confirm that Security Defaults remained enabled.

The portal displayed:

**Your organization is protected by security defaults.**

<img width="1530" height="866" alt="security-defaults-enabled" src="https://github.com/user-attachments/assets/8a34e726-bd06-4db9-a9a6-10373a7dc302" />

No transition from Security Defaults to enforced custom Conditional Access policies was performed.

## Testing and Results

### Verification Summary

| Verification | Result | Notes |
|---|---|---|
| Initial Conditional Access inventory | Passed | No policies configured at baseline |
| Emergency administrator account creation | Passed | Dedicated account created |
| Global Administrator role assignment | Passed | Direct, permanently active assignment |
| Emergency administrator interactive sign-in | Passed | MFA requirement observed |
| Emergency administrator portal access | Passed | Administrative access verified |
| Organization-wide MFA policy configuration | Passed | Created and saved as Off |
| Legacy authentication blocking policy configuration | Passed | Created and saved as Off |
| Engineering device compliance policy configuration | Passed | Created and saved as Off |
| Intune device inactivity investigation | Passed | Inactivity identified as the initial compliance issue |
| Security Defaults verification | Passed | Existing baseline protection remained enabled |
| Custom Conditional Access enforcement | Not tested | All three policies remained Off |
| Group-based access restriction | Not tested | No enforced Engineering policy |
| Legacy authentication blocking | Not tested | No enforced blocking policy |

### Authentication and Sign-In Log Verification

*Pending – Interactive sign-in tests using standard organizational accounts and review of Microsoft Entra sign-in logs.*

### Testing Limitations

Conditional Access policies support three operating states:

- **On:** Policy conditions are evaluated and applicable controls are enforced.
- **Report-only:** Policy impact can be evaluated without enforcing access controls.
- **Off:** The policy is disabled and is not evaluated for access decisions.

Because Security Defaults remained enabled, the custom Conditional Access policies were not activated or placed into Report-only mode.

Consequently, the successful creation of a policy must not be interpreted as proof that its access controls were enforced.

Interactive authentication tests and sign-in logs can provide evidence of the tenant's authentication behavior, but they do not demonstrate enforcement by policies that remain Off.

## Security Considerations

### Administrative Lockout Prevention

The development tenant is a shared dependency for several infrastructure and cybersecurity projects. Losing administrative access would affect future lab activities.

To minimize this risk:

- A separate emergency administrative identity was created and tested.
- The tenant administration account was kept separate from the fictional organization's user structure.
- Both administrative identities were excluded from the three experimental Conditional Access policies.
- Policies were saved in the Off state.
- Security Defaults remained enabled throughout configuration.

These measures reduced the risk of administrative lockout during the lab.

However, exclusions from individual Conditional Access policies do not override other authentication requirements, including Security Defaults.

### Security Defaults and Conditional Access

Security Defaults provides a baseline set of identity security protections.

Custom Conditional Access policies allow more granular control, but transitioning from Security Defaults requires planning to avoid weakening protection or interrupting administrative access.

A production migration should include:

- An inventory of existing authentication requirements.
- Appropriate MFA coverage for organizational users and administrators.
- Legacy authentication restrictions.
- Emergency access procedures and recovery testing.
- Staged policy rollout and monitoring.
- Validation of the replacement controls before relying on them.

In this lab, maintaining the existing security configuration was prioritized over demonstrating enforced custom access restrictions.

## Lessons Learned

### 1. Conditional Access requires operational risk management

A technically valid policy can still introduce unacceptable operational risk if it affects critical administrative identities.

Policy scope, exclusions, deployment state, and recovery arrangements must be reviewed before enforcement.

### 2. Security groups provide reusable policy boundaries

The existing `SEC-Engineering` security group was reused to define a targeted access requirement.

This demonstrates how identity structures established in earlier labs can support more granular access management.

### 3. Device compliance is a separate security signal

A user may have valid credentials and satisfy MFA requirements while accessing Microsoft 365 from a device that does not meet organizational compliance requirements.

Conditional Access can combine identity and device signals when determining whether access should be granted.

### 4. Device inactivity can affect compliance reporting

The Intune investigation showed how a previously managed device could become noncompliant after an extended period without checking in.

Restoring device activity allowed compliance information to be refreshed.

### 5. Policy configuration is not the same as enforcement

The three policies were successfully created and documented, but remained disabled.

Their configuration demonstrates familiarity with Conditional Access administration; it does not establish that the intended access restrictions were successfully enforced.

Distinguishing configuration evidence from enforcement evidence is essential for accurate security documentation.

## Project Status

**In progress**

Completed:

- Initial identity security assessment.
- Emergency access account configuration and verification.
- Three Conditional Access policy configurations.
- Intune compliance investigation.
- Policy inventory and Security Defaults verification.

Remaining:

- Standard user sign-in tests.
- Sign-in log analysis.
- Final screenshot review and documentation cleanup.
- Optional Named Locations exercise.

  <img width="1530" height="866" alt="security-defaults-enabled-" src="https://github.com/user-attachments/assets/c3edd947-fc18-47f2-b09f-f1a27d9f8377" />

