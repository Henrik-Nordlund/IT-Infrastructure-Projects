
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

### Planned Testing

The following tests will be performed during the next stages of the lab:

- Enroll a Windows device in Intune
- Verify that enrolled devices appear in the Intune admin center
- Assign a Configuration Profile to the test devices
- Verify that the configured settings are applied
- Assign a Compliance Policy
- Verify the compliance state of the devices
- Introduce a non-compliant condition
- Verify that Intune detects the non-compliant state
- Remediate the condition and verify the resulting compliance state

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
This is the baseline on each of the managed devices:

Powershell:

```powershell
PS C:\WINDOWS\system32> Get-NetFirewallProfile | Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

| Profile | Enabled | Default Inbound Action | Default Outbound Action |
|---|---|---|---|
| Domain | True | NotConfigured | NotConfigured |
| Private | True | NotConfigured | NotConfigured |
| Public | True | NotConfigured | NotConfigured |
