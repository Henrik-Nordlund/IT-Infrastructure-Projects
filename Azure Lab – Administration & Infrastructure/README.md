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

2. Resource Group
Create a dedicated Resource Group for the lab
Configure basic resource organisation
Add appropriate tags

3. Azure RBAC
Review Azure RBAC
Verify role assignment and scope
Test access to the Resource Group and resources

4. Networking
Create a Virtual Network
Create a subnet
Create a Network Security Group
Configure required network rules
Associate the NSG with the appropriate subnet or network interface

5. Compute
Deploy a small test Virtual Machine
Configure the required operating system and network settings
Verify the VM deployment
Connect to the VM

6. Monitoring
Review Azure Activity Log
Verify administrative activities
Review relevant monitoring information

7. Security configuration
Review network exposure
Verify NSG configuration
Verify Azure RBAC assignments
Review basic security configuration of the deployed resources

8. Resource lifecycle and cleanup
Document the completed environment
Delete the temporary Azure resources
Verify that the resources have been removed

## Testing and Results

Här skulle jag inte skriva resultaten ännu, utan bara definiera vilka tester labben ska innehålla.

### Test 1 – Verify Azure subscription access

Verify that the Azure subscription is accessible and that the user has sufficient permissions to create and manage resources.

Expected result → Passed

### Test 2 – Verify Resource Group configuration

Verify that the Resource Group was created in the intended subscription and region and that the configured tags are present.

Expected result → Passed

### Test 3 – Verify Azure RBAC

Verify the assigned Azure RBAC role and its scope.

Test that the assigned permissions correspond to the intended administrative role.

Expected result → Passed

### Test 4 – Verify Virtual Network configuration

Verify that the Virtual Network and subnet were created correctly and that the NSG is associated with the intended resource.

Expected result → Passed

### Test 5 – Verify VM deployment

Verify that the Virtual Machine was successfully deployed and that it can be accessed using the configured network and authentication settings.

Expected result → Passed

### Test 6 – Verify network security rules

Test network connectivity permitted by the NSG and verify that traffic not permitted by the configured rules is blocked.

Expected result → Passed

### Test 7 – Verify monitoring

Perform an administrative change to an Azure resource and verify that the corresponding activity is recorded in the Azure Activity Log.

Expected result → Passed

### Test 8 – Verify security configuration

Verify the RBAC assignments, network exposure, and relevant security configuration of the deployed resources.

Expected result → Passed

### Test 9 – Verify resource cleanup

Delete the temporary Azure resources and verify that the lab environment has been removed.

Expected result → Passed

# Lessons learned

To be completed after the lab.
