# Azure vwan with s2s-autolab

This one is a bit different, this deploys a pair logic apps with the API connector as well. One logicapp to create the lab environment at the start of every day, and another to delete the lab resource group at the end of the day. 

## ! Important !  
This uses a cisco asav so you will need to accept the VM image terms before deployment, you can do that by running this in the cloudshell:  
    az vm image terms accept --urn cisco:cisco-asav:asav-azure-byol:latest

After deploying the logic apps, you'll need to give the managed identities of both contributor access to your subscription to be able to create and delete the resource groups. 
You can run these powershell commands in the cloudshell to do that:  
    $id1 = (Get-AzADServicePrincipal -DisplayName lab-vwans2s-creator).id  
    $id2 = (Get-AzADServicePrincipal -DisplayName lab-vwans2s-killer).id  
    $subid = (Get-AzSubscription).Id  
    New-AzRoleAssignment -ObjectId $id1 -RoleDefinitionName Contributor -Scope /subscriptions/$subid  
    New-AzRoleAssignment -ObjectId $id2 -RoleDefinitionName Contributor -Scope /subscriptions/$subid  

This creates a vwan and a couple of spoke vnets with VM's connected to the vhub and an onprem vnet with a ASAv with a S2S tunnel to the vhub. You'll be prompted for the resource group name to deploy the logicapps in, your public ip and username and password to use for the VM's and a resource group for the lab resources to be in, this resource group will be created and deleted everyday. NSG's are placed on the default subnets of each vnet allowing RDP access from your public ip. This also creates a logic app that will delete the resource group in 24hrs.

The topology of the lab will look like this:
![wvanlabwithS2S-asa](https://github.com/user-attachments/assets/f5b9686e-c355-4e7c-a383-24dcea9676f8)
