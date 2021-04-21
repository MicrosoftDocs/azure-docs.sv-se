---
title: Azure Disk Encryption-scenarier på virtuella Windows-datorer
description: Den här artikeln innehåller anvisningar om hur du Microsoft Azure diskkryptering för virtuella Windows-datorer för olika scenarier
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2d8173e8b79e8696c2a3e4a7ea722b2947b1aee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776819"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-scenarier på virtuella Windows-datorer

Azure Disk Encryption för virtuella Windows-datorer (VM) använder BitLocker-funktionen i Windows för att tillhandahålla fullständig diskkryptering av OS-disken och datadisken. Dessutom ger den kryptering av den tillfälliga disken när parametern VolumeType är Alla.

Azure Disk Encryption är [integrerad med Azure Key Vault](disk-encryption-key-vault.md) som hjälper dig att styra och hantera diskkrypteringsnycklarna och hemligheterna. En översikt över tjänsten finns i Azure Disk Encryption [virtuella Windows-datorer.](disk-encryption-overview.md)

Du kan bara använda diskkryptering för virtuella datorer med [VM-storlekar och operativsystem som stöds.](disk-encryption-overview.md#supported-vms-and-operating-systems) Du måste också uppfylla följande krav:

- [Nätverkskrav](disk-encryption-overview.md#networking-requirements)
- [grupprincip krav](disk-encryption-overview.md#group-policy-requirements)
- [Lagringskrav för krypteringsnyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Om du tidigare har använt Azure Disk Encryption Azure AD för att kryptera en virtuell dator måste du fortsätta att använda det här alternativet för att kryptera den virtuella datorn. Se [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) för mer information. 
>
> - Du bör [ta en ögonblicksbild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhetskopia innan diskarna krypteras. Säkerhetskopieringar säkerställer att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopiering innan krypteringen sker. När en säkerhetskopia har gjorts kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Mer information om hur du återställer krypterade virtuella datorer finns i Back up and restore encrypted Azure VM ( [Back up and restore encrypted Azure VM).](../../backup/backup-azure-vms-encryption.md) 
>
> - Kryptering eller inaktivering av kryptering kan göra att en virtuell dator startas om.

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och ansluta till Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Aktivera kryptering på en befintlig eller aktiv virtuell Windows-dator
I det här scenariot kan du aktivera kryptering med hjälp Resource Manager, PowerShell-cmdlets eller CLI-kommandon. Om du behöver schemainformation för tillägget för virtuella datorer kan du läsa [Azure Disk Encryption för Windows-tillägget.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Aktivera kryptering på befintliga eller körande virtuella datorer med Azure PowerShell 
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure. 

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar dina variabler och kör Set-AzVMDiskEncryptionExtension cmdlet. Resursgruppen, den virtuella datorn och nyckelvalvet bör redan ha skapats som förutsättningar. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden.

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
   > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell IaaS-dator använder du cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera krypteringen använder [du cmdleten Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Aktivera kryptering på befintliga eller körande virtuella datorer med Azure CLI
Använd kommandot [az vm encryption enable för](/cli/azure/vm/encryption#az_vm_encryption_enable) att aktivera kryptering på en virtuell IaaS-dator som körs i Azure.

- **Kryptera en virtuell dator som körs:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Kryptera en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell IaaS-dator använder du [kommandot az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du [kommandot az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Använda Resource Manager mall

Du kan aktivera diskkryptering på befintliga eller köra virtuella IaaS Windows-datorer i Azure med hjälp av Resource Manager för att kryptera en [virtuell Windows-dator som körs.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)


1. I Azure-snabbstartsmallen klickar du **på Distribuera till Azure**.

2. Välj prenumeration, resursgrupp, plats, inställningar, juridiska villkor och avtal. Klicka **på Köp** för att aktivera kryptering på den befintliga eller körande virtuella IaaS-datorn.

I följande tabell visas de Resource Manager mallparametrarna för befintliga eller virtuella datorer som körs:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella dator som krypteringsåtgärden ska köras på. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckeln ska laddas upp till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommandot `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Namnet på resursgruppen som innehåller nyckelvalvet|
|  keyEncryptionKeyURL | URL:en för nyckelkrypteringsnyckeln i formatet https:// &lt; keyvault-name &gt; .vault.azure.net/key/ &lt; key-name &gt; . Om du inte vill använda en kek lämnar du det här fältet tomt. |
| volumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS,_ _Data_ och _Alla._ 
| forceUpdateTag | Skicka in ett unikt värde, till exempel ett GUID, varje gång åtgärden måste framt tvångskörning. |
| resizeOSDisk | Om os-partitionen ändras så att den upptar en fullständig virtuell hårddisk för operativsystemet innan systemvolymen delas. |
| location | Platser för alla resurser. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Aktivera kryptering på NVMe-diskar för virtuella Lsv2-datorer

I det här scenariot beskrivs Azure Disk Encryption på NVMe-diskar för virtuella datorer i Lsv2-serien.  Lsv2-serien har lokal NVMe-lagring. Lokala NVMe-diskar är tillfälliga och data går förlorade på dessa diskar om du stoppar/frisläpper den virtuella datorn (se: [Lsv2-serien](../lsv2-series.md)).

Så här aktiverar du kryptering på NVMe-diskar:

1. Initiera NVMe-diskarna och skapa NTFS-volymer.
1. Aktivera kryptering på den virtuella datorn med parametern VolumeType inställd på Alla. Detta aktiverar kryptering för alla operativsystem och datadiskar, inklusive volymer som backas upp av NVMe-diskar. Mer information finns i Aktivera [kryptering på en befintlig eller aktiv virtuell Windows-dator.](#enable-encryption-on-an-existing-or-running-windows-vm)

Krypteringen bevaras på NVMe-diskarna i följande scenarier:
- Omstart av virtuell dator
- Avbild av VM-skalningsuppsättning
- Växla operativsystem

NVMe-diskar kommer att oinitialiseras i följande scenarier:

- Starta virtuell dator efter avallokering
- Tjänstläkning
- Backup

I dessa scenarier måste NVMe-diskarna initieras när den virtuella datorn startar. Om du vill aktivera kryptering på NVMe-diskarna kör du kommandot för att aktivera Azure Disk Encryption igen när NVMe-diskarna har initierats.

Förutom de scenarier som anges i [avsnittet Scenarier som inte stöds](#unsupported-scenarios) stöds inte kryptering av NVMe-diskar för:

- Virtuella datorer krypterade med Azure Disk Encryption med AAD (tidigare version)
- NVMe-diskar med lagringsutrymmen
- Azure Site Recovery av SKU:er med NVMe-diskar (se Supportmatris för haveriberedskap för virtuella Azure-datorer mellan [Azure-regioner: Replikerade datorer – lagring).](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nya virtuella IaaS-datorer som skapats från kundkrypterad virtuell hårddisk och krypteringsnycklar

I det här scenariot kan du skapa en ny virtuell dator från en förkrypterad virtuell hårddisk och tillhörande krypteringsnycklar med hjälp av PowerShell-cmdlets eller CLI-kommandon. 

Följ instruktionerna i [Förbereda en förkrypterad virtuell Windows-hårddisk.](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd) När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Kryptera virtuella datorer med förkrypterade virtuella hårddiskar med Azure PowerShell
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk med hjälp av PowerShell-cmdleten [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) I exemplet nedan visas några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk till en virtuell Windows-dator](attach-disk-ps.md)med hjälp av PowerShell eller via [Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder PowerShell för att kryptera en ny disk för virtuella Windows-datorer ska en ny sekvensversion anges. Sekvensversionen måste vara unik. Skriptet nedan genererar ett GUID för sekvensversionen. I vissa fall kan en nyligen tillagd datadisk krypteras automatiskt av Azure Disk Encryption tillägg. Automatisk kryptering sker vanligtvis när den virtuella datorn startas om när den nya disken är online. Detta beror vanligtvis på att "Alla" angavs för volymtypen när diskkryptering tidigare kördes på den virtuella datorn. Om automatisk kryptering sker på en nyligen tillagd datadisk rekommenderar vi att du kör Set-AzVmDiskEncryptionExtension cmdlet igen med en ny sekvensversion. Om den nya datadisken krypteras automatiskt och du inte vill krypteras dekrypterar du först alla enheter och krypterar sedan om med en ny sekvensversion som anger operativsystemet som volymtyp. 
  
 

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar dina variabler och kör Set-AzVMDiskEncryptionExtension cmdlet. Resursgruppen, den virtuella datorn och nyckelvalvet bör redan ha skapats som förutsättningar. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. I det här exemplet används "Alla" för parametern -VolumeType, som innehåller både OS- och Datavolymer. Om du bara vill kryptera OS-volymen använder du "OS" för parametern -VolumeType. 

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
- **Kryptera en virtuell dator som körs med KEK:** I det här exemplet används "Alla" för parametern -VolumeType, som innehåller både OS- och Datavolymer. Om du bara vill kryptera OS-volymen använder du "OS" för parametern -VolumeType.

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
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Azure CLI-kommandot tillhandahåller automatiskt en ny sekvensversion när du kör kommandot för att aktivera kryptering. I exemplet används "Alla" för parametern volymtyp. Du kan behöva ändra parametern för volymtyp till OPERATIVSYSTEM om du bara krypterar OS-disken. Till skillnad från PowerShell-syntaxen kräver INTE CLI att användaren anger en unik sekvensversion när krypteringen möjliggörs. CLI genererar och använder automatiskt sitt eget unika versionsvärde för sekvensen.   

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

- Kryptering av virtuella datorer på basic-nivå eller virtuella datorer som skapats via den klassiska metoden för att skapa virtuella datorer.
- Kryptera virtuella datorer som konfigurerats med programvarubaserade RAID-system.
- Kryptera virtuella datorer som konfigurerats med Lagringsdirigering (S2D) eller Windows Server-versioner före 2016 konfigurerade med Windows-lagringsutrymmen.
- Integrering med ett lokalt nyckelhanteringssystem.
- Azure Files (delat filsystem).
- Network File System (NFS).
- Dynamiska volymer.
- Windows Server-containrar, som skapar dynamiska volymer för varje container.
- Tillfälliga OS-diskar.
- Kryptering av delade/distribuerade filsystem som (men inte begränsat till) DFS, GFS, DRDB och CephFS.
- Flytta en krypterad virtuell dator till en annan prenumeration eller region.
- Skapa en avbildning eller ögonblicksbild av en krypterad virtuell dator och använda den för att distribuera ytterligare virtuella datorer.
- Virtuella datorer i M-serien Skrivningsaccelerator diskar.
- Tillämpa ADE på en virtuell dator som har diskar krypterade med kryptering på [serversidan med kund-hanterade nycklar](../disk-encryption.md) (SSE + CMK). Att använda SSE + CMK på en datadisk på en virtuell dator som krypterats med ADE stöds inte också.
- Migrera en virtuell dator som är krypterad  med ADE, eller som någonsin har krypterats med ADE, till kryptering på serversidan med [kund hanterade nycklar.](../disk-encryption.md)
- Kryptera virtuella datorer i redundanskluster.
- Kryptering av [Azure Ultra-diskar](../disks-enable-ultra-ssd.md).

## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption översikt](disk-encryption-overview.md)
- [Exempelskript för Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)