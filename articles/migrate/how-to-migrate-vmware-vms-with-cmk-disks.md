---
title: Migrera virtuella VMware-datorer till Azure med Server Side Encryption (SSE) och Kundhanterade nycklar (CMK) med hjälp av Azure Migrate Server-migrering
description: Lär dig hur du migrerar virtuella VMware-datorer till Azure med Server Side Encryption (SSE) och Kundhanterade nycklar (CMK) med hjälp av Azure Migrate Server-migrering
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751095"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrera virtuella VMware-datorer till virtuella Azure-datorer med kryptering på Server sidan och Kundhanterade nycklar

Den här artikeln beskriver hur du migrerar virtuella VMware-datorer till Azure Virtual Machines med diskar som har krypterats med hjälp av kryptering på Server sidan (SSE) med Kundhanterade nycklar (CMK) med hjälp av Azure Migrate Server-migrering (replikering utan agent).

Med den Azure Migrate serverns Migreringsverktyg kan du [migrera virtuella VMware-datorer till Azure med en agent lös replikering.](tutorial-migrate-vmware.md) Portal upplevelsen ger för närvarande inte möjlighet att aktivera SSE med CMK för dina replikerade diskar i Azure. Möjligheten att aktivera SSE med CMK för replikerade diskar är för närvarande endast tillgänglig via REST API. I den här artikeln får du se hur du skapar och distribuerar en [Azure Resource Manager mall](../azure-resource-manager/templates/overview.md) för att replikera en virtuell VMware-dator och konfigurera de replikerade diskarna i Azure för att använda SSE med CMK.

I exemplen i den här artikeln används [Azure PowerShell](/powershell/azure/new-azureps-module-az) för att utföra de uppgifter som behövs för att skapa och distribuera Resource Manager-mallen.

[Lär dig mer](../virtual-machines/disk-encryption.md) om Server Side Encryption (SSE) med Kundhanterade nycklar (CMK) för hanterade diskar.

## <a name="prerequisites"></a>Förutsättningar

- [Gå igenom självstudien](tutorial-migrate-vmware.md) om migrering av virtuella VMware-datorer till Azure med agent lös replikering för att förstå verktygs kraven.
- [Följ de här anvisningarna](./create-manage-projects.md) för att skapa ett Azure Migrate-projekt och lägga till **Azure Migrate: Migreringsverktyg för Server** i projektet.
- [Följ dessa anvisningar](how-to-set-up-appliance-vmware.md) för att konfigurera Azure Migrate-installationen för VMware i din lokala miljö och fullständig identifiering.

## <a name="prepare-for-replication"></a>Förbered för replikering

När identifieringen av den virtuella datorn är klar visar raden identifierade virtuella VMware-datorer som identifierats av enheten.

Innan du kan påbörja replikering av virtuella datorer måste replikeringens infrastruktur vara för beredd.

1. Skapa en Service Bus-instans i mål regionen. Service Bus används av den lokala Azure Migrates enheten för att kommunicera med Server migrations tjänsten för att samordna replikering och migrering.
2. Skapa ett lagrings konto för överföring av åtgärds loggar från replikering.
3. Skapa ett lagrings konto som Azure Migrates enheten överför replikeringsdata till.
4. Skapa en Key Vault och konfigurera Key Vault för att hantera token för signaturer för delad åtkomst för BLOB-åtkomst på de lagrings konton som skapades i steg 3 och 4.
5. Generera en token för signatur för delad åtkomst för Service Bus som skapades i steg 1 och skapa en hemlighet för token i den Key Vault som skapades i föregående steg.
6. Skapa en Key Vault åtkomst princip för att ge den lokala Azure Migrate-installationen (med hjälp av appen AAD-app) och Server migrations tjänsten åtkomst till Key Vault.
7. Skapa en replikeringsprincip och konfigurera server migrations tjänsten med information om den infrastruktur för replikering som skapades i föregående steg.

Infrastrukturen för replikering måste skapas i Azure-regionen för migreringen och i den Azure-prenumeration som de virtuella datorerna migreras till.

Portalen för Server Migration fören klar förberedelsen av infrastrukturen för replikering genom att automatiskt göra detta när du replikerar en virtuell dator för första gången i ett projekt. I den här artikeln förutsätter vi att du redan har replikerat en eller flera virtuella datorer med hjälp av Portal upplevelsen och att infrastrukturen för replikering redan har skapats. Vi ska titta på hur du kan identifiera information om den befintliga infrastrukturen för replikering och hur du använder den här informationen som indata till den Resource Manager-mall som ska användas för att konfigurera replikering med CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identifiera infrastruktur komponenter för replikering

1. På Azure Portal går du till sidan resurs grupper och väljer resurs gruppen där Azure Migrate projektet skapades.
2. Välj **distributioner** på den vänstra menyn och Sök efter ett distributions namn som börjar med strängen *"Microsoft. MigrateV2. VMwareV2EnableMigrate"*. Du ser en lista över Resource Manager-mallar som skapats av portalen för att konfigurera replikering för virtuella datorer i det här projektet. Vi laddar ned en sådan mall och använder den som bas för att förbereda mallen för replikering med CMK.
3. Om du vill hämta mallen väljer du en distribution som matchar sträng mönstret i föregående steg > väljer **mall** i den vänstra menyn > klickar på **Hämta** på den översta menyn. Spara template.jsfilen lokalt. Du redigerar den här mallfilen i det sista steget.

## <a name="create-a-disk-encryption-set"></a>Skapa en disk krypterings uppsättning

Ett objekt med en disk krypterings uppsättning mappar Managed Disks till en Key Vault som innehåller den CMK som ska användas för SSE. Om du vill replikera virtuella datorer med CMK skapar du en disk krypterings uppsättning och skickar den som en indatatyp till replikeringsåtgärden.

Följ exemplet [här](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) för att skapa en disk krypterings uppsättning med hjälp av Azure PowerShell. Se till att disk krypterings uppsättningen har skapats i mål prenumerationen som de virtuella datorerna migreras till och i Azure-regionen för migreringen.

Disk krypterings uppsättningen kan konfigureras för att kryptera hanterade diskar med en kundhanterad nyckel, eller för dubbel kryptering med både en kundhanterad nyckel och en plattforms nyckel. Om du vill använda alternativet dubbel kryptering i vilo läge konfigurerar du disk krypterings uppsättningen enligt beskrivningen [här](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md).

I det exempel som visas nedan konfigureras disk krypterings uppsättningen till att använda en kundhanterad nyckel.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Hämta information om den virtuella VMware-datorn som ska migreras

I det här steget ska du använda Azure PowerShell för att hämta information om den virtuella dator som behöver migreras. Den här informationen används för att skapa Resource Manager-mallen för replikering. Mer specifikt är de två intresse egenskaperna:

- Dator resurs-ID: t för de identifierade virtuella datorerna.
- Listan över diskar för den virtuella datorn och deras disk-ID: n.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Kopiera värdet för ID-strängen som motsvarar Azure Migrate-installationen som den virtuella datorn identifieras via. I det exempel som visas ovan är ID: t *"/Subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Kopiera värdena för ResourceId, namn och disk-UUID för datorn som ska migreras.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Skapa Resource Manager-mall för replikering

- Öppna den Resource Manager-mallfil som du laddade ned i steget **identifiera replikering av infrastruktur komponenter** i valfritt redigerings program.
- Ta bort alla resurs definitioner från mallen förutom resurser av typen *"Microsoft. RecoveryServices/valv/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Om det finns flera resurs definitioner av ovanstående typ, tar du bort alla utom en. Ta bort alla **dependsOn** egenskaps definitioner från resurs definitionen.
- I slutet av det här steget bör du ha en fil som ser ut som exemplet nedan och har samma uppsättning egenskaper.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Redigera **namn** -egenskapen i resurs definitionen. Ersätt strängen som följer den sista "/" i namn-egenskapen med värdet för *$Machine. Namn*(från föregående steg).
- Ändra värdet för egenskapen **Properties. providerSpecificDetails. vmwareMachineId** med värdet *$Machine. ResourceId*(från föregående steg).
- Ange värdena för **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** till mål resurs grupps-ID: t, målets virtuella nätverks resurs-ID och mål under näts namn.
- Ange värdet för **licenseType** till "Windows Server" för att tillämpa Azure Hybrid-förmån för den här virtuella datorn. Om den här virtuella datorn inte är giltig för Azure Hybrid-förmån anger du värdet för **licenseType** till NoLicenseType.
- Ändra värdet för egenskapen **targetVmName** till önskat namn på den virtuella Azure-datorn för den MIGRERAde virtuella datorn.
- Du kan också lägga till en egenskap med namnet **targetVmSize** under egenskapen **targetVmName** . Ange värdet för egenskapen **targetVmSize** till önskad storlek på virtuell Azure-dator för den MIGRERAde virtuella datorn.
- Egenskapen **disksToInclude** är en lista med disk indata för replikering med varje List objekt som representerar en lokal disk. Skapa så många List objekt som antalet diskar på den lokala virtuella datorn. Ersätt egenskapen **diskId** i listobjektet med uuid för de diskar som identifierades i föregående steg. Ange värdet "true" för **isOSDisk** för den virtuella DATORns OS-disk och "false" för alla andra diskar. Lämna **logStorageAccountId** och **logStorageAccountSasSecretName** -egenskaperna oförändrade. Ange värdet **diskType** till den Azure-hanterade disk typen (*Standard_LRS, Premium_LRS, StandardSSD_LRS*) som ska användas för disken. För diskar som behöver krypteras med CMK lägger du till en egenskap med namnet **diskEncryptionSetId** och anger värdet till resurs-ID för den disk krypterings uppsättning som skapades (**$des. ID**) i steget *skapa en disk krypterings uppsättning*
- Spara den redigerade mallfilen. I exemplet ovan ser den redigerade mallfilen ut så här:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Konfigurera replikering

Nu kan du distribuera den redigerade Resource Manager-mallen till projekt resurs gruppen och konfigurera replikeringen för den virtuella datorn. Lär dig hur du [distribuerar resurser med Azure Resource Manager mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Nästa steg

[Övervaka replikeringsstatus](tutorial-migrate-vmware.md#track-and-monitor) via portal upplevelsen och utför testmigreringar och migrering.