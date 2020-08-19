---
title: Azure Disk Encryption-scenarier på virtuella Windows-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Windows-datorer för olika scenarier
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 10e306d26ebfd5ffafe65d7aa52753e993b085bf
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509169"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-scenarier på virtuella Windows-datorer

Azure Disk Encryption för virtuella Windows-datorer (VM: ar) använder BitLocker-funktionen i Windows för att tillhandahålla fullständig disk kryptering för operativ system disken och data disken. Dessutom ger den en kryptering av den temporära disken när VolumeType-parametern är all.

Azure Disk Encryption är [integrerat med Azure Key Vault](disk-encryption-key-vault.md) för att hjälpa dig att styra och hantera disk krypterings nycklar och hemligheter. En översikt över tjänsten finns i [Azure Disk Encryption för virtuella Windows-datorer](disk-encryption-overview.md).

Du kan bara använda disk kryptering för virtuella datorer med [stödda VM-storlekar och operativ system](disk-encryption-overview.md#supported-vms-and-operating-systems). Du måste också uppfylla följande krav:

- [Nätverks krav](disk-encryption-overview.md#networking-requirements)
- [grupprincip krav](disk-encryption-overview.md#group-policy-requirements)
- [Lagrings krav för krypterings nyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) . 
>
> - Du bör [ta en ögonblicks bild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhets kopia innan diskarna krypteras. Säkerhets kopieringar säkerställer att ett återställnings alternativ är möjligt om ett oväntat fel uppstår under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhets kopia innan krypteringen utförs. När du har gjort en säkerhets kopia kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern-skipVmBackup. Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i [säkerhetskopiera och återställa krypterade virtuella Azure-datorer](../../backup/backup-azure-vms-encryption.md). 
>
> - Kryptering eller inaktivera kryptering kan leda till att en virtuell dator startas om.

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och Anslut till Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Aktivera kryptering på en befintlig eller Windows-VM som körs
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdletar eller CLI-kommandon. Om du behöver schema information för tillägget för virtuell dator kan du läsa artikeln [Azure Disk Encryption för Windows-tillägg](../extensions/azure-disk-enc-windows.md) .

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Aktivera kryptering på befintliga eller aktiva virtuella datorer med Azure PowerShell 
Använd cmdleten [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure. 

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension. Resurs gruppen, den virtuella datorn och nyckel valvet bör redan ha skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Kryptera en virtuell dator som körs med KEK:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

- **Kontrol lera att diskarna är krypterade:** Använd cmdleten [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) för att kontrol lera krypterings statusen för en virtuell IaaS-dator. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera disk kryptering:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Aktivera kryptering på befintliga eller aktiva virtuella datorer med Azure CLI
Använd kommandot [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure.

- **Kryptera en virtuell dator som körs:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Kryptera en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn] </br> Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

- **Kontrol lera att diskarna är krypterade:** Om du vill kontrol lera krypterings statusen för en virtuell IaaS-dator använder du kommandot [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Använda Resource Manager-mallen

Du kan aktivera disk kryptering på befintliga eller köra IaaS virtuella Windows-datorer i Azure med hjälp av [Resource Manager-mallen för att kryptera en Windows-VM som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. I mallen för Azure snabb start klickar **du på distribuera till Azure**.

2. Välj prenumeration, resurs grupp, plats, inställningar, juridiska villkor och avtal. Klicka på **köp** för att aktivera kryptering på den befintliga eller aktiva virtuella IaaS-datorn.

I följande tabell visas parametrarna för Resource Manager-mallen för befintliga eller aktiva virtuella datorer:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella dator som ska köra krypterings åtgärden. |
| keyVaultName | Namnet på nyckel valvet som BitLocker-nyckeln ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommandot `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Namnet på den resurs grupp som innehåller nyckel valvet|
|  keyEncryptionKeyURL | URL: en för nyckel krypterings nyckeln, i formatet https:// &lt; Key Vault-name &gt; . Vault.Azure.net/Key/ &lt; nyckel-name &gt; . Lämna fältet tomt om du inte vill använda en KEK. |
| volumeType | Typ av volym som krypterings åtgärden utförs på. Giltiga värden är _OS_, _data_och _alla_. 
| forceUpdateTag | Skicka ett unikt värde som ett GUID varje gång åtgärden måste tvingas köras. |
| resizeOSDisk | Vill du ändra storlek på operativ systemets partition så att den upptar full OS VHD innan du delar upp system volymen. |
| location | Platser för alla resurser. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Aktivera kryptering på NVMe-diskar för virtuella Lsv2-datorer

I det här scenariot beskrivs hur du aktiverar Azure Disk Encryption på NVMe-diskar för virtuella Lsv2-datorer.  Lsv2-seriens funktioner Local NVMe-lagring. Lokala NVMe-diskar är tillfälliga och data går förlorade på diskarna om du stoppar/frigör den virtuella datorn (se: [Lsv2-serien](../lsv2-series.md)).

Aktivera kryptering på NVMe-diskar:

1. Initiera NVMe-diskarna och skapa NTFS-volymer.
1. Aktivera kryptering på den virtuella datorn med parametern VolumeType inställd på alla. Detta aktiverar kryptering för alla operativ system och data diskar, inklusive volymer som backas upp av NVMe-diskar. Mer information finns i [Aktivera kryptering på en befintlig eller Windows-VM som körs](#enable-encryption-on-an-existing-or-running-windows-vm).

Krypteringen sparas på NVMe-diskarna i följande scenarier:
- Omstart av virtuell dator
- VMSS avbildning
- Växla OS

NVMe-diskar kommer att avinitieras i följande scenarier:

- Starta virtuell dator efter avallokering
- Tjänst återställning
- Backup

I dessa scenarier måste NVMe-diskarna initieras när den virtuella datorn har startats. Om du vill aktivera kryptering på NVMe-diskarna kör du kommandot för att aktivera Azure Disk Encryption igen när NVMe-diskarna har initierats.

Förutom scenarier som anges i avsnittet scenarier som [inte stöds](#unsupported-scenarios) , stöds inte kryptering av NVMe-diskar för:

- Virtuella datorer som krypterats med Azure Disk Encryption med AAD (tidigare version)
- NVMe-diskar med lagrings utrymmen
- Azure Site Recovery av SKU: er med NVMe-diskar (se [support mat ris för haveri beredskap för virtuella Azure-datorer mellan Azure-regioner: replikerade datorer-lagring](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nya virtuella IaaS-datorer som skapats från kund-krypterade VHD-och krypterings nycklar

I det här scenariot kan du skapa en ny virtuell dator från en förkrypterad virtuell hård disk och tillhör ande krypterings nycklar med hjälp av PowerShell-cmdletar eller CLI-kommandon. 

Följ anvisningarna i [förbereda en förkrypterad Windows-VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Kryptera virtuella datorer med förkrypterade virtuella hård diskar med Azure PowerShell
Du kan aktivera disk kryptering på din krypterade virtuella hård disk med PowerShell-cmdleten [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Exemplet nedan visar några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk till en virtuell Windows-dator med hjälp av PowerShell](attach-disk-ps.md), eller [via Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder PowerShell för att kryptera en ny disk för virtuella Windows-datorer ska en ny version av sekvens anges. Sekvens-versionen måste vara unik. Skriptet nedan genererar en GUID för sekvens-versionen. I vissa fall kan en nyligen tillagd datadisk krypteras automatiskt av Azure Disk Encryption tillägget. Automatisk kryptering sker vanligt vis när den virtuella datorn startas om efter att den nya disken är online. Detta beror vanligt vis på att "alla" angavs för volym typen när disk kryptering tidigare kördes på den virtuella datorn. Om automatisk kryptering sker på en nyligen tillagd datadisk rekommenderar vi att du kör cmdleten Set-AzVmDiskEncryptionExtension igen med en ny sekvens-version. Om den nya datadisken krypteras automatiskt och du inte vill vara krypterad dekrypterar du alla enheter och krypterar först om med en ny sekvens version som anger OS som typ av volym. 
  
 

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension. Resurs gruppen, den virtuella datorn och nyckel valvet bör redan ha skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. I det här exemplet används "alla" för parametern-VolumeType, som innehåller både OS-och data volymer. Om du bara vill kryptera operativ system volymen använder du "OS" för parametern-VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Kryptera en virtuell dator som körs med KEK:** I det här exemplet används "alla" för parametern-VolumeType, som innehåller både OS-och data volymer. Om du bara vill kryptera operativ system volymen använder du "OS" för parametern-VolumeType.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Azure CLI-kommandot kommer automatiskt att tillhandahålla en ny version av sekvensen när du kör kommandot för att aktivera kryptering. Exemplet använder "alla" för volym typ parametern. Du kan behöva ändra volym typs parametern till OS om du bara krypterar OS-disken. Till skillnad från PowerShell-syntaxen kräver CLI inte att användaren anger en unik sekvens-version när krypteringen aktive ras. CLI genererar automatiskt och använder sitt eget unika sekvens versions värde.   

-  **Kryptera en virtuell dator som körs:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Kryptera en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Inaktivera kryptering
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Azure Disk Encryption fungerar inte för följande scenarier, funktioner och teknik:

- Kryptering av virtuella datorer på Basic-nivå eller virtuella datorer som skapats via den klassiska skapande metoden för virtuella datorer.
- Kryptera virtuella datorer som kon figurer ATS med programvarubaserade RAID-system.
- Kryptering av virtuella datorer som kon figurer ATS med Lagringsdirigering (S2D) eller Windows Server-versioner innan 2016 har kon figurer ATS med Windows lagrings utrymmen.
- Integrering med ett lokalt nyckel hanterings system.
- Azure Files (delat fil system).
- NFS (Network File System).
- Dynamiska volymer.
- Windows Server-behållare, som skapar dynamiska volymer för varje behållare.
- Tillfälliga OS-diskar.
- Kryptering av delade/distribuerade fil system som (men inte begränsat till) DFS, GFS, DRDB och CephFS.
- Flytta en krypterad virtuell dator till en annan prenumeration eller region.
- Skapa en avbildning eller ögonblicks bild av en krypterad virtuell dator och använda den för att distribuera ytterligare virtuella datorer.
- Virtuella Gen2-datorer (se: [stöd för virtuella datorer i generation 2 på Azure](generation-2.md#generation-1-vs-generation-2-capabilities))
- Virtuella datorer i M-serien med Skrivningsaccelerator diskar.
- Använda ADE på en virtuell dator som har eller *någonsin* haft diskar som är krypterade med [kryptering på Server sidan med Kundhanterade nycklar](disk-encryption.md) (SSE + CMK). Att använda SSE + CMK till en datadisk på en virtuell dator som är krypterad med ADE är ett scenario som inte stöds.
- Migrera en virtuell dator som har krypterats med ADE till [kryptering på Server sidan med Kundhanterade nycklar](disk-encryption.md).


## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
- [Exempelskript för Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
