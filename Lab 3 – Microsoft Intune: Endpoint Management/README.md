
# Lab 3 – Microsoft Intune: Endpoint Management

## Project overview

This lab demonstrate how Microsoft Intune can be used to manage Windows endpoints, apply security-related configuration, and evaluate device compliance.

The focus is the hands-on administration and verification of managed Windows devices, including policy deployment, endpoint configuration, compliance evaluation, and troubleshooting.

My goal here is to demonstrate that I can:

* enroll and manage Windows devices with Microsoft Intune
* use security groups for policy assignment
* create and deploy Configuration Profiles
* create and evaluate Compliance Policies
* verify policy results from both Intune and the Windows endpoint
* investigate differences between configured and effective endpoint settings
* identify and analyze non-compliant device states
* use PowerShell to verify endpoint configuration and security status

It also illustrates how Microsoft Intune can be utilized as a security tool, as well as its limitations in this area.

## Environment

* Microsoft 365 Developer Program tenant
* Microsoft Intune
* Microsoft Entra ID
* Two test users:

  * Adele Vance
  * Alex Wilber
* Two Windows 11 virtual machines running on Hyper-V:

  * `WIN11-INTUNE-01`
  * `WIN11-INTUNE-02`
    
On both virtual machines, I configured Windows 11 Pro to use Swedish as the system language, as it is my native language and mother tongue.

## Scenario

Nordlund Industries wants to centrally manage Windows endpoints using Microsoft Intune.

The objective is to establish a basic endpoint management environment where devices can be enrolled, configured through centralized policies, and evaluated against  security and compliance requirements defined by me for this fictional company.

The lab uses a small test environment to simulate common endpoint management tasks performed by an IT administrator. Policy deployment and compliance results are verified both in the Intune admin center and directly on the Windows endpoints.


## Implementation

### Device Enrollment

WIN11-INTUNE-01 and WIN11-INTUNE-02 were enrolled in Microsoft Intune and joined to Microsoft Entra ID.

Both devices were successfully registered as Intune-managed Windows devices and appeared in the Intune admin center without any issues.

### Configuration Profile

A Windows Firewall configuration profile was created using the Settings Catalog.

The following settings were configured:

* Enable Public Network Firewall
* Enable Log Dropped Packets

The profile was assigned to the test device group containing WIN11-INTUNE-01 and WIN11-INTUNE-02.

### Compliance Policy

A Windows 11 compliance policy was created to evaluate the security state of the managed endpoints.

The policy I set requires the following:

| Focus area | Setting | Requirement |
|---|---|---|
| Device Health | Trusted Platform Module (TPM) | Require |
| Device Health | Secure Boot | Require |
| Device Security | Firewall | Require |
| Device Security | Antivirus | Require |
| Device Security | Antispyware | Require |
| Microsoft Defender | Microsoft Defender Antimalware | Require |
| Microsoft Defender | Real-time protection | Require |
| Microsoft Defender | Security intelligence up-to-date | Require |

### Policy Assignment

I created a security group called GRP-Intune-Test-Devices and added both test devices to the group. Using a device group makes it easier and more convenient for me as an administrator to manage policy assignments if there are many test devices or to scale the configuration when more test devices are added.

The Configuration Profile was then assigned to the security group `GRP-Intune-Test-Devices`, containing WIN11-INTUNE-01 and WIN11-INTUNE-02.

The Compliance Policy was also assigned to the same security group and thus the same test devices.


## Testing and Results

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

## Test 1 – Enroll a Windows device in Intune

I logged into WIN11-INTUNE-01 as Adele Vance (the local administrator) and enrolled the device in Intune.

As shown below, the Windows device is now joined to both Microsoft Entra ID and Microsoft Intune.

<img width="1291" height="631" alt="WIN11_INTUNE-01 enrolleras i Intune" src="https://github.com/user-attachments/assets/f1040138-a490-4902-9db4-38b0ae78a949" />   

**Expected result → Passed**

## Test 2 – Verify that enrolled devices appear in the Intune admin center

I verified in the Intune admin center that WIN11-INTUNE-01 was successfully enrolled in Microsoft Intune and appeared as a managed Windows device.

<img width="1742" height="487" alt="WIN11_INTUNE-01 enrolleras i Intune bekräftad" src="https://github.com/user-attachments/assets/e0f35078-0443-4c56-93ad-6a0ec573be87" />  

**Expected result → Passed**

## Test 3 - Create a Configuration Profile and deploy it to the managed devices

To create a Configuration Profile in Intune, I went to Devices > Manage devices > Configuration > Create policy.

As mentioned above, the Configuration Profile was configured to enable logging of dropped packets for the Public network firewall.

The Windows Firewall configuration profile was then assigned to GRP-Intune-Test-Devices. Intune reported successful deployment for both devices.

<img width="1475" height="615" alt="configuration settings picker" src="https://github.com/user-attachments/assets/9be57b67-eed0-4e54-baa8-f6a54e031a8b" />

<img width="1590" height="477" alt="firewall settings intune portal" src="https://github.com/user-attachments/assets/7670cad5-b3a3-496c-87f6-024c8839c834" />

<img width="1052" height="427" alt="Windows Firewall settings lab 3 settings" src="https://github.com/user-attachments/assets/fddb3067-f433-46e0-afac-ccffc99fc3c6" />

**Expected result → Passed**

## Test 4 - Verify that the configured settings are applied on the endpoints

### Endpoint verification

The configuration was verified on WIN11-INTUNE-02 using PowerShell.

Baseline configuration: Windows Defender Firewall was enabled for all network profiles, while the default inbound and outbound actions were not explicitly configured, as shown below.

```powershell
PS C:\WINDOWS\system32> Get-NetFirewallProfile | Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

| Profile | Enabled | Default Inbound Action | Default Outbound Action |
|---|---|---|---|
| Domain | True | NotConfigured | NotConfigured |
| Private | True | NotConfigured | NotConfigured |
| Public | True | NotConfigured | NotConfigured |

Endpoint verification can be somewhat tricky in this case because Windows Firewall comes with a pre-configured PersistentStore containing local settings. When a custom configuration policy is created and applied from the Intune admin center, the resulting policy settings can instead be found in the MDM policy store and the effective ActiveStore configuration. I had to specify the -PolicyStore parameter when checking these settings with PowerShell.

Step 1 – Check the firewall profile without specifying a policy store
```powershell
PS C:\WINDOWS\system32> Get-NetFirewallProfile | Select-Object Name, LogBlocked
```

| Profile | LogBlocked |
|---|---|
| Domain | False |
| Private | False |
| Public | False |


Step 2 - Check the effective policy in ActiveStore    

```powershell
PS C:\WINDOWS\system32> Get-NetFirewallProfile -PolicyStore ActiveStore | Select-Object Name, LogBlocked
```
| Profile | LogBlocked |
|---|---|
| Domain | False |
| Private | False |
| Public | True |

Step 3 - Check the policy settings applied by Intune 

```powershell
PS C:\WINDOWS\system32> Get-NetFirewallProfile -PolicyStore MDM | Select-Object Name, LogBlocked
```

| Profile | LogBlocked |
|---|---|
| Domain | NotConfigured |
| Private | NotConfigured |
| Public | True |

This confirmed that the Intune configuration was applied to the
device and became part of the effective Windows Firewall configuration.


| Test                                      | Expected result                 | Result |
| ----------------------------------------- | ------------------------------- | ------ |
| Configuration Profile assigned            | Policy deployed to test devices | Passed |
| Configuration Profile status              | Succeeded                       | Passed |
| Enable Log Dropped Packets` in MDM store  | Public = True                   | Passed |
| Effective firewall configuration          | Public = True                   | Passed |

**Expected result → Passed**

## Test 5 - Assign a Compliance Policy

I assigned the Compliance Policy to GRP-Intune-Test-Devices, containing WIN11-INTUNE-01 and WIN11-INTUNE-02.

The policy was then synchronized with the test devices and evaluated by Intune.

**Compliance policy matrix** 
| Focus-area         | Settings                           | value   |
|--------------------|------------------------------------|-------  |
| Device Health       | Trusted Platform module (TPM)      | Require |
| Device Health       | Require Secure Boot                | Require | 
| Device Security    | Firewall                           | Require | 
| Device Security    | Antivirus                          | Require |
| Device Security    | Antispyware                        | Require | 
| Microsoft Defender | Microsoft Defender Antimalware     | Require | 
| Microsoft Defender | Real-time protection               | Require | 
| Microsoft Defender | Security intelligence up-to-date   | Require | 

<img width="1542" height="316" alt="compliance policy" src="https://github.com/user-attachments/assets/f6f771b5-3669-4625-9783-517c69b56233" />

<img width="1512" height="446" alt="compliant devices" src="https://github.com/user-attachments/assets/e7590ddc-7d74-42c9-b218-7b35d86738d7" />

**Expected result → Passed**

## Test 6 – Verify the compliance state of the devices

The compliance requirements were independently verified on the Windows endpoint using PowerShell.

The following security controls were verified:

| Compliance requirement        | Endpoint verification                  | Result |
| ----------------------------- | -------------------------------------- | ------ |
| Trusted Platform Module (TPM) | `TpmPresent = True`, `TpmReady = True` | Passed |
| Secure Boot                   | `Confirm-SecureBootUEFI = True`        | Passed |
| Firewall                      | All profiles enabled                   | Passed |
| Antivirus                     | `AntivirusEnabled = True`              | Passed |
| Antispyware                   | `AntispywareEnabled = True`            | Passed |
| Real-time protection          | `RealTimeProtectionEnabled = True`     | Passed |
| Security intelligence         | Antivirus signature timestamp present  | Passed |

The endpoint configuration matched the requirements defined in the Intune compliance policy.

**Expected result → Passed**

## Test 7 – Introducing a non-compliant condition

To introduce and evaluate a non-compliant condition, I disabled real-time protection on WIN11-INTUNE-02.

<img width="1037" height="922" alt="avstängt skydd" src="https://github.com/user-attachments/assets/17888ed1-89c8-49c1-824f-16e4d15c293d" />

Get-MpComputerStatus | Select-Object RealTimeProtectionEnabled

The endpoint reported RealTimeProtectionEnabled = False.

**Expected result → Passed**

## Test 8 – Verification that Intune detects the non-compliant state
<img width="1496" height="397" alt="non compliant" src="https://github.com/user-attachments/assets/9edc895d-1c7a-4d01-b147-6799da742040" />
<img width="1562" height="370" alt="non compliant 2" src="https://github.com/user-attachments/assets/3569363f-6547-4947-8fe2-0eb1b6dd0ceb" />
<img width="1572" height="547" alt="non compliant 3" src="https://github.com/user-attachments/assets/ef514919-0467-4c2c-ab03-ad07b1fc4193" />

After synchronization, Intune detected the changed endpoint state. The device was reported as Not compliant.

The compliance policy identified Real-time protection and Antivirus as not compliant, while the remaining requirements remained compliant.

**Expected result → Passed**

## Test 9 – Remediation

At this point I can remediate this by this by enabling real-time protection from the endpoint, and I ultimately that is what I decided to do. 

To remediate from the intune portal, the run remediation function could be run however this requires purchasing specific windows license for the OS on the virtual machines which was out of scope for this demonstration lab.

It would be done in 2 steps.

Step 1 - Detection script

Ex: 
```powershell
$Status = Get-MpComputerStatus

if ($Status.RealTimeProtectionEnabled -eq $true) {
    exit 0
}

exit 1
```
  
Step 2 - Remediation script

Ex:
```powershell
Set-MpPreference -DisableRealtimeMonitoring $false
```
### Remediation from the endpoint

```powershell
PS C:\WINDOWS\system32> Get-MpComputerStatus | Select-Object RealTimeProtectionEnabled
```
Result -> RealTimeProtectionEnabled: True

**Expected result → Passed**

## Test 10 – Verification of resulting compliance state

<img width="1507" height="331" alt="compliant now" src="https://github.com/user-attachments/assets/5a618818-f813-4529-afe7-730187205b67" />

**Expected result → Passed**

# Lessons Learned
One lesson from this lab is that compliance and configuration is not the same thing. Configuration policy enables the organization to centrally configure the endpoints in their digital environment to their liking. It could be to not show this optionality if a user clicks in this specific menu, or to automatically have firewall enabled on the device. But it is not really a security setting, it is an administrative tool. Compliance however is used to measure to determine if this particualar device fullfills our security requirements as the organization ha defined them

Another important observation from this lab was the distinction between device configuration and compliance evaluation. Intune did not automatically prevent the local user from disabling a security setting. Instead, the change was detected during compliance evaluation and caused the device to become flagged as non-compliant. That makes Intune more an administrative tool used for configuration and monitoring, rather than a security tool to me - even if Intune is useful for security purposes.

It is worth mentioning that Intune does not automatically detect a non-compliant device. There is a check-in cycle, but to apply it immediately - sync.

As always in Microsoft 365 - it is faster and easier to manage things if you put things in groups, and apply policies to those groups rather than manage each device individually. If there are many policies to apply, pay attention to principles as least privilege as those polcies could otherwise interfer with each other. Policies needs to have te right scope and be specific. 
