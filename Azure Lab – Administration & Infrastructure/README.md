
# Lab 4 – Azure Administration & Infrastructure

## Project Overview

This lab demonstrates the administration of a small Microsoft Azure infrastructure environment, including resource management, Azure role-based access control (RBAC), networking, virtual machine deployment, monitoring, and security configuration.

The objective was to create a temporary Azure environment, verify its configuration and functionality, document the results, and remove the resources after completing the lab.

The project also demonstrates basic Azure resource lifecycle management on my part, from deployment and configuration to verification and cleanup.

## Scenario

Nordlund Industries wants to establish a small infrastructure environment in Microsoft Azure.

The objective is to deploy and administer basic Azure resources, configure network security, verify administrative access, and monitor infrastructure activity.

The environment is intended for testing and demonstration purposes only. All lab resources are deployed within a dedicated Resource Group and are removed after testing and documentation have been completed. That last step is actually in itself a part of the lab.

## Lab Environment

| Component | Configuration |
|---|---|
| Cloud platform | Microsoft Azure |
| Subscription | Henrik Azure Subscription |
| Region | Sweden Central |
| Resource Group | `rg-nordlund-lab4` |
| Virtual Network | `vnet-nordlund-lab4` |
| Subnet | `snet-workload` (`10.10.1.0/24`) |
| Network Security Group | `nsg-nordlund-lab4` |
| Virtual Machine | `vm-nordlund-lab4` |
| Operating System | Windows Server 2025 Datacenter: Azure Edition |
| VM Size | Standard_B2s (2 vCPUs, 4 GiB RAM) |
| Private IP Address | `10.10.1.4` |
| Public IP Address | None |
| Monitoring | Azure Activity Log |
| Access Control | Azure RBAC |
| VM Security | Trusted Launch, Secure Boot, vTPM |

## Implementation

### 1. Azure Subscription and Administrative Access

Verified that the Azure subscription was active and accessible through Azure Portal and Azure PowerShell.

Confirmed the relationship between the Azure subscription and its associated Microsoft Entra tenant.

Reviewed administrative access and verified that the user had the built-in Owner role at subscription scope.

### 2. Resource Group

Created a dedicated Resource Group, `rg-nordlund-lab4`, to contain the temporary infrastructure resources.

The Resource Group was used to organize the lab environment and simplify resource management and eventual cleanup.

### 3. Azure RBAC

Reviewed the existing Azure RBAC role assignments at subscription scope.

During experimentation two separate Owner role assignments for the same user principal and scope was identified. The assignments had distinct role assignment IDs but shared the same principal Object ID, role definition, and scope.

The duplicate assignment was of course identified as redundant under the existing configuration. Its origin was not established, and no changes were made to the role assignments.

### 4. Networking

Created the Virtual Network `vnet-nordlund-lab4` with the subnet `snet-workload`, using the address range `10.10.1.0/24`.

Created the Network Security Group `nsg-nordlund-lab4` and associated it with the subnet.

The NSG uses Azure's default inbound and outbound security rules. No custom security rules were configured.

The subnet was configured without default outbound internet access. No NAT Gateway or custom route table was associated with the subnet.

### 5. Virtual Machine Deployment

Deployed `vm-nordlund-lab4` using Windows Server 2025 Datacenter: Azure Edition.

The VM was configured with:

- Standard_B2s (2 vCPUs, 4 GiB RAM)
- Trusted Launch
- Secure Boot and vTPM
- Standard SSD operating system disk
- Private IP address `10.10.1.4`
- No public IP address
- Network connectivity through `vnet-nordlund-lab4/snet-workload`

The VM was successfully deployed and started.

An authenticated Windows command-line session was established through Azure Serial Console, without using a public RDP connection or deploying Azure Bastion.

### 6. Monitoring

Reviewed Azure Activity Log for the deployed virtual machine.

Verified that administrative operations, including VM creation and update activities, were recorded with timestamps, operation status, and associated identities.

Azure Policy evaluation events were also visible in the log.

### 7. Security Configuration

Reviewed the VM's security configuration, network exposure, and administrative access.

Confirmed that Trusted Launch, Secure Boot, and vTPM were enabled.

Verified that the VM had no public IP address and that the NSG was associated with its subnet.

Used Azure Network Watcher to examine effective security rules and verify how the NSG evaluates a specified inbound RDP traffic flow.

## Testing and Results

### Test 1 – Verify Azure Subscription Access

**Objective:** Verify that the Azure subscription is accessible and that the user has sufficient administrative permissions.

**Verification:**

- Confirmed that the subscription was active.
- Verified the associated Microsoft Entra tenant.
- Confirmed Owner access at subscription scope.
- Verified subscription access through Azure Portal and Azure PowerShell.

**Result:** Passed.

### Test 2 – Verify Resource Group Configuration

**Objective:** Verify that the dedicated Resource Group was created in the intended subscription and region.

<img width="1862" height="481" alt="Resource Group Configuration verified" src="https://github.com/user-attachments/assets/83743dc6-a92d-45a1-961d-ad8a44ef2e62" />    

**Verification:**

- Confirmed the existence of `rg-nordlund-lab4`.
- Verified that the lab resources were deployed within the Resource Group.
- Confirmed that the environment was deployed in Sweden Central.

**Result:** Passed for Resource Group creation and resource organization. Tag verification was not documented in this lab.

### Test 3 – Verify Azure RBAC

**Objective:** Review the assigned Azure RBAC roles and their scope.

**Verification:**

- Reviewed existing role assignments at subscription scope.
- Confirmed two separate Owner role assignments for the same principal.
- Verified that the assignments had different role assignment IDs but the same principal Object ID, role definition, and scope.
- Documented the redundant assignment without modifying the existing configuration.

**Result:** Passed – The existing role assignments and their scope were reviewed. The origin of the duplicate assignment remains unknown.

### Test 4 – Verify Virtual Network Configuration

**Objective:** Verify the Virtual Network, subnet, and NSG configuration.

**Verification:**
- Confirmed that `vnet-nordlund-lab4` contains the subnet `snet-workload`.
- Verified the subnet address range `10.10.1.0/24`.
- Associated `nsg-nordlund-lab4` with the subnet.
- Confirmed that the NSG uses the default security rules.
- Verified that no custom route table was associated with the subnet.

<img width="716" height="907" alt="ip adress range" src="https://github.com/user-attachments/assets/5d01edbf-0e15-49d6-b171-c920a8aa7935" />    

<img width="1518" height="722" alt="subnet" src="https://github.com/user-attachments/assets/368427c2-53c8-4265-bcc7-af1c8ee2605c" />   

**Result:** Passed – The network configuration and NSG association were verified.

### Test 5 – Verify VM Deployment

**Objective:** Verify that the Windows Server VM was successfully deployed and could be accessed.

**Verification:**

- Confirmed successful VM deployment through Azure Portal.
- Verified that the VM was running.
- Confirmed Windows Server 2025 Datacenter: Azure Edition and Standard_B2s configuration.
- Verified private IP address `10.10.1.4` and the absence of a public IP address.
- Established an authenticated command-line session through Azure Serial Console.
- Executed `hostname`, `whoami`, and `ipconfig /all` to inspect the Windows environment.

**Result:** Passed – The VM was deployed, started, and accessed successfully.


<img width="1220" height="706" alt="azure-vm-deployment-completed" src="https://github.com/user-attachments/assets/aba029b8-5f7c-411d-b73e-aafde3e80c21" />

<img width="1061" height="579" alt="azure-vm-overview" src="https://github.com/user-attachments/assets/34f1a8ca-6622-44ef-9692-347c2ca751a5" />   

<img width="1102" height="686" alt="azure-vm-console-verification" src="https://github.com/user-attachments/assets/c8c295a1-08c3-4a8f-aa35-7cdf6a834cf9" />   

<img width="722" height="254" alt="azure-vm-console-verification-final" src="https://github.com/user-attachments/assets/89c0728c-1e0c-4046-a79f-f9dbccbea829" />   

### Test 6 – Verify Network Security Rules

**Objective:** Verify the effective NSG rules and evaluate inbound RDP traffic.

**Verification:**

- Confirmed that `nsg-nordlund-lab4` was associated with the VM's subnet.
- Reviewed the effective inbound and outbound security rules for the VM's network interface.
- Used Azure Network Watcher IP flow verify to evaluate inbound TCP traffic from a representative external IP address to `10.10.1.4` on port `3389`.
- Confirmed that the traffic was denied by the `DenyAllInBound` rule.

**Result:** Passed – The effective NSG configuration was verified, and the specified inbound RDP traffic flow was denied.

The IP flow verification evaluated the applicable NSG rules. It was not a live network connectivity test.

<img width="1360" height="693" alt="effective security-rules-expanded" src="https://github.com/user-attachments/assets/90027949-6b51-4a5b-97c5-91bd207966eb" />



![NSG IP flow verification](screenshots/azure-nsg-ip-flow-verification.png)
<img width="1524" height="710" alt="acitivitly log" src="https://github.com/user-attachments/assets/85a238ac-933f-4907-bbb3-d901cdf490b2" />

### Test 7 – Verify Monitoring

**Objective:** Verify that administrative activities affecting the virtual machine are recorded in Azure Activity Log.

**Verification:**

- Opened Azure Activity Log for `vm-nordlund-lab4`.
- Confirmed that VM creation and update operations were recorded.
- Reviewed administrative connection events and Azure Policy evaluation events.
- Verified the presence of operation names, timestamps, status information, and associated identities.

**Result:** Passed – Administrative activities were recorded and could be reviewed through Azure Activity Log.

Azure Activity Log provides visibility into Azure control-plane operations. It does not provide detailed monitoring of activity inside the Windows operating system.

<img width="1524" height="710" alt="azure-vm-activity-log" src="https://github.com/user-attachments/assets/c80b5894-92be-4183-ae5a-0a1ea09d654a" />


### Test 8 – Verify Security Configuration

**Objective:** Review the VM's security configuration, network exposure, and administrative access.

**Verification:**

- Confirmed that Trusted Launch was enabled.
- Verified that Secure Boot and vTPM were enabled.
- Confirmed that integrity monitoring was disabled.
- Verified that the VM had no public IP address.
- Confirmed the subnet-level NSG association.
- Verified that the tested inbound RDP traffic flow was denied.
- Reviewed the existing Azure RBAC Owner assignments at subscription scope.

**Result:** Passed – The security settings, network exposure, and administrative role assignments were reviewed and documented.

<img width="634" height="734" alt="azure-vm-security-configuration" src="https://github.com/user-attachments/assets/d491e43e-86c3-4c9c-aee6-2236bf111c7f" />

### Test 9 – Verify Resource Cleanup

**Objective:** Remove the temporary Azure infrastructure and verify that the resources have been deleted.

**Planned verification:**

- Review the resources contained in `rg-nordlund-lab4`.
- Confirm that no resources outside the intended lab environment will be affected.
- Delete the dedicated Resource Group and its associated resources.
- Verify that the Resource Group and temporary infrastructure have been removed.

**Result:** Pending – Resource cleanup has not yet been performed.

## Lessons Learned

This lab provided practical experience in deploying, administering, and verifying a small Azure infrastructure environment.

Several aspects were particularly useful:

**Azure RBAC and administrative scope**

Reviewing existing role assignments demonstrated the importance of examining both role definitions and assignment scope. The duplicate Owner assignments also illustrated why administrative configurations should be inspected rather than assumed to be correct.

**Network security and effective rules**

Creating an NSG was only part of the task. Associating it with the intended subnet and verifying the effective security rules were necessary to establish how the configuration applied to the VM.

Azure Network Watcher provided a way to evaluate a specific traffic flow without requiring an additional test machine.

**Secure VM administration**

The VM was deployed without a public IP address. Azure Serial Console allowed authenticated command-line access without exposing RDP to the internet or deploying Azure Bastion.

This demonstrated the difference between managing a virtual machine through Azure and accessing its operating system through a network connection.

**Monitoring and verification**

Azure Activity Log provided visibility into administrative operations affecting the VM. The exercise also highlighted the distinction between Azure control-plane logging and monitoring within the guest operating system.

**Resource lifecycle management**

The lab emphasized the importance of controlling resource costs and managing temporary infrastructure throughout its lifecycle.

The final cleanup and verification will complete this part of the exercise.

## Resource Cleanup

The lab resources are temporary and will be removed after the final documentation review.

**Status:** Pending.

The cleanup process and its verification will be documented here after the Resource Group has been deleted.


# Lab 4 - Azure Administration & Infrastructure

## Project overview

This lab demonstrates the administration of basic Azure infrastructure, including resource management, Azure RBAC, networking, compute, monitoring, and security configuration.

The lab focuses on creating and managing a small temporary Azure environment, verifying its configuration and functionality, and documenting the results.

A key part of the lab is the complete lifecycle of Azure resources: resources are created for testing, documented, and removed after the lab has been completed.

## Scenario

Nordlund Industries wants to use Microsoft Azure for a small infrastructure environment.

The objective is to establish and administer a basic Azure environment where resources can be created, access can be controlled, network configuration can be applied, and infrastructure activity can be monitored.

The lab uses a temporary test environment. Azure resources are created only for the purpose of the lab and are removed after testing and documentation have been completed.

Environment
Microsoft Azure
Azure subscription
Microsoft Entra ID
Azure Resource Group
Azure Virtual Network
Azure Subnet
Network Security Group
Azure Virtual Machine
Azure Monitor / Activity Log
Azure RBAC

## Implementation

1. Azure subscription and access
Verify Azure subscription
Verify tenant and subscription relationship
Verify user permissions
Verify Azure RBAC role

- Verified that the Azure subscription is active and accessible.
- Verified the Microsoft Entra tenant associated with the subscription.
- Confirmed Owner access at subscription scope.

3. Resource Group
Create a dedicated Resource Group for the lab
Configure basic resource organisation
Add appropriate tags

4. Azure RBAC
Review Azure RBAC
Verify role assignment and scope
Test access to the Resource Group and resources

- Reviewed existing Azure RBAC role assignments at subscription scope.
- Identified two separate Owner role assignments for the same user principal and scope.
- Confirmed that the assignments have distinct role assignment IDs.
- No changes were made to the existing role assignments.

5. Networking
Create a Virtual Network
Create a subnet
Create a Network Security Group
Configure required network rules
Associate the NSG with the appropriate subnet or network interface

6. Compute
Deploy a small test Virtual Machine
Configure the required operating system and network settings
Verify the VM deployment
Connect to the VM

7. Monitoring
Review Azure Activity Log
Verify administrative activities
Review relevant monitoring information

8. Security configuration
Review network exposure
Verify NSG configuration
Verify Azure RBAC assignments
Review basic security configuration of the deployed resources

9. Resource lifecycle and cleanup
Document the completed environment
Delete the temporary Azure resources
Verify that the resources have been removed

## Testing and Results


### Test 1 – Verify Azure subscription access

Verify that the Azure subscription is accessible and that the user has sufficient permissions to create and manage resources.

**Initial environment verification**

- Azure subscription: Henrik Azure Subscription
- Subscription status: Active
- Directory: Standardkatalog (`nordlundhenrikoutlook.onmicrosoft.com`)
- Administrative access: Owner at subscription scope
- Identity: Microsoft account identity represented in Microsoft Entra ID
- Subscription access: Verified through Azure Portal and Azure PowerShell

The subscription and administrative access were verified before creating the lab environment. Existing resources will be inventoried before deployment to avoid unnecessary resource creation and costs.

Expected result → Passed

### Test 2 – Verify Resource Group configuration

Verify that the Resource Group was created in the intended subscription and region and that the configured tags are present.

Expected result → Pending

### Test 3 – Verify Azure RBAC

Verify the assigned Azure RBAC role and its scope.

Test that the assigned permissions correspond to the intended administrative role.

RBAC baseline review

The subscription contains two separate Azure RBAC role assignments for the same user, both granting the built-in Owner role at subscription scope.

The assignments have different role assignment IDs but share the same principal Object ID, role definition and scope.

The duplicate assignment is redundant under the current configuration. Its origin has not been established. No changes were made to the existing role assignments during the initial review.

Expected result → Partially verified

### Test 4 – Verify Virtual Network Configuration

**Objective:** Verify that the Virtual Network, subnet, and Network Security Group (NSG) are correctly configured and associated.

**Verification:**

- Confirmed that `vnet-nordlund-lab4` contains the subnet `snet-workload` with the address range `10.10.1.0/24`.
- Verified that `nsg-nordlund-lab4` was successfully associated with `snet-workload`.
- Confirmed that the NSG contains the default inbound and outbound security rules, with no custom rules configured.
- Verified that no route table is associated with the subnet.

**Result:** Passed – The Virtual Network, subnet, and NSG association were verified in Azure Portal.

### Test 5 – Verify VM Deployment

**Objective:** Verify that the Windows Server virtual machine was successfully deployed, is running, and can be accessed using the configured authentication method.

**Verification:**

- Deployed `vm-nordlund-lab4` using Windows Server 2025 Datacenter: Azure Edition.
- Confirmed that the VM was running in Sweden Central with the `Standard_B2s` size (2 vCPUs, 4 GiB RAM).
- Verified that the VM was connected to `vnet-nordlund-lab4/snet-workload` and assigned the private IP address `10.10.1.4`, without a public IP address.
- Established an authenticated command-line session through Azure Serial Console without requiring Azure Bastion or public RDP access.
- Executed `hostname`, `whoami`, and `ipconfig /all` to verify the Windows environment and inspect its network configuration.

**Result:** Passed – The VM was successfully deployed, started, and accessed through an authenticated Windows command-line session.

### Test 6 – Verify Network Security Rules

**Objective:** Verify that the Network Security Group (NSG) applies the intended security rules to the virtual machine.

**Verification:**

- Confirmed that `nsg-nordlund-lab4` is associated with the subnet `snet-workload`.
- Reviewed the effective inbound and outbound security rules for the VM's network interface.
- Used Azure Network Watcher IP flow verify to evaluate inbound TCP traffic to `10.10.1.4` on port `3389` (RDP).
- The verification returned `Access denied`, confirming that the specified traffic flow is blocked by the effective security rules.

**Result:** Passed – The NSG configuration was verified, and the specified inbound RDP traffic was denied.

### Test 7 – Verify Monitoring

**Objective:** Verify that administrative activities affecting the virtual machine are recorded in Azure Activity Log.

**Verification:**

- Opened Azure Activity Log for `vm-nordlund-lab4`.
- Confirmed that VM creation and update operations were recorded.
- Reviewed administrative connection events and Azure Policy evaluation events.
- Verified that the log includes operation names, timestamps, status information, and the identity associated with the recorded activities.

**Result:** Passed – Administrative activities were successfully recorded and could be reviewed through Azure Activity Log.

### Test 8 – Verify Security Configuration

**Objective:** Review the virtual machine's security configuration, network exposure, and access controls.

**Verification:**

- Confirmed that the VM uses Trusted Launch with Secure Boot and vTPM enabled.
- Verified that integrity monitoring was not enabled.
- Confirmed that the VM has no public IP address.
- Verified that `nsg-nordlund-lab4` is associated with the VM's subnet.
- Used Azure Network Watcher IP flow verify to confirm that inbound RDP traffic from the tested external source was denied.
- Reviewed the existing Azure RBAC Owner assignments at subscription scope.

**Result:** Passed – The VM's security settings, network exposure, and existing administrative role assignments were reviewed and documented.

### Test 9 – Verify resource cleanup

Delete the temporary Azure resources and verify that the lab environment has been removed.

Expected result → Pending

# Lessons learned

To be completed after the lab.
