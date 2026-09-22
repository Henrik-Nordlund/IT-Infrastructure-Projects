
# Lab 3 – Microsoft Intune: Endpoint Management

## Project overview
This lab focuses on illustrating on how an administrator can manage Windows devices centrally, apply configuration and security requirements and make sure that the managed units follows the policies created by the organization.

My goal here is to demonstrate that I can:
- manage Windows devices with Intune
- use groups for policy assignment
- create and apply Configuration Profiles
- create Compliance Policies
- illustrate that I know the difference between configuration and compliance
- control whether a device is compliant or not
- generally use Intune as part of an organization's security work.

## Environment

- Microsoft 365 Developer Program
- Microsoft Intune
- 2 test users in the Microsoft 365 Developer environment - Adele Vance and Alex Wilber
- 2 Virtual machines (Hyper-V)

## Scenario
Nordlund Industries wants to centrally manage its Windows devices using Microsoft Intune. The goal is to establish a basic endpoint management setup where configuration, security requirements and device compliance can be managed through Microsoft 365.

The lab uses a small test environment to demonstrate how an administrator can enroll and manage Windows devices, assign policies to users or groups, and verify the resulting device state.



## Implementation

### 1. Intune Access

Logged in to Microsoft Intune using the Microsoft 365 Developer Program
tenant and verified that Intune is available to the administrator account.

### 2. Windows Test Devices

Two Windows virtual machines were created using Hyper-V Manager and given the names listed below.

| Device          | Operating System | Purpose                      |     
| WIN11-INTUNE-01 | Windows 11       | Primary Intune test device   |     
| WIN11-INTUNE-02 | Windows 11       | Secondary Intune test device |

The virtual machines will now be used as managed endpoint devices in the
Intune environment. Adele Vance has been assigned the role as local administrator in WIN11-INTUNE-01 and Alex Wilber has the same function in WIN11-INTUNE-02.

The Windows 11 installation ISO was downloaded from the official Microsoft website (https://www.microsoft.com/sv-se/software-download/windows11).
A detailed description of the procedure of setting up the test environment with virtual machines will not be provided here - out of scope.

The host computer running Hyper-V is not itself part of the Intune test
environment.

### Test Status

| Test | Status |
|---|---|
| Enroll a Windows device in Intune | Passed |
| Verify that enrolled devices appear in Intune | Passed |
| Assign a Configuration Profile | Passed |
| Verify that the configured settings are applied | Passed |
| Assign a Compliance Policy | Passed |
| Verify the compliance state of the devices | Passed |
| Introduce a non-compliant condition | Passed |
| Verify that Intune detects the non-compliant state | Passed |
| Remediate the condition | Passed |
| Verify the resulting compliance state | Passed |

### 3. Device Enrollment

WIN11-INTUNE-01 and WIN11-INTUNE-02 were enrolled in Microsoft Intune
and joined to Microsoft Entra ID.

Both devices were successfully registered as Intune-managed Windows
devices and appeared in the Intune admin center.

### 4. Configuration Profile

A Windows Firewall configuration profile was created using the
Settings Catalog.

The following settings were configured:

- Enable Public Network Firewall
- Enable Log Dropped Packets

The profile was assigned to the test device group containing
WIN11-INTUNE-01 and WIN11-INTUNE-02.

Intune reported successful deployment of both settings to both devices.

### 5. Configuration Verification

The configuration was verified on WIN11-INTUNE-02 using PowerShell.

The local PersistentStore initially reported:

| Profile | LogBlocked |
|---|---|
| Domain | False |
| Private | False |
| Public | False |

After the Intune policy was applied, the MDM policy store reported:

| Profile | LogBlocked |
|---|---|
| Domain | NotConfigured |
| Private | NotConfigured |
| Public | True |

The effective ActiveStore configuration also reported:

| Profile | LogBlocked |
|---|---|
| Domain | False |
| Private | False |
| Public | True |

This confirmed that the Intune configuration was applied to the
device and became part of the effective Windows Firewall configuration.

## Test 1 – Enroll a Windows device in Intune
Logging into WIN11-INTUNE-01 as Adele Vance (the local administrator) and enroll this device in Intune. As shown below, this windows device is now joined to both Entra ID and Intune.

<img width="1291" height="631" alt="WIN11_INTUNE-01 enrolleras i Intune" src="https://github.com/user-attachments/assets/f1040138-a490-4902-9db4-38b0ae78a949" />

Expected result → Passed

## Test 2 – Verify that enrolled devices appear in the Intune admin center
WIN11-INTUNE-01 was successfully enrolled in Microsoft Intune and appeared in the Intune admin center as a managed Windows device.

<img width="1742" height="487" alt="WIN11_INTUNE-01 enrolleras i Intune bekräftad" src="https://github.com/user-attachments/assets/e0f35078-0443-4c56-93ad-6a0ec573be87" />

Expected result → Passed


## Test 3 - creating a configuration profile and push it out to the managed devices.

For this test a change in the settings for the public network firewall is desirable to enforce on managed devices.
Baseline configuration: The Windows Defender Firewall was enabled for all network profiles, while the default inbound and outbound actions were not explicitly configured.

```powershell
PS C:\WINDOWS\system32> Get-NetFirewallProfile | Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

| Profile | Enabled | Default Inbound Action | Default Outbound Action |
|---|---|---|---|
| Domain | True | NotConfigured | NotConfigured |
| Private | True | NotConfigured | NotConfigured |
| Public | True | NotConfigured | NotConfigured |

### Configuration Profile deployment

The Windows Firewall configuration profile was assigned to the test devices. Intune reported successful deployment for both devices.
<img width="1052" height="427" alt="Windows Firewall settings lab 3 settings" src="https://github.com/user-attachments/assets/fddb3067-f433-46e0-afac-ccffc99fc3c6" />


## Test 4 - Verification that the configured settings are applied on the endpoints.

### Endpoint verification

The configuration was verified on WIN11-INTUNE-02 using PowerShell. The Intune MDM policy store reported EnableLogDroppedPackets = True for the Public profile, and the effective ActiveStore configuration also reported LogBlocked = True.

| Test                                      | Expected result                 | Result |
| ----------------------------------------- | ------------------------------- | ------ |
| Configuration Profile assigned            | Policy deployed to test devices | Passed |
| Configuration Profile status              | Succeeded                       | Passed |
| Enable Log Dropped Packets` in MDM store | Public = True                    | Passed |
| Effective firewall configuration          | Public = True                   | Passed |

## Test 5 - Assigning a Compliance Policy

**Compliance policy matrix** 
| Focus-area         | Settings                           | value   |
|--------------------|------------------------------------|-------  |
| Device Heath       | Trusted Platform module (TPM)      | Require |
| Device Heath       | Require Secure Boot                | Require | 
| Device Security    | Firewall                           | Require | 
| Device Security    | Antivirus                          | Require |
| Device Security    | Antispyware                        | Require | 
| Microsoft Defender | Microsoft Defender Antimalware     | Require | 
| Microsoft Defender | Real-time protection               | Require | 
| Microsoft Defender | Security intelligence up-to-date   | Require | 

<img width="1542" height="316" alt="compliance policy" src="https://github.com/user-attachments/assets/f6f771b5-3669-4625-9783-517c69b56233" />

<img width="1512" height="446" alt="compliant devices" src="https://github.com/user-attachments/assets/e7590ddc-7d74-42c9-b218-7b35d86738d7" />

## Test 6 - Verification of the compliance state of the devices

The compliance requirements were independently verified on the Windows
endpoint using PowerShell.

The following security controls were verified:

| Compliance requirement | Endpoint verification | Result |
|---|---|---|
| Trusted Platform Module (TPM) | TpmPresent = True, TpmReady = True | Passed |
| Secure Boot | Confirm-SecureBootUEFI = True | Passed |
| Firewall | All profiles enabled | Passed |
| Antivirus | AntivirusEnabled = True | Passed |
| Antispyware | AntispywareEnabled = True | Passed |
| Real-time protection | RealTimeProtectionEnabled = True | Passed |
| Security intelligence | Antivirus signature timestamp present | Passed |

The endpoint configuration matched the requirements defined in the
Intune compliance policy.

## Test 7 – Introducing a non-compliant condition

For the non-compliant condition to introduce and evaluate, shutting off real-time protection on WIN11-INTUNE-02 was selected.
<img width="1037" height="922" alt="avstängt skydd" src="https://github.com/user-attachments/assets/17888ed1-89c8-49c1-824f-16e4d15c293d" />

PS C:\WINDOWS\system32> Get-MpComputerStatus | Select-Object RealTimeProtectionEnabled

RealTimeProtectionEnabled
-------------------------
                    False

The endpoint reported `RealTimeProtectionEnabled = False`.

## Test 8 – Verification that Intune detects the non-compliant state
<img width="1496" height="397" alt="non compliant" src="https://github.com/user-attachments/assets/9edc895d-1c7a-4d01-b147-6799da742040" />
<img width="1562" height="370" alt="non compliant 2" src="https://github.com/user-attachments/assets/3569363f-6547-4947-8fe2-0eb1b6dd0ceb" />
<img width="1572" height="547" alt="non compliant 3" src="https://github.com/user-attachments/assets/ef514919-0467-4c2c-ab03-ad07b1fc4193" />

Intune detected the changed endpoint state after synchronization.
The device was reported as Not compliant. The compliance policy identified
Real-time protection and Antivirus as not compliant, while the remaining
requirements remained compliant.

## Test 9 – Remediation

Of course, we can remediate this by enabling realtime protection from the endpoint. That would simply be a replication of test 7 & 8 in reverse order.
But initially I thought we should remediate this from within Intune instead. To do this, we have to use the Run remediation function pictured in the first image shown in test 8 above. This is a motor able to contain powershell scripts that enables an administrator to automate certain tasks, such as for this instance automatically remediate things that some careless person in the team might be known to do occasionally.

It is done in 2 steps:
- Detection script

Ex: 
```powershell
$Status = Get-MpComputerStatus

if ($Status.RealTimeProtectionEnabled -eq $true) {
    exit 0
}

exit 1
```
  
- Remediation script

Ex:
```powershell
Set-MpPreference -DisableRealtimeMonitoring $false
```

However: Use of remediations requires Windows a license verification to be enabled, and I don´t intend to purchase a windows license for a temporary virtual machine just to prove my point.

PS C:\WINDOWS\system32> Get-MpComputerStatus | Select-Object RealTimeProtectionEnabled

RealTimeProtectionEnabled
-------------------------
                     True


Test 10 – Verification of resulting compliance state

<img width="1507" height="331" alt="compliant now" src="https://github.com/user-attachments/assets/5a618818-f813-4529-afe7-730187205b67" />

# Lessons Learned
One important observation from this lab was the distinction between device configuration and compliance evaluation. Intune did not automatically prevent the local user from disabling a security setting. Instead, the change was detected during compliance evaluation and caused the device to become non-compliant. That makes Intune more an administrative tool and a monitoring tool, rather than a control tool.


