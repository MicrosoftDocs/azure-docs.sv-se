---
title: Vanliga fel vid migrering från klassiskt läge till Azure Resource Manager-läge
description: Den här artikeln innehåller de vanligaste felen och begränsningarna under migreringen av IaaS-resurser från Azure Service Management till Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 02bf0430892490b5d3cfe35a6da4bf0973373fd3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676135"
---
# <a name="errors-that-commonly-occur-during-classic-to-azure-resource-manager-migration"></a>Fel som ofta uppstår under klassisk att Azure Resource Manager migrering

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](classic-vm-deprecation.md#how-does-this-affect-me).

I den här artikeln visas vanliga fel och åtgärder under migrering av IaaS-resurser från Azures klassiska distributionsmodell till Azure Resource Manager-stacken.


## <a name="list-of-errors"></a>Lista över fel

| Felsträng | Åtgärd |
| --- | --- |
| Internt serverfel |I vissa fall är det här ett tillfälligt fel som försvinner vid ett nytt försök. Om felet kvarstår kan du [kontakta Azure-supporten](../azure-portal/supportability/how-to-create-azure-support-request.md) så att plattformsloggarna kan undersökas. <br><br> **OBS!** När supporten väl har spårat incidenten bör du inte göra några egna försök att åtgärda problemet eftersom det kan få oönskade konsekvenser för din miljö. |
| Migrering stöds inte för distributionen {deployment-name} i HostedService {hosted-service-name} eftersom det är en PaaS-distribution (Web/Worker). |Det här inträffar när en distribution innehåller en web/worker-roll. Eftersom migrering bara stöds för Virtual Machines bör du ta bort web/worker-rollen från distributionen. Försök sedan igen. |
| Det gick inte att distribuera mallen {template-name}. CorrelationId={guid} |I serverdelen av migreringstjänsten använder vi Azure Resource Manager-mallar för att skapa resurser i Azure Resource Manager-stacken. Eftersom mallar är idempotenta kan du vanligtvis lugnt göra ett nytt migreringsförsök för att komma förbi felet. Om felet kvarstår kan du [kontakta Azure-supporten](../azure-portal/supportability/how-to-create-azure-support-request.md) och ge dem CorrelationId. <br><br> **OBS!** När supporten väl har spårat incidenten bör du inte göra några egna försök att åtgärda problemet eftersom det kan få oönskade konsekvenser för din miljö. |
| Det virtuella nätverket {virtual-network-name} finns inte. |Det här kan inträffa om du har skapat det virtuella nätverket på nya Azure Portal. Det faktiska namnet på det virtuella nätverket följer mönstret "Grupp * \<VNET name>" |
| Den virtuella datorn {vm-name} i HostedService {hosted-service-name} innehåller tillägget {extension-name} som inte stöds i Azure Resource Manager. Du bör avinstallera det från den virtuella datorn innan du fortsätter med migreringen. |XML-tillägg som BGInfo 1. \* stöds inte i Azure Resource Manager. Därför kan de inte heller migreras. Om tilläggen är kvar på den virtuella datorn avinstalleras de automatiskt innan migreringen slutförs. |
| Den virtuella datorn {vm-name} i HostedService {hosted-service-name} innehåller tillägget VMSnapshot/VMSnapshotLinux, som för närvarande inte stöds för migrering. Avinstallera det från den virtuella datorn och lägg tillbaka det via Azure Resource Manager när migreringen är klar |I det här fallet är den virtuella datorn konfigurerad för Azure Backup. Eftersom det för närvarande är ett scenario som inte stöds följer du lösningen på https://aka.ms/vmbackupmigration |
| Den virtuella datorn {vm-name} i HostedService {hosted-service-name} innehåller tillägget {extension-name} vars status inte har rapporteras från den virtuella datorn. Den virtuella datorn kan därför inte migreras. Se till att tilläggets status rapporteras eller avinstallera tillägget från den virtuella datorn och försök att migrera igen. <br><br> Den virtuella datorn {vm-name} i HostedService {hosted-service-name} innehåller tillägget {extension-name} som rapporterar hanterarstatus: {handler-status}. Den virtuella datorn kan därför inte migreras. Se till att tilläggets hanterarstatus som rapporteras är {handler-status} eller avinstallera det från den virtuella datorn och försök att migrera igen. <br><br> Den virtuella datoragenten för den virtuella datorn {vm-name} i HostedService {hosted-service-name} rapporterar övergripande agentstatus Inte redo. Den virtuella datorn kan därför inte migreras, om den har ett migreringsbart tillägg. Se till att den virtuella datoragenten rapporterar övergripande agentstatus Redo. Referera till https://aka.ms/classiciaasmigrationfaqs . |Azure-gästagenten och virtuella datortillägg måste ha utgående Internetåtkomst till den virtuella datorns lagringskonto för att kunna ange status. Vanliga orsaker till fel kan vara <li> en nätverkssäkerhetsgrupp som blockerar utgående åtkomst till Internet <li> Om VNET har lokala DNS-servrar och DNS-anslutningen förloras <br><br> Om en status som inte stöds fortsätter att visas kan du avinstallera tilläggen om du vill hoppa över kontrollen och gå vidare med migreringen. |
| Migrering stöds inte för distributionen {deployment-name} i HostedService {hosted-service-name} eftersom den har flera tillgänglighetsuppsättningar. |För närvarande går det bara att migrera värdbaserade tjänster med högst 1 tillgänglighetsuppsättning. Undvik det här problemet genom att flytta ytterligare tillgänglighets uppsättningar och virtuella datorer i dessa tillgänglighets uppsättningar till en annan värdbaserad tjänst. |
| Migrering stöds inte för distributionen {deployment-name} i HostedService {hosted-service-name} eftersom den har virtuella datorer som inte ingår i tillgänglighetsuppsättningen även om HostedService innehåller en. |Lösningen i det här fallet är att flytta alla virtuella datorer till en enda tillgänglighetsuppsättning eller ta bort alla virtuella datorer från tillgänglighetsuppsättningen i den värdbaserade tjänsten. |
| Kontot för lagring/HostedService/virtuellt nätverk {virtual-network-name} håller på att migreras och kan därför inte ändras |Det här felet inträffar när migreringsåtgärden ”Förbered” har slutförts på resursen och en åtgärd som skulle ändra resursen aktiveras. Eftersom hanteringsplanet låses efter förberedelseåtgärden blockeras alla ändringar av resursen. Du kan låsa upp hanteringsplanet, köra en incheckningsåtgärd för att slutföra migreringen eller köra migreringsåtgärden Avbryt för att återställa förberedelseåtgärden. |
| Migrering tillåts inte för HostedService {hosted-service-name} eftersom den virtuella datorn {vm-name} har status: RoleStateUnknown. Migrering tillåts bara när den virtuella datorn har följande status: körs, stoppad, stoppad frigjord. |Den virtuella datorn kan genomgå en tillstånds över gång, som vanligt vis sker vid en uppdatering på HostedService, till exempel en omstart, installation av tillägg osv. Vi rekommenderar att uppdaterings åtgärden slutförs på HostedService innan du försöker migrera. |
| Distributionen {deployment-name} i HostedService {hosted-service-name} innehåller en virtuell dator {vm-name} med datadisken {data-disk-name} vars fysiska blobstorlek på {size-of-the-vhd-blob-backing-the-data-disk} byte inte stämmer med den logiska storleken på den virtuella datorns datadisk på {size-of-the-data-disk-specified-in-the-vm-api} byte. Migreringen fortsätter utan att du anger en storlek för datadisken för den virtuella Azure Resource Manager-datorn. | Felet kan inträffa om du har ändrat storlek på den virtuella hårddisk-blobben utan att uppdatera storleken på den virtuella dator API-modellen. Utförliga anvisningar för migrering hittar du [nedan](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes).|
| Ett Storage-undantag uppstod under verifiering av datadisken {data disk name} med medielänken {data disk Uri} för den virtuella datorn {VM name} i molntjänsten {Cloud Service name}. Kontrollera att den virtuella hårddiskens medielänk kan nås av den virtuella datorn | Det här kan inträffa om den virtuella datorns diskar har tagits bort eller inte kan nås längre. Kontrollera att den virtuella datorns diskar finns.|
| Den virtuella datorn {vm-name} i HostedService {cloud-service-name} innehåller en disk med MediaLink {vhd-uri} med blobbnamnet {vhd-blob-name} som inte stöds i Azure Resource Manager. | Felet uppstår när blobbnamnet innehåller ett snedstreck: /, vilket för närvarande inte stöds i Compute-resursprovidern. |
| Migrering tillåts inte för distributionen {deployment-name} i HostedService {cloud-service-name} eftersom den inte ingår i det regionala omfånget. Se https: \/ /aka.MS/regionalscope för att flytta den här distributionen till det regionala omfånget. | Azure meddelade 2014 att nätverksresurser skulle flyttas från klusternivå till regionalnivå. Mer [https://aka.ms/regionalscope](https://aka.ms/regionalscope) information finns i. Felet kan uppstå när den distribution som migreras inte har uppdaterats så att den automatiskt flyttas till ett regionalt omfång. Det bästa arbetet är att antingen lägga till en slut punkt till en virtuell dator eller en datadisk till den virtuella datorn och sedan försöka migrera igen. <br> Mer information finns i [How to set up endpoints on a classic Windows virtual machine in Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) (Så här ställer du in slutpunkter på en klassisk virtuell Windows-dator) eller [ Attach a data disk to a Windows virtual machine created with the classic deployment model](./linux/attach-disk-portal.md) (Anslut en datadisk till en virtuell Windows-dator som skapats med den klassiska distributionsmodellen)|
| Migrering stöds inte för Virtual Network {VNet-Name} eftersom det har icke-Gateway-PaaS distributioner. | Det här felet uppstår när du har icke-Gateway-PaaS distributioner som Application Gateway eller API Management tjänster som är anslutna till Virtual Network.|


## <a name="detailed-mitigations"></a>Detaljerade åtgärder

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>En virtuell dator med en datadisk vars fysiska blobbstorlek i byte inte stämmer med datadiskens logiska storlek i byte.

Det här kan hända när datadiskens logiska storlek inte är synkroniserad med den faktiska blobbstorleken på den virtuella hårddisken. Det går lätt att kontrollera med följande kommandon:

#### <a name="verifying-the-issue"></a>Kontrollera problemet

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Åtgärda problemet

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Flytta en virtuell dator till en annan prenumeration efter slutförd migrering

Efter en migrering vill du kanske flytta den virtuella datorn till en annan prenumeration. Men om det finns en hemlighet eller ett certifikat på den virtuella datorn som hänvisar till en Key Vault-resurs, finns det för närvarande inte stöd för en sådan flytt. Anvisningarna nedan ger dig möjlighet att lösa problemet. 

#### <a name="powershell"></a>PowerShell

```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```


## <a name="next-steps"></a>Nästa steg

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md)