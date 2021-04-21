---
title: Azure Disk Encryption-scenarier på virtuella Linux-datorer
description: Den här artikeln innehåller anvisningar om hur du Microsoft Azure diskkryptering för virtuella Linux-datorer för olika scenarier
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f014c07a319cbb07497cba01699b93d092255b93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771519"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-scenarier på virtuella Linux-datorer

Azure Disk Encryption för virtuella Linux-datorer (VM) använder DM-Crypt i Linux för att tillhandahålla fullständig diskkryptering av OS-disken och datadiskarna. Dessutom ger den kryptering av den tillfälliga disken när du använder funktionen EncryptFormatAll.

Azure Disk Encryption är [integrerad med Azure Key Vault](disk-encryption-key-vault.md) som hjälper dig att styra och hantera diskkrypteringsnycklarna och hemligheterna. En översikt över tjänsten finns i Azure Disk Encryption [virtuella Linux-datorer.](disk-encryption-overview.md)

Du kan bara använda diskkryptering för virtuella datorer med [VM-storlekar och operativsystem som stöds.](disk-encryption-overview.md#supported-vms-and-operating-systems) Du måste också uppfylla följande krav:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverkskrav](disk-encryption-overview.md#networking-requirements)
- [Lagringskrav för krypteringsnyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

I samtliga fall bör du [ta en ögonblicksbild och/eller](snapshot-copy-managed-disk.md) skapa en säkerhetskopia innan diskarna krypteras. Säkerhetskopieringar säkerställer att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan krypteringen sker. När en säkerhetskopia har gjorts kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Mer information om hur du återställer krypterade virtuella datorer finns i artikeln [Azure Backup](../../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
> - Om du tidigare har använt Azure Disk Encryption Azure AD för att kryptera en virtuell dator måste du fortsätta att använda det här alternativet för att kryptera den virtuella datorn. Se [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) för mer information. 
>
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att du undviker SSH-inloggningar medan krypteringen pågår för att undvika problem som blockerar öppna filer som behöver nås under krypteringsprocessen. Om du vill kontrollera förloppet använder du [PowerShell-cmdleten Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller kommandot [vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI. Den här processen kan förväntas ta några timmar för en os-volym på 30 GB, plus ytterligare tid för kryptering av datavolymer. Krypteringstiden för datavolymerna är proportionell mot storleken och kvantiteten för datavolymerna, såvida inte alternativet kryptera formatera alla används. 
> - Inaktivering av kryptering på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats.  

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och ansluta till Azure

Azure Disk Encryption kan aktiveras och hanteras via [Azure CLI och](/cli/azure) [Azure PowerShell](/powershell/azure/new-azureps-module-az). För att göra det måste du installera verktygen lokalt och ansluta till din Azure-prenumeration.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0 är ett](/cli/azure) kommandoradsverktyg för att hantera Azure-resurser. CLI är utformat för att flexibelt fråga efter data, stödja långvariga åtgärder som icke-blockerande processer och göra skript enkelt. Du kan installera det lokalt genom att följa stegen i [Installera Azure CLI.](/cli/azure/install-azure-cli)

 

Om [du vill logga in på ditt Azure-konto med Azure CLI](/cli/azure/authenticate-azure-cli)använder du kommandot az [login.](/cli/azure/reference-index#az_login)

```azurecli
az login
```

Om du vill välja en klientorganisation att logga in under använder du:
    
```azurecli
az login --tenant <tenant>
```

Om du har flera prenumerationer och vill ange en specifik, hämtar du din prenumerationslista med [az account list](/cli/azure/account#az_account_list) och anger med az account [set](/cli/azure/account#az_account_set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Mer information finns i [Kom igång med Azure CLI 2.0.](/cli/azure/get-started-with-azure-cli) 

### <a name="azure-powershell"></a>Azure PowerShell
Modulen [Azure PowerShell az innehåller](/powershell/azure/new-azureps-module-az) en uppsättning cmdlets som använder Azure Resource Manager för att hantera dina Azure-resurser. [](../../azure-resource-manager/management/overview.md) Du kan använda det i webbläsaren [med Azure Cloud Shell](../../cloud-shell/overview.md), eller så kan du installera det på den lokala datorn med hjälp av [anvisningarna](/powershell/azure/install-az-ps)i Installera Azure PowerShell modulen . 

Om du redan har installerat det lokalt kontrollerar du att du använder den senaste versionen Azure PowerShell SDK-versionen för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen [Azure PowerShell versionen](https://github.com/Azure/azure-powershell/releases).

Om [du vill logga in på ditt Azure-Azure PowerShell](/powershell/azure/authenticate-azureps)använder du cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer och vill ange en använder du cmdleten [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) för att visa dem, följt av cmdleten [Set-AzContext:](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Om du [kör cmdleten Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifieras att rätt prenumeration har valts.

Kontrollera att Azure Disk Encryption cmdlets är installerade med hjälp av [cmdleten Get-command:](/powershell/module/microsoft.powershell.core/get-command)
     
```powershell
Get-command *diskencryption*
```
Mer information finns i [Komma igång med Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Aktivera kryptering på en befintlig eller aktiv virtuell Linux-dator
I det här scenariot kan du aktivera kryptering med hjälp Resource Manager, PowerShell-cmdlets eller CLI-kommandon. Om du behöver schemainformation för tillägget för virtuella datorer kan du läsa artikeln [om Azure Disk Encryption för Linux.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilder och/eller säkerhetskopiering av en hanterad diskbaserad VM-instans utanför och innan du aktiverar Azure Disk Encryption. En ögonblicksbild av den hanterade disken kan tas från portalen eller via [Azure Backup](../../backup/backup-azure-vms-encryption.md). Säkerhetskopieringar säkerställer att ett återställningsalternativ är möjligt om det uppstår oväntade fel under krypteringen. När en säkerhetskopia har gjorts kan Set-AzVMDiskEncryptionExtension-cmdleten användas för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot hanterade diskbaserade virtuella datorer tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivering av kryptering kan göra att den virtuella datorn startas om. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Aktivera kryptering på en befintlig eller aktiv virtuell Linux-dator med Hjälp av Azure CLI 

Du kan aktivera diskkryptering på din krypterade virtuella hårddisk genom att installera och använda azure CLI-kommandoradsverktyget. [](/cli/azure/) Du kan använda det i webbläsaren med [Azure Cloud Shell](../../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Om du vill aktivera kryptering på befintliga eller köra virtuella Linux-datorer i Azure använder du följande CLI-kommandon:

Använd kommandot [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_show) för att aktivera kryptering på en virtuell dator som körs i Azure.

- **Kryptera en virtuell dator som körs:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell dator använder du [kommandot az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du [kommandot az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Inaktivering av kryptering tillåts endast på datavolymer för virtuella Linux-datorer.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Aktivera kryptering på en befintlig eller aktiv virtuell Linux-dator med PowerShell
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell dator som körs i Azure. Ta en [ögonblicksbild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella [datorn Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern -skipVmBackup har redan angetts i PowerShell-skript för att kryptera en virtuell Linux-dator som körs.

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar dina variabler och kör Set-AzVMDiskEncryptionExtension cmdlet. Resursgruppen, den virtuella datorn och nyckelvalvet skapades som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Ändra parametern -VolumeType för att ange vilka diskar du krypterar.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Kryptera en virtuell dator som körs med KEK:** Du kan behöva lägga till parametern -VolumeType om du krypterar datadiskar och inte OS-disken. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell dator använder du cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera krypteringen använder [du cmdleten Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Inaktivering av kryptering tillåts endast på datavolymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Aktivera kryptering på en befintlig eller aktiv virtuell Linux-dator med en mall

Du kan aktivera diskkryptering på en befintlig eller aktiv virtuell Linux-dator i Azure med hjälp [Resource Manager mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klicka **på Distribuera till Azure** i Azure-snabbstartsmallen.

2. Välj prenumeration, resursgrupp, resursgruppsplats, parametrar, juridiska villkor och avtal. Klicka **på Skapa** för att aktivera kryptering på den befintliga eller virtuella datorn som körs.

I följande tabell visas Resource Manager mallparametrar för befintliga eller virtuella datorer som körs:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella dator som krypteringsåtgärden ska köras på. |
| keyVaultName | Namnet på nyckelvalvet som krypteringsnyckeln ska laddas upp till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommandot `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | Namnet på den resursgrupp som innehåller nyckelvalvet. |
|  keyEncryptionKeyURL | URL för den nyckelkrypteringsnyckel som används för att kryptera krypteringsnyckeln. Den här parametern är valfri **om du väljer nokek** i listrutan UseExistingKek. Om du **väljer kek** i listrutan UseExistingKek måste du ange _värdet keyEncryptionKeyURL._ |
| volumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS,_ _Data_ och _Alla._ 
| forceUpdateTag | Skicka in ett unikt värde, till exempel ett GUID, varje gång åtgärden måste framt tvångskörning. |
| location | Platser för alla resurser. |

Mer information om hur du konfigurerar diskkrypteringsmallen för virtuella Linux-datorer [finns i Azure Disk Encryption för Linux](../extensions/azure-disk-enc-linux.md).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Använda funktionen EncryptFormatAll för datadiskar på virtuella Linux-datorer

Parametern **EncryptFormatAll** minskar tiden för Linux-datadiskar att krypteras. Partitioner som uppfyller vissa kriterier formateras tillsammans med deras aktuella filsystem och återmonteras sedan tillbaka till den plats där de var innan kommandokörningen. Om du vill undanta en datadisk som uppfyller villkoren kan du demontera den innan du kör kommandot.

 När du har kört det här kommandot formateras alla enheter som monterades tidigare och krypteringslagret startas ovanpå den nu tomma enheten. När det här alternativet väljs krypteras även den tillfälliga disken som är ansluten till den virtuella datorn. Om den tillfälliga disken återställs formateras den om och krypteras om för den virtuella datorn av Azure Disk Encryption nästa gång. När resursdisken har krypterats [kan Microsoft Azure Linux-agenten](../extensions/agent-linux.md) inte hantera resursdisken och aktivera växlingsfilen, men du kan konfigurera växlingsfilen manuellt.

>[!WARNING]
> EncryptFormatAll bör inte användas när det behövs data på en virtuell dators datavolymer. Du kan undanta diskar från kryptering genom att demontera dem. Du bör först prova EncryptFormatAll först på en virtuell test-dator, förstå funktionsparametern och dess konsekvenser innan du testar den på den virtuella datorn i produktion. Alternativet EncryptFormatAll formaterar datadisken och alla data på den kommer att gå förlorade. Kontrollera att de diskar som du vill undanta är korrekt omonterade innan du fortsätter. </br></br>
 >Om du ställer in den här parametern när du uppdaterar krypteringsinställningarna kan det leda till en omstart innan den faktiska krypteringen. I det här fallet bör du också ta bort den disk som du inte vill formatera från fstab-filen. På samma sätt bör du lägga till den partition som du vill kryptera formatera i fstab-filen innan du påbörjar krypteringsåtgärden. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll-villkor
Parametern går igenom alla partitioner och krypterar dem så länge de **uppfyller** alla kriterier nedan:
- Är inte en rot-/OS-/startpartition
- Är inte redan krypterad
- Är inte en BEK-volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera de diskar som utgör RAID- eller LVM-volymen i stället för RAID- eller LVM-volymen.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Använda parametern EncryptFormatAll med Azure CLI
Använd kommandot [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) för att aktivera kryptering på en virtuell dator som körs i Azure.

-  **Kryptera en virtuell dator som körs med EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Använda parametern EncryptFormatAll med en PowerShell-cmdlet
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) med parametern EncryptFormatAll. 

**Kryptera en virtuell dator som körs med EncryptFormatAll:** Skriptet nedan initierar till exempel variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension med parametern EncryptFormatAll. Resursgruppen, den virtuella datorn och nyckelvalvet skapades som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Använd parametern EncryptFormatAll med Logical Volume Manager (LVM) 
Vi rekommenderar en LVM-on-crypt-konfiguration. För alla följande exempel ersätter du enhetssökvägen och monteringspunkterna med det som passar ditt användningsfall. Den här konfigurationen kan göras på följande sätt:

1.  Lägg till de datadiskar som ska skapa den virtuella datorn.

1. Formatera, montera och lägg till diskarna i fstab-filen.

1. Välj en partitionsstandard, skapa en partition som sträcker sig över hela enheten och formatera sedan partitionen. Vi använder symlinks som genereras av Azure här. Med symlinks undviker du problem som rör ändring av enhetsnamn. Mer information finns i artikeln [Felsöka problem med enhetsnamn.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. Montera diskarna:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Lägg till i fstab-filen:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Kör cmdleten [Azure PowerShell Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) med -EncryptFormatAll för att kryptera diskarna.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Om du vill använda en nyckelkrypteringsnyckel (KEK) skickar du URI:n för din KEK och ResourceID för nyckelvalvet till parametrarna -KeyEncryptionKeyUrl respektive -KeyEncryptionKeyVaultId:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Konfigurera LVM ovanpå dessa nya diskar. Observera att krypterade enheter låses upp när den virtuella datorn har startats klart. Därför måste LVM-monteringen också fördröjas senare.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nya virtuella datorer som skapats från kundkrypterad virtuell hårddisk och krypteringsnycklar
I det här scenariot kan du aktivera kryptering med hjälp av PowerShell-cmdlets eller CLI-kommandon. 

Använd instruktionerna i Azure Disk Encryption samma skript för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbereda en förkrypterad virtuell Linux-hårddisk](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilder och/eller säkerhetskopiering av en hanterad diskbaserad virtuell datorinstans utanför och innan du aktiverar Azure Disk Encryption. En ögonblicksbild av den hanterade disken kan tas från portalen eller [Azure Backup](../../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopieringar säkerställer att ett återställningsalternativ är möjligt om det uppstår oväntade fel under krypteringen. När en säkerhetskopia har gjorts kan Set-AzVMDiskEncryptionExtension-cmdleten användas för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot hanterade diskbaserade virtuella datorer tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
> Kryptering eller inaktivering av kryptering kan göra att den virtuella datorn startas om. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Använd Azure PowerShell för att kryptera virtuella datorer med förkrypterade virtuella hårddiskar 
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk med hjälp av PowerShell-cmdleten [Set-AzVMOSDisk.](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) I exemplet nedan visas några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk

Du kan lägga till en ny datadisk [med az vm disk attach](add-disk.md), eller via [Azure Portal](attach-disk-portal.md). Innan du kan kryptera måste du montera den nyligen anslutna datadisken först. Du måste begära kryptering av dataenheten eftersom enheten kommer att vara oanvändbar medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI

 Om den virtuella datorn tidigare krypterades med "Alla" ska parametern --volume-type förbli "Alla". Alla innehåller både os- och datadiskar. Om den virtuella datorn tidigare krypterades med volymtypen "OS" bör parametern --volume-type ändras till "Alla" så att både operativsystemet och den nya datadisken inkluderas. Om den virtuella datorn har krypterats med endast volymtypen "Data" kan den förbli "Data" enligt nedan. Det räcker inte att lägga till och koppla en ny datadisk till en virtuell dator för kryptering. Den nyligen anslutna disken måste också vara formaterad och korrekt monterad på den virtuella datorn innan kryptering kan användas. På Linux måste disken monteras i /etc/fstab med ett [beständigt blockenhetsnamn.](../troubleshooting/troubleshoot-device-names-problems.md)  

Till skillnad från PowerShell-syntaxen kräver INTE CLI att användaren anger en unik sekvensversion när kryptering möjliggörs. CLI genererar och använder automatiskt ett eget unikt versionsvärde för sekvensen.

-  **Kryptera datavolymer för en virtuell dator som körs:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera datavolymer för en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder PowerShell för att kryptera en ny disk för Linux måste en ny sekvensversion anges. Sekvensversionen måste vara unik. Skriptet nedan genererar ett GUID för sekvensversionen. Ta en [ögonblicksbild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella [datorn Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern -skipVmBackup har redan angetts i PowerShell-skript för att kryptera en nyligen tillagd datadisk.
 

-  **Kryptera datavolymer för en virtuell dator som körs:** Skriptet nedan initierar dina variabler och kör Set-AzVMDiskEncryptionExtension cmdlet. Resursgruppen, den virtuella datorn och nyckelvalvet bör redan ha skapats som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Godkända värden för parametern -VolumeType är Alla, OPERATIVSYSTEM och Data. Om den virtuella datorn tidigare krypterades med volymtypen "OS" eller "Alla" bör parametern -VolumeType ändras till "Alla" så att både operativsystemet och den nya datadisken inkluderas.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Kryptera datavolymer för en virtuell dator som körs med KEK:** Godkända värden för parametern -VolumeType är Alla, OPERATIVSYSTEM och Data. Om den virtuella datorn tidigare krypterades med volymtypen "OS" eller "Alla" bör parametern -VolumeType ändras till Alla så att både operativsystemet och den nya datadisken inkluderas.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[KMetrisource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Azure Disk Encryption fungerar inte för följande Linux-scenarier, -funktioner och -teknik:

- Kryptering av virtuella datorer på basic-nivå eller virtuella datorer som skapats via den klassiska metoden för att skapa virtuella datorer.
- Inaktivera kryptering på en operativsystemenhet eller dataenhet för en virtuell Linux-dator när OS-enheten är krypterad.
- Kryptera OS-enheten för VM-skalningsuppsättningar i Linux.
- Kryptera anpassade avbildningar på virtuella Linux-datorer.
- Integrering med ett lokalt nyckelhanteringssystem.
- Azure Files (delat filsystem).
- Network File System (NFS).
- Dynamiska volymer.
- Tillfälliga OS-diskar.
- Kryptering av delade/distribuerade filsystem som (men inte begränsat till): DFS, GFS, DRDB och CephFS.
- Flytta en krypterad virtuell dator till en annan prenumeration eller region.
- Skapa en avbildning eller ögonblicksbild av en krypterad virtuell dator och använda den för att distribuera ytterligare virtuella datorer.
- Kernel Crash Dump (kdump).
- Oracle ACFS (ASM Cluster File System).
- NVMe-diskarna för virtuella datorer i Lsv2-serien (se: [Lsv2-serien](../lsv2-series.md)).
- En virtuell dator med "kapslade monteringspunkter"; det vill säga flera monteringspunkter i en enda sökväg (till exempel "/1stmountpoint/data/2stmountpoint").
- En virtuell dator med en dataenhet monterad ovanpå en OS-mapp.
- En virtuell dator där en logisk rotvolym (OS-disk) har utökats med hjälp av en datadisk.
- Virtuella datorer i M-serien Skrivningsaccelerator diskar.
- Tillämpa ADE på en virtuell dator som har diskar krypterade med kryptering på serversidan med [kund-hanterade nycklar](../disk-encryption.md) (SSE + CMK). Att tillämpa SSE + CMK på en datadisk på en virtuell dator som krypterats med ADE stöds inte också.
- Migrera en virtuell dator som är krypterad  med ADE, eller som någonsin har krypterats med ADE, till kryptering på serversidan med [kund hanterade nycklar.](../disk-encryption.md)
- Kryptera virtuella datorer i redundanskluster.
- Kryptering av [Azure Ultra-diskar](../disks-enable-ultra-ssd.md).

## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption översikt](disk-encryption-overview.md)
- [Exempelskript för Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)