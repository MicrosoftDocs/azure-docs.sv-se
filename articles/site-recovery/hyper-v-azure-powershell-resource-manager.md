---
title: Haveri beredskap för virtuella Hyper-V-datorer med Azure Site Recovery och PowerShell
description: Automatisera haveri beredskap för virtuella Hyper-V-datorer till Azure med Azure Site Recovery tjänsten med PowerShell och Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4140a0b22f7ca8164d50cf60fe57c861f826eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132512"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Konfigurera haveriberedskap till Azure för virtuella Hyper-V-datorer med PowerShell och Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR) genom att dirigera replikering, redundans och återställning av virtuella Azure-datorer (VM) och lokala virtuella datorer och fysiska servrar.

Den här artikeln beskriver hur du använder Windows PowerShell tillsammans med Azure Resource Manager för att replikera virtuella Hyper-V-datorer till Azure. Exemplet som används i den här artikeln visar hur du replikerar en enskild virtuell dator som körs på en Hyper-V-värd till Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell tillhandahåller cmdlets för att hantera Azure med hjälp av Windows PowerShell. Site Recovery PowerShell-cmdlet: ar som är tillgängliga med Azure PowerShell för Azure Resource Manager hjälper dig att skydda och återställa dina servrar i Azure.

Du behöver inte vara en PowerShell-expert för att använda den här artikeln, men du måste förstå grundläggande koncept, till exempel moduler, cmdletar och sessioner. Mer information finns i PowerShell- [dokumentationen](/powershell) och [använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

> [!NOTE]
> Microsoft-partner i ett CSP-program (Cloud Solution Provider) kan konfigurera och hantera skydd av kund servrar för sina respektive CSP-prenumerationer (klient prenumerationer).

## <a name="before-you-start"></a>Innan du börjar

Kontrol lera att du har dessa krav på plats:

- Ett [Microsoft Azure](https://azure.microsoft.com/) konto. Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). Dessutom kan du läsa om priser för [Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell. Information om den här versionen och hur du installerar den finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

Dessutom har det särskilda exemplet som beskrivs i den här artikeln följande krav:

- En Hyper-V-värd som kör Windows Server 2012 R2 eller Microsoft Hyper-V Server 2012 R2 och som innehåller en eller flera virtuella datorer. Hyper-V-servrar bör vara anslutna till Internet, antingen direkt eller via en proxyserver.
- De virtuella datorer som du vill replikera bör uppfylla [dessa krav](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Steg 1: Logga in på ditt Azure-konto

1. Öppna en PowerShell-konsol och kör det här kommandot för att logga in på ditt Azure-konto. Cmdleten hämtar en webb sida där du uppmanas att ange dina autentiseringsuppgifter för ditt konto: `Connect-AzAccount` .
   - Alternativt kan du inkludera dina konto uppgifter som en parameter i `Connect-AzAccount` cmdleten med hjälp av parametern **Credential** .
   - Om du är en CSP-partner som arbetar för en klient kan du ange kunden som en klient, genom att använda sitt tenantID eller klientens primära domän namn. Exempelvis: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Associera den prenumeration som du vill använda med kontot, eftersom ett konto kan ha flera prenumerationer:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Kontrol lera att din prenumeration har registrerats för användning av Azure-providers för Recovery Services och Site Recovery med följande kommandon:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Kontrol lera att **RegistrationState** är inställt på **registrerad** i kommandot utdata. du kan gå vidare till steg 2. Annars bör du registrera den saknade providern i din prenumeration genom att köra följande kommandon:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Verifiera att providern har registrerats korrekt med hjälp av följande kommandon:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Steg 2: Konfigurera valvet

1. Skapa en Azure Resource Manager resurs grupp där valvet ska skapas, eller Använd en befintlig resurs grupp. Skapa en ny resurs grupp på följande sätt. `$ResourceGroupName`Variabeln innehåller namnet på den resurs grupp som du vill skapa och variabeln $geo innehåller den Azure-region där du vill skapa resurs gruppen (till exempel "Brasilien, södra").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Kör cmdleten om du vill hämta en lista över resurs grupper i din prenumeration `Get-AzResourceGroup` .
1. Skapa ett nytt Azure Recovery Services-valv på följande sätt:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Du kan hämta en lista över befintliga valv med `Get-AzRecoveryServicesVault` cmdleten.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Steg 3: Ange kontexten för Recovery Services valvet

Ange valv kontexten enligt följande:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Steg 4: skapa en Hyper-V-plats

1. Skapa en ny Hyper-V-plats på följande sätt:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Den här cmdleten startar ett Site Recovery jobb för att skapa platsen och returnerar ett Site Recovery Job-objekt. Vänta tills jobbet har slutförts och kontrol lera att jobbet har slutförts.
1. Använd `Get-AzRecoveryServicesAsrJob` cmdleten för att hämta jobbobjektet och kontrol lera jobbets aktuella status.
1. Skapa och ladda ned en registrerings nyckel för platsen enligt följande:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Kopiera den nedladdade nyckeln till Hyper-V-värden. Du behöver nyckeln för att registrera Hyper-V-värden på platsen.

## <a name="step-5-install-the-provider-and-agent"></a>Steg 5: installera providern och agenten

1. Hämta installations programmet för den senaste versionen av providern från [Microsoft](https://aka.ms/downloaddra).
1. Kör installations programmet på Hyper-V-värden.
1. I slutet av installationen fortsätter du till registrerings steget.
1. När du uppmanas till det anger du den hämtade nyckeln och slutför registreringen av Hyper-V-värden.
1. Kontrol lera att Hyper-V-värden är registrerad på platsen enligt följande:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Om du kör en Hyper-V Core-server laddar du ned installations filen och följer de här stegen:

1. Extrahera filerna från _AzureSiteRecoveryProvider.exe_ till en lokal katalog genom att köra det här kommandot:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Kör följande kommando:

   ```console
   .\setupdr.exe /i
   ```

   Resultaten loggas till _%programdata%\ASRLogs\DRASetupWizard.log_.

1. Registrera servern genom att köra det här kommandot:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Steg 6: skapa en replikeringsprincip

Innan du börjar bör det angivna lagrings kontot finnas i samma Azure-region som valvet och ha geo-replikering aktiverat.

1. Skapa en replikeringsprincip enligt följande:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Kontrol lera det returnerade jobbet för att se till att replikeringsprincipen har skapats.

1. Hämta skydds behållaren som motsvarar platsen, enligt följande:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Koppla skydds containern till replikeringsprincipen enligt följande:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Vänta tills Associations jobbet har slutförts.

1. Hämta skydds behållar mappningen.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Steg 7: Aktivera VM-skydd

1. Hämta det skydds bara objekt som motsvarar den virtuella dator som du vill skydda, enligt följande:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Skydda den virtuella datorn. Om den virtuella dator som du skyddar har fler än en disk ansluten, anger du operativ system disken med hjälp av parametern **OSDiskName** .

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Vänta tills de virtuella datorerna når ett skyddat tillstånd efter den inledande replikeringen. Detta kan ta en stund, beroende på faktorer som mängden data som ska replikeras och den tillgängliga överordnade bandbredden till Azure. När ett skyddat tillstånd är på plats uppdateras jobbets tillstånd och StateDescription enligt följande:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Uppdatera återställnings egenskaper (till exempel storleken på den virtuella dator rollen) och det Azure-nätverk som du vill ansluta VM-NÄTVERKSKORTet till efter redundansväxlingen.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Om du vill replikera till CMK-aktiverade hanterade diskar i Azure utför du följande steg med AZ PowerShell 3.3.0 och senare:
>
> 1. Aktivera redundans till hanterade diskar genom att uppdatera VM-egenskaper
> 1. Använd `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdleten för att hämta disk-ID: t för varje disk i det skyddade objektet
> 1. Skapa ett Dictionary-objekt med hjälp av en `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet som innehåller mappningen av disk-ID till disk krypterings uppsättning. De här disk krypterings uppsättningarna skapas i förväg av dig i mål regionen.
> 1. Uppdatera VM-egenskaperna med `Set-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet genom att skicka Dictionary-objektet i **DiskIdToDiskEncryptionSetMap** -parametern.

## <a name="step-8-run-a-test-failover"></a>Steg 8: köra ett redundanstest

1. Kör ett redundanstest enligt följande:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Kontrol lera att den virtuella test datorn har skapats i Azure. Jobbet för redundanstest pausas när du har skapat den virtuella test datorn i Azure.
1. Om du vill rensa och slutföra redundanstestningen kör du:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Nästa steg

[Läs mer](/powershell/module/az.recoveryservices) om Azure Site Recovery med Azure Resource Manager PowerShell-cmdletar.
