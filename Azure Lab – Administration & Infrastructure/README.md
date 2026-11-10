# Lab 4 – Azure Administration & Infrastructure

## Project Overview
This lab demonstrates the administration of a small Microsoft Azure infrastructure environment, including resource management, Azure role-based access control (RBAC), networking, virtual machine deployment, monitoring, and security configuration.

The objective was to create a temporary Azure environment, verify its configuration and functionality, document the results, and remove the resources after completing the lab.

The project also demonstrates my practical experience with Azure resource lifecycle management, from deployment and configuration to verification and cleanup.  

## Scenario

Nordlund Industries wants to establish a small infrastructure environment in Microsoft Azure.

The objective is to deploy and administer basic Azure resources, configure network security, verify administrative access, and monitor infrastructure activity.

The environment is intended for testing and demonstration purposes only. All lab resources are deployed within a dedicated Resource Group and are removed after testing and documentation have been completed. Resource cleanup and verification are integral parts of the lab.

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

Created a dedicated Resource Group, `rg-nordlund-lab4`, to contain the temporary infrastructure resources. Applied organizational tags to identify the purpose and temporary nature of the lab resources, including Purpose: Lab4 and Environment: Temporary.

The Resource Group was used to organize the lab environment and simplify resource management and eventual cleanup.

### 3. Azure RBAC

Reviewed the existing Azure RBAC role assignments at subscription scope.

During the initial review, I identified two separate Owner role assignments for the same user principal and scope. The assignments had distinct role assignment IDs but shared the same principal Object ID, role definition, and scope.

The duplicate assignment was identified as redundant under the existing configuration. Its origin was not established, and no changes were made to the role assignments.

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

<img width="1224" height="454" alt="azure-subscription-overview" src="https://github.com/user-attachments/assets/40f484a4-21d8-4bf6-9d8d-211a20df08ea" />   

**Result:** Passed.

### Test 2 – Verify Resource Group Configuration

**Objective:** Verify that the dedicated Resource Group was created in the intended subscription and region and that its organizational tags were configured.

<img width="1490" height="385" alt="Resource Group Configuration verified" src="https://github.com/user-attachments/assets/afa8758c-e0e9-4db2-8457-41b2fd51a28f" />


*Figure: Resource Group configuration before deployment of the lab infrastructure.*

**Verification:**

- Confirmed that `rg-nordlund-lab4` was created in the intended Azure subscription.
- Verified that the Resource Group was located in Sweden Central.
- Confirmed that organizational tags were configured, including `Purpose: Lab4` and `Environment: Temporary`.

**Result:** Passed – The Resource Group's subscription, region, and organizational tags were verified.

### Test 3 – Verify Azure RBAC

**Objective:** Review the existing Azure RBAC role assignments and verify their scope.

<img width="1513" height="389" alt="azure-subscription-rbac" src="https://github.com/user-attachments/assets/e265b346-2588-4a14-8bc8-593c647d2c3f" />   

**Verification:**

- Reviewed the existing Azure RBAC role assignments at subscription scope.
- Identified two Owner role assignments for the same user principal.
- Confirmed that both assignments shared the same principal Object ID, role definition, and scope.
- Verified that the assignments had distinct role assignment IDs.
- Documented the redundant assignment without modifying the existing configuration.

**Result:** Passed – The existing Azure RBAC assignments and their scope were reviewed. The duplicate Owner assignment was identified, although its origin remains unknown.

**Additional observation:**

I have a possible explanation for when I may have inadvertently created the duplicate Owner assignment. However, I could not verify this hypothesis because the relevant events were outside the retention period of the available Azure Activity Logs. The origin of the duplicate assignment therefore remains unconfirmed.

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

<img width="810" height="698" alt="Ip flow verify" src="https://github.com/user-attachments/assets/cbbf2b32-4494-4f6d-915b-8d0b104c2a2e" />   

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

**Azure Activity Log retention**

Azure Activity Log retains events for 90 days by default. This became relevant when I investigated the duplicate Owner role assignment identified during the lab.

I had a possible explanation for when I might have created the duplicate assignment, but the relevant events were no longer available in the Activity Log. I therefore could not verify its origin.

This highlighted the importance of planning log retention in advance. For longer-term auditing and investigation, Azure Activity Logs can be exported to services such as Log Analytics or Azure Storage, rather than relying solely on the default retention period.

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
