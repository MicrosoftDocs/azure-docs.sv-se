---
title: Konfigurera VMware haveri beredskap med PowerShell på Azure Site Revoery
description: Lär dig hur du konfigurerar replikering och redundans till Azure för haveri beredskap för virtuella VMware-datorer med hjälp av PowerShell i Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: de25a3f9df04b09a7337dc889a688a171d98db28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129903"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Konfigurera haveriberedskap för virtuella VMware-datorer till Azure med PowerShell

I den här artikeln får du se hur du replikerar och redundansväxlas virtuella VMware-datorer till Azure med hjälp av Azure PowerShell.

Lär dig att:

> [!div class="checklist"]
> - Skapa ett Recovery Services valv och ange valv kontexten.
> - Verifiera Server registrering i valvet.
> - Konfigurera replikering, inklusive en replikeringsprincip. Lägg till vCenter-servern och identifiera virtuella datorer.
> - Lägg till en vCenter-Server och identifiera
> - Skapa lagrings konton för att lagra loggar eller data och replikera de virtuella datorerna.
> - Utför en redundans. Konfigurera inställningar för redundans, utför inställningar för replikering av virtuella datorer.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](vmware-azure-architecture.md).
- Granska [support kraven](./vmware-physical-azure-support-matrix.md) för alla komponenter.
- Du har Azure PowerShell- `Az`  modulen. Om du behöver installera eller uppgradera Azure PowerShell, följ den här [guiden för att installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med hjälp av Connect-AzAccount cmdlet:

```azurepowershell
Connect-AzAccount
```
Välj den Azure-prenumeration som du vill replikera virtuella VMware-datorer till. Använd Get-AzSubscription-cmdlet för att hämta listan över Azure-prenumerationer som du har åtkomst till. Välj den Azure-prenumeration som du vill arbeta med med hjälp av Select-AzSubscription cmdlet.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

1. Skapa en resurs grupp där du kan skapa Recovery Servicess valvet. I exemplet nedan heter resurs gruppen VMwareDRtoAzurePS och skapas i den Asien, östra regionen.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Skapa ett Recovery Services-valv. I exemplet nedan heter Recovery Services-valvet VMwareDRToAzurePs och skapas i Asien, östra region och i resurs gruppen som skapades i föregående steg.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Ladda ned valv registrerings nyckeln för valvet. Valv registrerings nyckeln används för att registrera den lokala konfigurations servern för det här valvet. Registreringen är en del av installations processen för konfigurations serverprogram vara.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Använd den hämtade valv registrerings nyckeln och följ stegen i artiklarna nedan för att slutföra installationen och registreringen av konfigurations servern.
   - [Välj skydds mål](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Konfigurera källmiljön](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Ange valv kontext

Ange valv kontexten med hjälp av Set-ASRVaultContext-cmdleten. När du har angett utförs efterföljande Azure Site Recovery åtgärder i PowerShell-sessionen i kontexten för det valda valvet.

> [!TIP]
> Azure Site Recovery PowerShell-modulen (AZ. RecoveryServices Module) har lätt att använda alias för de flesta cmdletar. Cmdletarna i modulen har formatet *\<Operation> - **AzRecoveryServicesAsr** \<Object>* och har motsvarande alias som gör att formatet *\<Operation> - **ASR** \<Object>* fungerar. Du kan ersätta cmdlet-aliasen för enkel användning.

I exemplet nedan används valv informationen från variabeln $vault för att ange valv kontexten för PowerShell-sessionen.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Som ett alternativ till Set-ASRVaultContext-cmdlet: en kan också använda cmdleten Import-AzRecoveryServicesAsrVaultSettingsFile för att ange valv kontexten. Ange den sökväg där valv registrerings nyckel filen finns som parametern-Path i Import-AzRecoveryServicesAsrVaultSettingsFile-cmdleten. Exempel:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Efterföljande avsnitt i den här artikeln förutsätter att valv kontexten för Azure Site Recovery åtgärder har ställts in.

## <a name="validate-vault-registration"></a>Verifiera valv registrering

I det här exemplet har vi följande:

- En konfigurations Server (**ConfigurationServer**) har registrerats på det här valvet.
- En ytterligare processerver (**skalnings-ProcessServer**) har registrerats på *ConfigurationServer*
- Konton (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) har kon figurer ATS på konfigurations servern. Dessa konton används för att lägga till vCenter-servern, för att identifiera virtuella datorer och för att push-installera mobilitets tjänstens program vara på Windows-och Linux-servrar som ska replikeras.

1. Registrerade konfigurations servrar representeras av ett Fabric-objekt i Site Recovery. Hämta listan över infrastruktur objekt i valvet och identifiera konfigurations servern.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Identifiera de process servrar som kan användas för att replikera datorer.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   Från utdata ovan ***$ProcessServers [0]** _ motsvarar _skalnings-ProcessServer * och ***$ProcessServers [1]**_ motsvarar process Server rollen på _ConfigurationServer *

3. Identifiera konton som har kon figurer ATS på konfigurations servern.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   Från utdata ovan ***$AccountHandles [0]** _ motsvarar konto _vCenter_account *, ***$AccountHandles [1]**_ för kontot _WindowsAccount * och ***$AccountHandles [2]**_ till konto _LinuxAccount *

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

I det här steget skapas två principer för replikering. En princip för att replikera virtuella VMware-datorer till Azure och den andra att replikera misslyckade över virtuella datorer som körs i Azure tillbaka till den lokala VMware-platsen.

> [!NOTE]
> De flesta Azure Site Recovery åtgärder körs asynkront. När du startar en åtgärd skickas ett Azure Site Recovery jobb och ett jobb spårnings objekt returneras. Detta jobb spårnings objekt kan användas för att övervaka status för åtgärden.

1. Skapa en replikeringsprincip med namnet *ReplicationPolicy* för att replikera virtuella VMware-datorer till Azure med de angivna egenskaperna.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Skapa en replikeringsprincip som ska användas för återställning efter fel från Azure till den lokala VMware-platsen.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Använd jobb informationen i *$Job _FailbackPolicyCreate* för att spåra åtgärden till slutförd.

   * Skapa en skydds container mappning för att mappa replikeringsprinciper med konfigurations servern.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Lägg till en vCenter-Server och identifiera virtuella datorer

Lägg till en vCenter Server med IP-adress eller värdnamn. Parametern **-port** anger porten på den vCenter-Server som du vill ansluta till, **-Name-** parametern anger ett eget namn som ska användas för vCenter-servern och parametern  **-Account** anger den konto referens på konfigurations servern som ska användas för att identifiera virtuella datorer som hanteras av vCenter-servern.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Skapa lagrings konton för replikering

**Om du vill skriva till hanterad disk använder du [PowerShell-AZ. RecoveryServices module 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) och senare.** Du behöver bara skapa ett logg lagrings konto. Vi rekommenderar att du använder en standard konto typ och LRS-redundans eftersom det används för att endast lagra tillfälliga loggar. Se till att lagrings kontot har skapats i samma Azure-region som valvet.

Om du använder en version av AZ. RecoveryServices som är äldre än 2.0.0 använder du följande steg för att skapa lagrings konton. Dessa lagrings konton används senare för att replikera virtuella datorer. Se till att lagrings kontona skapas i samma Azure-region som valvet. Du kan hoppa över det här steget om du planerar att använda ett befintligt lagrings konto för replikering.

> [!NOTE]
> När du replikerar lokala virtuella datorer till ett Premium Storage-konto måste du ange ett ytterligare standard lagrings konto (logg lagrings konto). Logg lagrings kontot innehåller loggar som mellanliggande lagring tills loggarna kan tillämpas på Premium Storage-målet.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replikera virtuella VMware-datorer

Det tar ungefär 15-20 minuter för virtuella datorer att identifieras från vCenter-servern. När ett objekt med skydds objekt har identifierats skapas det i Azure Site Recovery för varje identifierad virtuell dator. I det här steget replikeras tre av de identifierade virtuella datorerna till de Azure Storage konton som skapades i föregående steg.

Du behöver följande information för att skydda en identifierad virtuell dator:

* Det skydds bara objekt som ska replikeras.
* Lagrings kontot för att replikera den virtuella datorn till (endast om du replikerar till lagrings kontot). 
* En logg lagring krävs för att skydda virtuella datorer till ett Premium Storage-konto eller till en hanterad disk.
* Den Processerver som ska användas för replikering. Listan över tillgängliga process servrar har hämtats och sparats i ***$ProcessServers [0]** _ _(Scale-ProcessServer) * och ***$ProcessServers [1]**_ _ (ConfigurationServer) * variabler.
* Det konto som ska användas för push-installation av mobilitets tjänst programmet på datorerna. Listan över tillgängliga konton har hämtats och lagrats i ***$AccountHandles*** -variabeln.
* Skydds behållar mappningen för den replikeringsprincip som ska användas för replikering.
* Resurs gruppen där de virtuella datorerna måste skapas vid redundans.
* Det virtuella Azure-nätverket och under nätet som den misslyckade över virtuella datorn ska vara anslutet till kan du välja mellan.

Replikera nu följande virtuella datorer med de inställningar som anges i den här tabellen


|Virtuell dator  |Processerver        |Lagringskonto              |Logg lagrings konto  |Policy           |Konto för mobilitets tjänst installation|Målresursgrupp  | Virtuellt mål nätverk  |Mål under nät  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |Ej tillämpligt| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR – VNet                 |Undernät-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR – VNet                 |Undernät-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| Ej tillämpligt                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR – VNet                 |Undernät-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

När jobbet för att aktivera replikering har slutförts startas den inledande replikeringen för de virtuella datorerna. Den inledande replikeringen kan ta en stund, beroende på hur mycket data som ska replikeras och vilken bandbredd som är tillgänglig för replikering. När den inledande replikeringen har slutförts flyttas den virtuella datorn till ett skyddat tillstånd. När den virtuella datorn når ett skyddat tillstånd kan du utföra en redundanstest för den virtuella datorn, lägga till den i återställnings planer osv.

Du kan kontrol lera replikeringstillståndet och replikeringens hälso tillstånd för den virtuella datorn med cmdleten Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Konfigurera inställningar för redundans

Inställningarna för redundans för skyddade datorer kan uppdateras med hjälp av Set-ASRReplicationProtectedItem-cmdleten. Några av de inställningar som kan uppdateras med denna cmdlet är:
* Namnet på den virtuella dator som ska skapas vid redundans
* VM-storlek för den virtuella dator som ska skapas vid redundans
* Virtuellt Azure-nätverk och undernät som nätverkskorten på den virtuella datorn ska anslutas till vid redundans
* Redundans till hanterade diskar
* Använd Azure Hybrid Use-förmånen
* Tilldela en statisk IP-adress från det virtuella mål nätverket som ska tilldelas den virtuella datorn vid redundansväxling.

I det här exemplet uppdaterar vi VM-storleken på den virtuella datorn som ska skapas vid redundans för den virtuella datorn *Win2K12VM1* och anger att den virtuella datorn använder hanterade diskar vid redundansväxling.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. Kör en DR-granskning (redundanstest) enligt följande:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. När redundanstestning har slutförts kan du se att en virtuell dator med suffixet *"-test"* (Win2K12VM1-test i det här fallet) har skapats i Azure.
3. Nu kan du ansluta till testet misslyckades över den virtuella datorn och verifiera redundanstest.
4. Rensa redundanstestningen med hjälp av Start-ASRTestFailoverCleanupJob cmdlet. Den här åtgärden tar bort den virtuella dator som skapats som en del av redundanstest.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Redundansväxla till Azure

I det här steget växlar vi över den virtuella datorns Win2K12VM1 till en viss återställnings punkt.

1. Hämta en lista över tillgängliga återställnings punkter som ska användas för redundansväxlingen:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. När åtgärden har misslyckats kan du genomföra redundansväxlingen och konfigurera omvänd replikering från Azure tillbaka till den lokala VMware-platsen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du automatiserar fler uppgifter med hjälp av [Azure Site Recovery PowerShell-referens](/powershell/module/Az.RecoveryServices).
