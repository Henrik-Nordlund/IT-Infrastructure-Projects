# Lab 5 – Microsoft Entra Conditional Access

## Project Overview

This lab explores identity and access security using Microsoft Entra Conditional Access, focusing on multi-factor authentication (MFA), legacy authentication restrictions, group-based access controls, and Microsoft Intune device compliance.

The project documents the assessment, configuration, and verification of Conditional Access policies in a Microsoft 365 E5 Developer environment.

A key consideration throughout the lab is protecting administrative access and maintaining the availability of the development tenant.

## Scenario

Nordlund Industries is a fictional organization that has implemented Microsoft Entra ID for identity and access management and Microsoft Intune for endpoint management. These implementations are documented in previous infrastructure projects.

Employees access Microsoft 365 services using different identities and devices. The IT security team has been tasked with designing additional access controls based on Zero Trust principles.

The objectives are to demonstrate that the author are able to:

- Configure an organization-wide MFA policy.
- Configure a policy to block legacy authentication.
- Apply device compliance requirements, in addition to MFA, to a particular security group for hardening the attack surface in a that vector.
- Integrate Conditional Access configuration with existing Intune device management from a previous lab (lab 3).
- Protect administrative access through account exclusions and emergency access arrangements.
- Examine policy configuration, authentication activity, and available verification methods.
- Evaluate the operational risks associated with introducing Conditional Access.
- Evaluate the ability to block sign-ins from geographical locations that might be compromised.

**Implementation approach:** Policies were initially created in the **Off** state while Security Defaults remained enabled. After verifying regular and emergency administrative access, Security Defaults were disabled and custom policies were deployed in stages. MFA and Engineering device compliance were subsequently enforced and verified using Microsoft Entra sign-in logs. The geographic block policy was activated after a successful What If evaluation. The legacy authentication policy was evaluated in Report-only using What If and subsequently enabled. Actual legacy-client blocking was not tested.

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
| Current baseline MFA policy | On |
| Current Engineering device compliance policy | On |
| Geographic restriction | On (`CA-GEO-Block-China`) |
| Legacy authentication policy | On (confirmed after Report-only testing) |

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

Three baseline Conditional Access policies were created manually in Microsoft Entra, initially in the **Off** state to prevent unintended disruption. A fourth geographic restriction policy was added later. After both administrative accounts had been tested, Security Defaults were disabled and policies were deployed in stages.

#### 3.1 Organization-Wide MFA Baseline

**Policy:** `CA-BASE-Require-MFA-AllUsers`

**Purpose:** Require MFA for organizational identities accessing cloud resources.

| Setting | Configuration |
|---|---|
| Users – Include | All users |
| Users – Exclude | Emergency Access Administrator |
| Target resources | All resources |
| Grant control | Require multifactor authentication |
| Policy state | **On** |

The regular Global Administrator was removed from the exclusions and is covered by the MFA baseline. The emergency account remains excluded as an administrative recovery precaution.

**Result:** Enforced and verified through successful sign-ins and Conditional Access logs for the regular Global Administrator, Johanna Lorenz, and Adele Vance.

**Configuration evidence (captured during initial setup; exclusion settings were subsequently updated):**

<img width="817" height="678" alt="Initial MFA policy exclusions, before administrator scope was updated" src="https://github.com/user-attachments/assets/377a6317-424f-49b1-a86c-8da866dfcc55" />
<img width="842" height="686" alt="MFA policy grant configuration" src="https://github.com/user-attachments/assets/9175b670-1928-4a94-afc6-5c08a0e8cbbd" />

#### 3.2 Block Legacy Authentication

**Policy:** `CA-BASE-Block-Legacy-Authentication`

**Purpose:** Block legacy authentication clients that cannot meet modern authentication requirements.

| Setting | Configuration |
|---|---|
| Users – Include | All users |
| Users – Exclude | Emergency Access Administrator |
| Target resources | All resources |
| Conditions – Client apps | Exchange ActiveSync clients and Other clients |
| Grant control | Block access |
| Session controls | Not configured |
| Policy state | **On** (enabled after Report-only evaluation) |

The regular Global Administrator was removed from the exclusions. What If evaluations subsequently confirmed that the policy matched simulated legacy-client sign-ins. No real legacy authentication blocking attempt was performed.

**Initial configuration evidence (captured before the exclusion was updated):**

<img width="1615" height="877" alt="Legacy authentication blocking policy configuration" src="https://github.com/user-attachments/assets/0e8ea210-a86d-4ce1-9a5a-1148f256e218" />

#### 3.3 Group-Based Device Compliance Requirement

**Policy:** `CA-LAB5-Require-Compliant-Device-Engineering`

**Purpose:** Require members of `SEC-Engineering` to use compliant devices when accessing Office 365 resources.

| Setting | Configuration |
|---|---|
| Users – Include | SEC-Engineering |
| Users – Exclude | Emergency Access Administrator |
| Target resources | Office 365 |
| Conditions | Not configured |
| Grant control | Require device to be marked as compliant |
| Session controls | Not configured |
| Policy state | **On** |

The existing `SEC-Engineering` security group was reused from previous labs. Its members are **Johanna Lorenz** and **Lidia Holloway**, both standard users with Microsoft 365 E5 Developer licenses.

The policy was evaluated in **Report-only** mode before being enabled. Once active, it enforced an additional requirement alongside the organization-wide MFA policy: successful MFA alone was insufficient if the device did not satisfy the Intune compliance requirement.

**Result:** Both denial from an unmanaged/noncompliant device and successful access from an Intune-managed compliant device were verified.

**Initial configuration evidence:**

<img width="1017" height="850" alt="Engineering policy group assignment" src="https://github.com/user-attachments/assets/53635511-611c-4f1f-950f-79debb7fb041" />
<img width="1611" height="862" alt="Engineering policy compliant device grant control" src="https://github.com/user-attachments/assets/a0e18815-cb2c-4a41-9fb2-997767c8885d" />

#### 3.4 Geographic Restriction Using Named Locations

**Named location:** `NL-Blocked-China`  
**Policy:** `CA-GEO-Block-China`

**Purpose:** Demonstrate a country-based Conditional Access restriction without weakening MFA or device compliance controls.

| Setting | Configuration |
|---|---|
| Named location type | Countries (IP) |
| Selected country | China |
| Mark as trusted location | No |
| Users – Include | All users |
| Users – Exclude | Emergency Access Administrator |
| Target resources | All resources |
| Network condition | Include `NL-Blocked-China` |
| Grant control | Block access |
| Policy state | **On** (activated after What If evaluation) |

A What If simulation was performed for **Johanna Lorenz**, targeting **Office 365 Exchange Online**, with **Country: China** and **IP address: `114.114.114.114`**. The results listed `CA-GEO-Block-China` under *Policies that will apply* with **Block access**, while the policy was in **Report-only**. The MFA baseline and Engineering compliance policy also matched.

**Result:** Policy match validated by simulation; policy subsequently enabled. **A real sign-in from China was not attempted**, so an actual geographic access denial was not observed. IP-based country identification can be imperfect, including when users connect through VPNs or other network intermediaries.

**Evidence pending upload:** `ca-geo-china-what-if.png` (the actual image must be added and linked before publication).

A separate trusted-headquarters location was considered but deliberately not implemented. Exempting users from device compliance solely because they are on an office network would weaken the intended Zero Trust controls; the geographic restriction already demonstrates practical Named Locations usage.

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

**Result:** The device was subsequently confirmed compliant, and its compliant/managed status was independently verified in the successful Conditional Access sign-in record for Johanna Lorenz.

<img width="1550" height="646" alt="Intune-device-compliant" src="https://github.com/user-attachments/assets/ead9ede6-7bf1-4305-855e-d7ea882045ba" />


### 5. Policy Inventory and Security Defaults Transition

At the end of initial policy configuration, all three policies were **Off** and Security Defaults remained enabled. The following screenshots record that earlier state; they do **not** represent the final enforced configuration.

<img width="1617" height="815" alt="Initial policy inventory showing three policies Off" src="https://github.com/user-attachments/assets/ca02d1d7-3b5d-47b7-bb9d-5ee93398c593" />

<img width="1530" height="866" alt="Security Defaults enabled before Conditional Access rollout" src="https://github.com/user-attachments/assets/8a34e726-bd06-4db9-a9a6-10373a7dc302" />

After administrative access was verified, Security Defaults were disabled to allow custom Conditional Access enforcement.

**Policy states at the latest confirmed checkpoints:**

| Policy | State | Verification |
|---|---|---|
| `CA-BASE-Require-MFA-AllUsers` | **On** | Successful MFA enforcement |
| `CA-BASE-Block-Legacy-Authentication` | **On** | What If matching verified in Report-only before activation; real legacy-client denial not tested |
| `CA-LAB5-Require-Compliant-Device-Engineering` | **On** | Report-only evaluation, denied access, and successful compliant-device access |
| `CA-GEO-Block-China` | **On** | What If match in Report-only before activation; no real geographic block test |

## Testing and Results

### 1. Baseline Sign-In Testing – November 22, 2026

Before enabling the custom policies, Johanna Lorenz (`SEC-Engineering` member) and Adele Vance (non-member) signed in to OfficeHome. Both successful baseline sign-ins showed **Single-factor authentication** and **Conditional Access: Not applied**.

These records establish the starting point and must not be confused with the later enforcement tests.

**Johanna Lorenz – baseline sign-in evidence:**

<img width="830" height="836" alt="Baseline Johanna sign-in details" src="https://github.com/user-attachments/assets/f279e026-1f03-49b3-bc74-9e94fc251721" />
<img width="841" height="741" alt="Baseline Johanna authentication details" src="https://github.com/user-attachments/assets/1b4293e6-f4a0-4790-9909-a59fc0178aa5" />
<img width="827" height="861" alt="Baseline Johanna interrupted sign-in" src="https://github.com/user-attachments/assets/08b27977-89bb-4593-a053-f3c618e6ecc2" />

Johanna initially registered Microsoft Authenticator and encountered an unsuccessful sign-in attempt. A later sign-in succeeded without a new MFA challenge. The earlier registration difficulty was not conclusively diagnosed. A separate interrupted event was associated with the browser's *Stay signed in?* flow.

**Adele Vance – baseline sign-in evidence:**

<img width="837" height="856" alt="Baseline Adele sign-in details" src="https://github.com/user-attachments/assets/c5ac16ab-dc97-417a-a32c-7f738cc00833" />

### 2. MFA Enforcement – November 23, 2026

The MFA baseline was enabled after administrative recovery access had been verified.

| Test identity | Observed behavior | Conditional Access result |
|---|---|---|
| Regular Global Administrator | Password and Microsoft Authenticator verification | MFA policy: **Success** |
| Johanna Lorenz | MFA challenge completed | MFA policy: **Success** |
| Adele Vance | Successful sign-in | MFA policy: **Success** |

The regular Global Administrator's sign-in log showed the MFA policy as **Success**. Johanna and Adele's later sign-in records likewise confirmed successful evaluation of the active MFA policy.

**Result: Passed.** The active policy was verified for an administrator and two standard users.

### 3. Engineering Device Compliance – Report-only Evaluation

The Engineering policy was placed in **Report-only** mode to evaluate its effect without enforcing a block.

Johanna Lorenz signed in from the regular computer, which did not satisfy the required device compliance condition for this sign-in.

| Item | Observation |
|---|---|
| User | Johanna Lorenz |
| Application | OfficeHome |
| Policy | `CA-LAB5-Require-Compliant-Device-Engineering` |
| Report-only result | **Report-only: Failure** |
| Access blocked by report-only policy | No |

**Result: Passed.** The log identified the compliance failure before enforcement was enabled.

### 4. Engineering Device Compliance – Access Denied

The Engineering policy was enabled and Johanna repeated the sign-in from the regular computer.

Johanna completed MFA, but the device did not satisfy the additional compliance requirement. Microsoft displayed **“You can't get there from here”** and the portal also reported **AADSTS53000** (*Device is not in required device state: compliant*).

The Conditional Access tab showed:

| Policy | Result |
|---|---|
| `CA-BASE-Require-MFA-AllUsers` | **Success** |
| `CA-LAB5-Require-Compliant-Device-Engineering` | **Failure** |
| `CA-BASE-Block-Legacy-Authentication` | Disabled |

**Result: Passed.** Successful MFA did not bypass the Engineering device compliance requirement. Access to the protected resource was denied.

### 5. Engineering Device Compliance – Access Granted

Johanna Lorenz then signed in using the Intune-managed Windows 11 virtual machine `WIN11-INTUNE-01`, previously configured in Lab 3. The virtual machine uses Windows Hello, and Microsoft Edge was used for the Microsoft 365 sign-in.

The sign-in succeeded. Microsoft Entra sign-in logs showed:

| Policy | Result |
|---|---|
| `CA-BASE-Require-MFA-AllUsers` | **Success** |
| `CA-LAB5-Require-Compliant-Device-Engineering` | **Success** |
| `CA-BASE-Block-Legacy-Authentication` | Disabled |

The **Device info** tab also showed:

| Device property | Observed value |
|---|---|
| Browser | Microsoft Edge |
| Managed | **Yes** |
| Compliant | **Yes** |
| Join type | Azure AD joined |
| Device ID | Present |
| Operating system label | Windows10 |

**Note:** The sign-in record displays `Windows10` as the operating system label, although the test VM runs Windows 11. This discrepancy does not change the independently recorded **Managed: Yes** and **Compliant: Yes** results.

**Result: Passed.** The same Engineering identity was granted access when the accessing device satisfied the compliance requirement. Both active policies returned **Success**.

### 6. Geographic Restriction – What If Evaluation

The What If tool evaluated a simulated sign-in for Johanna Lorenz to Office 365 Exchange Online, with country **China** and a matching Chinese IP address (`114.114.114.114`).

| Matching policy | Control | State during simulation |
|---|---|---|
| `CA-BASE-Require-MFA-AllUsers` | Require MFA | On |
| `CA-LAB5-Require-Compliant-Device-Engineering` | Require compliant device | On |
| `CA-GEO-Block-China` | Block access | Report-only |

**Result: Passed (simulation).** The country-based block policy matched the simulated conditions. It was subsequently set to **On**. This does not establish that a real sign-in from China was blocked.

### 7. Legacy Authentication – What If Evaluation

The policy `CA-BASE-Block-Legacy-Authentication` was placed in **Report-only** for testing. A What If evaluation used **Johanna Lorenz** and **Office 365 Exchange Online**, with **Other clients** selected. The result included the legacy authentication policy under *Policies that will apply*, with **Block access** and **Report-only**.

An additional What If evaluation was performed while working through the Exchange ActiveSync client variants. The user confirmed a matching result after correcting an issue with the What If Reset interface; the precise platform/client variant of that final result should be confirmed from the saved screenshot before it is labelled more specifically.

**Result: Passed (simulated policy matching).** This demonstrates matching configuration, **not** a real legacy-protocol authentication denial. The policy was subsequently changed to **On**. No actual legacy-client authentication denial was performed, so real-world blocking remains unverified.

**Evidence pending upload:** `ca-legacy-auth-other-clients-what-if.png` and any verified Exchange ActiveSync What If screenshot.

### 8. Verification Summary

| Verification | Result | Evidence |
|---|---|---|
| Initial tenant and policy inventory | Passed | No custom policies initially configured |
| Emergency administrator access | Passed | MFA sign-in and Entra administrative access |
| Security Defaults transition | Completed | Disabled before activating custom policies |
| MFA baseline enforcement | Passed | Policy Success for admin and standard users |
| Engineering Report-only evaluation | Passed | Report-only: Failure from noncompliant device |
| Engineering access from regular computer | Passed | MFA Success; device policy Failure; access denied |
| Engineering access from Intune-managed VM | Passed | MFA Success; device policy Success; access granted |
| Intune device state verification | Passed | Managed: Yes; Compliant: Yes |
| Legacy authentication policy matching | Passed (simulation) | What If: Other clients; Block access, Report-only |
| Legacy authentication policy activation | Completed | Policy changed to On after What If evaluation |
| Actual legacy-client access denial | Not tested | No real legacy sign-in attempt |
| Named Location and China policy | Passed (simulation) | Country-based policy matched in What If; subsequently enabled |
| Real sign-in from China | Not tested | No actual geographic denial recorded |

### 9. Testing Limitations

- **On** policies enforce their applicable grant controls; **Report-only** policies evaluate without enforcing; **Off** policies are not evaluated for access decisions.
- The Engineering test confirms compliant-device enforcement for the tested Office 365 access scenarios, not for every possible application or client.
- The MFA policy's **Success** status confirms its grant control was satisfied; a separate interactive MFA prompt is not necessarily required on every sign-in when a valid authentication claim already exists.
- Geographic and legacy-authentication policies matched simulated What If conditions; no real China-based sign-in or legacy-client denial was tested.
- The legacy-authentication policy is enabled, but a real legacy-client sign-in denial was not tested.
- The emergency administrator uses Microsoft Authenticator and is not a fully independent production-grade break-glass design.

## Security Considerations

### Administrative Lockout Prevention

The Microsoft 365 Developer tenant supports several infrastructure and cybersecurity labs. Before disabling Security Defaults, both the regular Global Administrator and the dedicated Emergency Access Administrator were verified as able to sign in and access Microsoft Entra administration.

The regular administrator was then included in the organization-wide MFA baseline. The emergency administrator remained excluded from the custom policies to provide a recovery path.

**Limitation:** The emergency account uses Microsoft Authenticator. A production emergency access design should use independently recoverable authentication methods, securely managed credentials, and monitoring.

### Controlled Transition from Security Defaults

Security Defaults and custom Conditional Access enforcement were not used simultaneously. Security Defaults were disabled before the custom policies were enabled.

The deployment used an incremental approach: configuration while Off, administrative access verification, MFA enforcement, Engineering Report-only evaluation, and Engineering enforcement testing. The legacy-authentication policy was evaluated in Report-only using What If and then enabled. No real legacy-client denial was performed.

### Layered Identity and Device Security

The Engineering scenario illustrates that MFA and device compliance are separate requirements. Johanna's MFA requirement succeeded on the regular computer, but access was denied because the device compliance requirement failed. On the Intune-managed compliant VM, both controls succeeded and access was granted.

### Geographic Controls and Zero Trust

A country-based Named Location can support geographic restrictions, but it is not proof of a user's physical location. Trusted-network exemptions were not introduced: the Engineering compliance requirement remains independent of the user's network location.

### Device Identification and Browser Support

Device-based Conditional Access depends on the sign-in conveying usable device identity and compliance information. The successful test used Microsoft Edge on an Entra-joined, Intune-managed Windows VM. The sign-in record explicitly confirmed both managed and compliant status.

## Lessons Learned

1. **Policy configuration is not enforcement.** The lab progressed from disabled policies to Report-only evaluation and verified access decisions.
2. **MFA alone may be insufficient.** Successful authentication did not override the Engineering compliance requirement.
3. **Report-only mode is valuable before rollout.** It showed that the regular computer would fail the device requirement without immediately blocking access.
4. **Sign-in logs provide decisive evidence.** They distinguished MFA Success, compliance Failure, and compliance Success across the test scenarios.
5. **Intune compliance is an operational dependency.** An inactive virtual machine initially appeared noncompliant until device check-in and reporting were restored.
6. **Device identity matters.** A compliant endpoint must be identified correctly during sign-in for device-based Conditional Access to succeed.
7. **Administrative recovery needs deliberate planning.** Emergency access was verified before changing the tenant's baseline identity protection.
8. **Geographic conditions are additional signals, not substitutes for device trust.** The China policy was tested without exempting headquarters users from MFA or device compliance.
9. **What If evaluates policy applicability, not real-world enforcement.** The geographic and legacy-client tests must be described as simulations.

## Project Status

**Implementation and testing completed, subject to final documentation checks.**

**Completed:** Four Conditional Access policies enabled; initial security assessment; emergency administrator configuration and verification; four Conditional Access policy configurations; controlled Security Defaults transition; enforced MFA verification; Engineering Report-only evaluation; real access denial from an unmanaged/noncompliant device; real access grant from an Intune-managed compliant VM; Intune/sign-in-log analysis; China Named Location and What If validation; legacy-authentication What If evaluation.

**Before marking the lab fully complete:**

1. Upload and link the November 23 MFA, Engineering denial/grant, China What If, legacy-authentication What If, and final policy-inventory screenshots. Do not create placeholder image URLs.
2. Review screenshots for credentials, recovery details, personally identifying tenant data, and public IP addresses before publication.
3. Check that earlier screenshots are clearly identified as historical states, not the final policy configuration.

**Evidence standard:** Policy configuration, What If matching, sign-in-log results, and observed access decisions are reported separately. No production-wide effectiveness claim is made from this limited developer-tenant test.
