# Lab 4 - Azure Administration & Infrastructure (under construction) 

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

### Test 5 – Verify VM deployment

Verify that the Virtual Machine was successfully deployed and that it can be accessed using the configured network and authentication settings.

Expected result → Pending

### Test 6 – Verify network security rules

Test network connectivity permitted by the NSG and verify that traffic not permitted by the configured rules is blocked.

Expected result → Pending

### Test 7 – Verify monitoring

Perform an administrative change to an Azure resource and verify that the corresponding activity is recorded in the Azure Activity Log.

Expected result → Pending

### Test 8 – Verify security configuration

Verify the RBAC assignments, network exposure, and relevant security configuration of the deployed resources.

Expected result → Pending

### Test 9 – Verify resource cleanup

Delete the temporary Azure resources and verify that the lab environment has been removed.

Expected result → Pending

# Lessons learned

To be completed after the lab.
