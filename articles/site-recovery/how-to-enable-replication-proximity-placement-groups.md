---
title: Replikera virtuella Azure-datorer som körs i närhets placerings grupper
description: Lär dig hur du replikerar virtuella Azure-datorer som körs i närhets placerings grupper med hjälp av Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 681b635099d450f061e0bcdb5b2c5d60d56c20a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380780"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replikera virtuella Azure-datorer som körs i närhetsplaceringsgrupper till en annan region

Den här artikeln beskriver hur du replikerar, redundans och återställning efter fel för virtuella datorer som körs i en närhets placerings grupp till en sekundär region.

[Närhets placerings grupper](../virtual-machines/windows/proximity-placement-groups-portal.md) är en logisk grupperings funktion för virtuella Azure-datorer som du kan använda för att minska svars tiden mellan virtuella datorer som är kopplad till dina program. När de virtuella datorerna distribueras inom samma närhets placerings grupp, är de fysiskt placerade så nära varandra som möjligt. Placerings grupper för närhet är särskilt användbara för att tillgodose kraven för svars känsliga arbets belastningar.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Haveri beredskap med närhets placerings grupper

I ett typiskt scenario kan du ha dina virtuella datorer som körs i en närhets grupp för att undvika nätverks fördröjningen mellan olika nivåer av ditt program. Detta kan ge din program optimala nätverks fördröjning, men du vill skydda dessa program med hjälp av Site Recovery för ett region nivå haveri. Site Recovery replikerar data från en region till en annan Azure-region och tar upp datorer i Disaster Recovery-regionen i en händelse av redundans.

## <a name="considerations"></a>Överväganden

- Det bästa arbetet är att redundansväxla/redundansväxla de virtuella datorerna till en närhets placerings grupp. Men om den virtuella datorn inte kan hamna i närheten vid växling vid fel/återställning efter fel, kommer redundansväxlingen/failback fortfarande att ske, och virtuella datorer skapas utanför en närhets placerings grupp.
- Om en tillgänglighets uppsättning fästs på en närhets placerings grupp och under redundansväxling/failback-datorer i tillgänglighets uppsättningen har en tilldelnings begränsning skapas de virtuella datorerna utanför både tillgänglighets uppsättningen och närhets placerings gruppen.
- Site Recovery för närhets placerings grupper stöds inte för ohanterade diskar.

> [!NOTE]
> Azure Site Recovery har inte stöd för återställning efter fel från hanterade diskar för Hyper-V till Azure-scenarier. Därför stöds inte failback från närhets placerings gruppen i Azure till Hyper-V.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-portal"></a>Konfigurera katastrof återställning för virtuella datorer i närhets placerings grupper via portalen

### <a name="azure-to-azure-via-portal"></a>Azure till Azure via portalen

Du kan välja att aktivera replikering för en virtuell dator via sidan för haveri beredskap för virtuella datorer eller genom att gå till ett förskapat valv och navigera till Site Recovery avsnittet och sedan aktivera replikering. Nu ska vi titta på hur Site Recovery kan konfigureras för virtuella datorer i en PPG genom båda metoderna:

- Så här väljer du PPG i DR-regionen samtidigt som du aktiverar replikering via det virtuella IaaS-bladet för VM:
  1. Gå till den virtuella datorn. På bladet till vänster, under "åtgärder" väljer du "haveri beredskap"
  2. På fliken "grunder" väljer du den DR-region som du vill replikera den virtuella datorn till. Gå till avancerade inställningar
  3. Här kan du se placerings gruppen för den virtuella datorn och välja en PPG i DR-regionen. Site Recovery ger dig också möjlighet att använda en ny närhets grupp som skapas åt dig om du väljer att använda det här standard alternativet. Du är kostnads fri att välja önskad närhets grupp och sedan gå till "granska + starta replikering" och slutligen aktivera replikering.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="Aktivera replikering.":::

- Så här väljer du PPG i DR-regionen när du aktiverar replikering via valv bladet:
  1. Gå till Recovery Services valvet och gå till fliken Site Recovery
  2. Klicka på "+ Aktivera Site Recovery" och välj sedan ' 1: Aktivera replikering ' under Azure Virtual Machines (som du vill replikera en virtuell Azure-dator)
  3. Fyll i de obligatoriska fälten på fliken Källa och klicka på nästa
  4. Välj listan över virtuella datorer som du vill aktivera replikering för på fliken "virtuella datorer" och klicka på "Nästa"
  5. Här kan du se alternativet för att välja en PPG i DR-regionen. Site Recovery ger dig också möjlighet att använda en ny PPG som skapas för dig om du väljer att använda det här standard alternativet. Du kan välja de PPG som du vill använda och sedan fortsätta att aktivera replikering.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="Aktivera replikering via valv.":::

Observera att du enkelt kan uppdatera PPG-valet i DR-regionen när replikering har Aktiver ATS för den virtuella datorn.

1. Gå till den virtuella datorn och på bladet till vänster, under "åtgärder" väljer du haveri beredskap
2. Gå till bladet beräkning och nätverk och klicka på Redigera överst på sidan
3. Du kan se alternativen för att redigera flera mål inställningar, inklusive mål PPG. Välj den PPG som du vill att den virtuella datorn ska redundansväxla till och klicka på Spara.

### <a name="vmware-to-azure-via-portal"></a>VMware till Azure via portalen

Närhets placerings gruppen för den virtuella mål datorn kan konfigureras när replikeringen för den virtuella datorn har Aktiver ATS. Se till att du måste skapa PPG i mål regionen separat enligt ditt krav. Därefter kan du enkelt uppdatera PPG-valet i DR-regionen när replikering har Aktiver ATS för den virtuella datorn.

1. Välj den virtuella datorn från valvet och välj haveri beredskap under "åtgärder" på bladet till vänster.
2. Gå till bladet beräkning och nätverk och klicka på Redigera överst på sidan
3. Du kan se alternativen för att redigera flera mål inställningar, inklusive mål PPG. Välj den PPG som du vill att den virtuella datorn ska redundansväxla till och klicka på Spara.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="Uppdatera PPG V2A":::

### <a name="hyper-v-to-azure-via-portal"></a>Hyper-V till Azure via portalen

Närhets placerings gruppen för den virtuella mål datorn kan konfigureras när replikeringen för den virtuella datorn har Aktiver ATS. Se till att du måste skapa PPG i mål regionen separat enligt ditt krav. Därefter kan du enkelt uppdatera PPG-valet i DR-regionen när replikering har Aktiver ATS för den virtuella datorn.

1. Välj den virtuella datorn från valvet och välj haveri beredskap under "åtgärder" på bladet till vänster.
2. Gå till bladet beräkning och nätverk och klicka på Redigera överst på sidan
3. Du kan se alternativen för att redigera flera mål inställningar, inklusive mål PPG. Välj den PPG som du vill att den virtuella datorn ska redundansväxla till och klicka på Spara.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="Uppdatera PPG H2A":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>Konfigurera katastrof återställning för virtuella datorer i närhets placerings grupper via PowerShell

### <a name="prerequisites"></a>Förutsättningar 

1. Kontrol lera att du har modulen Azure PowerShell AZ. Om du behöver installera eller uppgradera Azure PowerShell, följ den här [guiden för att installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).
2. Den minsta Azure PowerShell AZ-versionen ska vara 4.1.0. Om du vill kontrol lera den aktuella versionen använder du kommandot nedan.

    ```
    Get-InstalledModule -Name Az
    ```

### <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Konfigurera Site Recovery för Virtual Machines i närhets placerings grupp

> [!NOTE]
> Kontrol lera att du har det unika ID: t för målets närhets placering. Om du skapar en ny närhets grupp, kontrollerar du kommandot [här](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) och om du använder en befintlig närhets placerings grupp använder du kommandot [här](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups).

### <a name="azure-to-azure"></a>Azure till Azure

1. [Logga in](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) på ditt konto och ange din prenumeration.
2. Hämta information om den virtuella dator som du planerar att replikera enligt vad som anges [här](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated).
3. [Skapa](./azure-to-azure-powershell.md#create-a-recovery-services-vault) Recovery Services-valvet och [Ange](./azure-to-azure-powershell.md#set-the-vault-context) valv kontexten.
4. Förbered valvet för att starta replikering av den virtuella datorn. Detta innebär att du skapar ett [Service Fabric-objekt](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) för både primär-och återställnings regioner.
5. [Skapa](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) en Site Recovery skydds behållare för både den primära och återställnings infrastrukturen.
6. [Skapa](./azure-to-azure-powershell.md#create-a-replication-policy) en replikeringsprincip.
7. Skapa en skydds container mappning mellan primär-och återställnings skydds behållare med hjälp av [de här](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) stegen och [](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)en skydds behållar mappning för återställning efter fel
8. Skapa ett lagrings konto för cache genom att följa [dessa](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) steg.
9. Skapa de nätverks mappningar som krävs enligt vad som anges [här](./azure-to-azure-powershell.md#create-network-mappings).
10. Om du vill replikera en virtuell Azure-dator med Managed disks använder du PowerShell-cmdleten nedan.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

När du aktiverar replikering för flera data diskar använder du PowerShell-cmdleten nedan.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

$diskconfigs = @()
$diskconfigs.Add($OSDiskReplicationConfig)

#Data disk

# Add data disks
Foreach( $disk in $VM.StorageProfile.DataDisks)
{
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $dataDiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
    $diskconfigs.Add($DataDisk1ReplicationConfig)
}

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

När du aktiverar zon-till-zon-replikering med PPG, kommer kommandot att starta replikeringen att utbytas med PowerShell-cmdleten –

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

När åtgärden starta replikeringen har slutförts replikeras data för virtuella datorer till återställnings regionen.

Replikeringen börjar genom att initiera en kopia av de replikerade diskarna för den virtuella datorn i återställnings regionen. Den här fasen kallas för fasen inledande replikering.

När den inledande replikeringen har slutförts flyttas replikeringen till fasen differentiell synkronisering. Den virtuella datorn är nu skyddad och en redundanstest kan utföras på den. Replikeringstillståndet för det replikerade objektet som representerar den virtuella datorn går till skyddat tillstånd när den inledande replikeringen har slutförts.

Övervaka replikeringstillståndet och replikeringens hälso tillstånd för den virtuella datorn genom att hämta information om det skyddade replikerings objekt som motsvarar det.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Följ [dessa](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) steg om du vill göra ett redundanstest, validera och rensa redundanstest.
12. Om du vill redundansväxla följer du stegen som anges [här](./azure-to-azure-powershell.md#fail-over-to-azure).
13. Om du vill återaktivera skyddet och återställning efter fel till käll regionen använder du PowerShell-cmdleten nedan –

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. Om du vill inaktivera replikering följer du stegen [här](./azure-to-azure-powershell.md#disable-replication).

### <a name="vmware-to-azure-via-powershell"></a>VMware till Azure via PowerShell

1. Se till att [förbereda dina lokala VMware-servrar](./vmware-azure-tutorial-prepare-on-premises.md) för haveri beredskap till Azure.
2. Logga in på ditt konto och Ställ in din prenumeration enligt vad som anges [här](./vmware-azure-disaster-recovery-powershell.md#log-into-azure).
3. [Konfigurera ett Recovery Services](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) valv och [Ange valv kontext](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. [Verifiera](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) din valv registrering.
5. [Skapa](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) en replikeringsprincip.
6. [Lägg till](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) en vCenter-Server och identifiera virtuella datorer och [skapa](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) lagrings konton för replikering.
7. Om du vill replikera VMware-Virtual Machines läser du informationen här och följer PowerShell-cmdleten nedan –

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. Du kan kontrol lera replikeringstillståndet och replikeringens hälso tillstånd för den virtuella datorn med cmdleten Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. Konfigurera inställningarna för redundans genom att följa stegen [här](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [Kör](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) ett redundanstest.
11. Redundansväxla till Azure med hjälp av [de här](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) stegen.

### <a name="hyper-v-to-azure-via-powershell"></a>Hyper-V till Azure via PowerShell

1. Se till att [förbereda dina lokala Hyper-V-servrar](./hyper-v-prepare-on-premises-tutorial.md) för haveri beredskap till Azure.
2. [Logga in](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) på Azure.
3. [Konfigurera](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) valvet och [Ange](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) Recovery Services valv kontext.
4. [Skapa](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) en Hyper-V-plats.
5. [Installera](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) providern och agenten.
6. [Skapa](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) en replikeringsprincip.
7. Aktivera replikering med hjälp av stegen nedan – 
    
    a. Hämta det skydds bara objekt som motsvarar den virtuella dator som du vill skydda, enligt följande:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Skydda den virtuella datorn. Om den virtuella dator som du skyddar har fler än en disk ansluten, anger du operativ system disken med hjälp av parametern OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. Vänta tills de virtuella datorerna når ett skyddat tillstånd efter den inledande replikeringen. Detta kan ta en stund, beroende på faktorer som mängden data som ska replikeras och den tillgängliga överordnade bandbredden till Azure. När ett skyddat tillstånd är på plats uppdateras jobbets tillstånd och StateDescription enligt följande: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Uppdatera återställnings egenskaper (till exempel storleken på den virtuella dator rollen) och det Azure-nätverk som du vill ansluta VM-NÄTVERKSKORTet till efter redundansväxlingen.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Kör ett redundanstest [.](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)


## <a name="next-steps"></a>Nästa steg

Följ stegen som beskrivs [här](./vmware-azure-prepare-failback.md)om du vill utföra skydd och återställning efter fel för VMware till Azure.

Om du vill utföra redundans för Hyper-V till Azure följer du stegen som beskrivs [här](./site-recovery-failover.md) och utför återställning efter fel genom att följa stegen som beskrivs [här](./hyper-v-azure-failback.md).

Mer information finns i [redundansväxlingen i Site Recovery](site-recovery-failover.md).