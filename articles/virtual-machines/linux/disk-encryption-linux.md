---
title: Azure Disk Encryption-scenarier på virtuella Linux-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Linux-datorer för olika scenarier
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 88a0239409d29700d95b8949c1499dd30f50bb5f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510733"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-scenarier på virtuella Linux-datorer


Azure Disk Encryption för virtuella Linux-datorer (VM: ar) använder DM-crypt-funktionen i Linux för att tillhandahålla fullständig disk kryptering för operativ system disk och data diskar. Dessutom ger den en kryptering av den temporära disken när du använder funktionen EncryptFormatAll.

Azure Disk Encryption är [integrerat med Azure Key Vault](disk-encryption-key-vault.md) för att hjälpa dig att styra och hantera disk krypterings nycklar och hemligheter. En översikt över tjänsten finns i [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md).

Du kan bara använda disk kryptering för virtuella datorer med [stödda VM-storlekar och operativ system](disk-encryption-overview.md#supported-vms-and-operating-systems). Du måste också uppfylla följande krav:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverks krav](disk-encryption-overview.md#networking-requirements)
- [Lagrings krav för krypterings nyckel](disk-encryption-overview.md#encryption-key-storage-requirements)

I samtliga fall bör du [ta en ögonblicks bild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhets kopia innan diskarna krypteras. Säkerhets kopieringar säkerställer att ett återställnings alternativ är möjligt om ett oväntat fel uppstår under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhets kopia innan krypteringen utförs. När du har gjort en säkerhets kopia kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern-skipVmBackup. Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i [Azure Backup](../../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) . 
>
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att du undviker SSH-inloggningar medan krypteringen pågår för att undvika problem som blockerar eventuella öppna filer som behöver nås under krypterings processen. Om du vill kontrol lera förloppet använder du PowerShell-cmdleten [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller [VM-kryptering Visa](/cli/azure/vm/encryption#az-vm-encryption-show) CLI-kommando. Den här processen kan förväntas ta några timmar för en 30 GB OS-volym, plus ytterligare tid för kryptering av data volymer. Krypterings tiden för data volymer är proportionell mot storlek och kvantitet för data volymerna om inte alternativet Kryptera format alla används. 
> - Det går bara att inaktivera kryptering på virtuella Linux-datorer för data volymer. Det stöds inte på data-eller OS-volymer om operativ system volymen har krypterats.  

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och Anslut till Azure

Azure Disk Encryption kan aktive ras och hanteras via [Azure CLI](/cli/azure) och [Azure PowerShell](/powershell/azure/new-azureps-module-az). Om du vill göra det måste du installera verktygen lokalt och ansluta till din Azure-prenumeration.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2,0](/cli/azure) är ett kommando rads verktyg för att hantera Azure-resurser. CLI är utformat för flexibel frågedata, stöder långvariga åtgärder som icke-blockerande processer och gör skript enkla. Du kan installera det lokalt genom att följa stegen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

 

Om du vill [Logga in på ditt Azure-konto med Azure CLI](/cli/azure/authenticate-azure-cli)använder du kommandot [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Om du vill välja en klient som ska logga in under använder du:
    
```azurecli
az login --tenant <tenant>
```

Om du har flera prenumerationer och vill ange en speciell, hämtar du din prenumerations lista med [AZ Account List](/cli/azure/account#az-account-list) och anger med [AZ Account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Mer information finns i [Kom igång med Azure CLI 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Modulen Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) innehåller en uppsättning cmdletar som använder [Azure Resource Manager](../../azure-resource-manager/management/overview.md) -modellen för att hantera dina Azure-resurser. Du kan använda den i din webbläsare med [Azure Cloud Shell](../../cloud-shell/overview.md), eller så kan du installera den på din lokala dator med hjälp av anvisningarna i [installera modulen Azure PowerShell](/powershell/azure/install-az-ps). 

Om du redan har installerat det lokalt kontrollerar du att du använder den senaste versionen av Azure PowerShell SDK-versionen för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)-versionen.

Om du vill [Logga in på ditt Azure-konto med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)använder du cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer och vill ange en använder du cmdleten [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) för att lista dem, följt av cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Genom att köra cmdleten [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifierar du att rätt prenumeration har valts.

Om du vill bekräfta att Azure Disk Encryption-cmdletarna är installerade använder du cmdleten [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Mer information finns i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Aktivera kryptering på en befintlig eller virtuell Linux-dator
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdletar eller CLI-kommandon. Om du behöver schema information för tillägget för virtuell dator kan du läsa artikeln [Azure Disk Encryption för Linux-tillägg](../extensions/azure-disk-enc-linux.md) .

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicks bilder och/eller säkerhetskopiera en hanterad disk baserad virtuell dator instans utanför, och innan Azure Disk Encryption aktive ras. En ögonblicks bild av den hanterade disken kan hämtas från portalen eller via [Azure Backup](../../backup/backup-azure-vms-encryption.md). Säkerhets kopieringar säkerställer att ett återställnings alternativ är möjligt om det uppstår oväntade fel under krypteringen. När en säkerhets kopia har gjorts kan cmdleten Set-AzVMDiskEncryptionExtension användas för att kryptera Managed disks genom att ange parametern-skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension fungerar inte mot hanterade diskbaserade virtuella datorer förrän en säkerhets kopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering kan leda till att den virtuella datorn startas om. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Aktivera kryptering på en befintlig eller virtuell Linux-dator med Azure CLI 

Du kan aktivera disk kryptering på din krypterade virtuella hård disk genom att installera och använda kommando rads verktyget för [Azure CLI](/cli/azure/?view=azure-cli-latest) . Du kan använda det i webbläsaren med [Azure Cloud Shell](../../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Använd följande CLI-kommandon om du vill aktivera kryptering på befintliga eller virtuella Linux-datorer i Azure:

Använd kommandot [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) för att aktivera kryptering på en virtuell dator som körs i Azure.

- **Kryptera en virtuell dator som körs:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br>
Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

- **Kontrol lera att diskarna är krypterade:** Om du vill kontrol lera krypterings statusen för en virtuell dator använder du kommandot [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Det går bara att inaktivera kryptering på data volymer för virtuella Linux-datorer.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Aktivera kryptering på en befintlig eller virtuell Linux-dator med PowerShell
Använd cmdleten [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell dator som körs i Azure. Ta en [ögonblicks bild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern-skipVmBackup har redan angetts i PowerShell-skripten för att kryptera en virtuell Linux-dator som körs.

-  **Kryptera en virtuell dator som körs:** Skriptet nedan initierar variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension. Resurs gruppen, den virtuella datorn och nyckel valvet har skapats som krav. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Ändra parametern-VolumeType för att ange vilka diskar som du ska kryptera.

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
- **Kryptera en virtuell dator som körs med KEK:** Du kan behöva lägga till parametern-VolumeType om du krypterar data diskar och inte på OS-disken. 

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
    > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 
    
- **Kontrol lera att diskarna är krypterade:** Om du vill kontrol lera krypterings statusen för en virtuell dator använder du cmdleten [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera disk kryptering:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Det går bara att inaktivera kryptering på data volymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Aktivera kryptering på en befintlig eller virtuell Linux-dator med en mall

Du kan aktivera disk kryptering på en befintlig eller köra en virtuell Linux-dator i Azure med hjälp av [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klicka på **distribuera till Azure** i Azure snabb starts mal len.

2. Välj prenumeration, resurs grupp, plats för resurs grupp, parametrar, juridiska villkor och avtal. Klicka på **skapa** för att aktivera kryptering på den befintliga eller aktiva virtuella datorn.

I följande tabell visas parametrar för Resource Manager-mallar för befintliga eller aktiva virtuella datorer:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella dator som ska köra krypterings åtgärden. |
| keyVaultName | Namnet på nyckel valvet som krypterings nyckeln ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI- `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` kommandot.|
| keyVaultResourceGroup | Namnet på den resurs grupp som innehåller nyckel valvet. |
|  keyEncryptionKeyURL | URL till den nyckel krypterings nyckel som används för att kryptera krypterings nyckeln. Den här parametern är valfri om du väljer **nokek** i list rutan UseExistingKek. Om du väljer **KEK** i list rutan UseExistingKek måste du ange värdet _keyEncryptionKeyURL_ . |
| volumeType | Typ av volym som krypterings åtgärden utförs på. Giltiga värden är _OS_, _data_och _alla_. 
| forceUpdateTag | Skicka ett unikt värde som ett GUID varje gång åtgärden måste tvingas köras. |
| location | Platser för alla resurser. |

Mer information om hur du konfigurerar den virtuella Linux-mallen för disk kryptering finns i [Azure Disk Encryption för Linux](../extensions/azure-disk-enc-linux.md).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Använda funktionen EncryptFormatAll för data diskar på virtuella Linux-datorer

Parametern **EncryptFormatAll** minskar tiden för att Linux-datadiskarna ska krypteras. Partitioner som uppfyller vissa villkor formateras, tillsammans med de aktuella fil systemen, monteras sedan tillbaka till där de var före kommando körningen. Om du vill undanta en datadisk som uppfyller villkoren kan du Demontera den innan du kör kommandot.

 När du har kört det här kommandot kommer alla enheter som monterats tidigare att formateras och krypterings lagret startas ovanpå den nu tomma enheten. När det här alternativet är markerat krypteras även den temporära disk som är ansluten till den virtuella datorn. Om den tillfälliga disken återställs, formateras den om och krypteras om för den virtuella datorn med Azure Disk Encryption lösning vid nästa tillfälle. När resurs disken krypteras kommer [Microsoft Azure Linux-agenten](../extensions/agent-linux.md) inte att kunna hantera resurs disken och aktivera växlings filen, men du kan konfigurera växlings filen manuellt.

>[!WARNING]
> EncryptFormatAll bör inte användas när det behövs data på en virtuell dators data volymer. Du kan utesluta diskar från kryptering genom att demontera dem. Du bör först testa EncryptFormatAll först på en virtuell test dator, förstå funktions parametern och dess indirekt innan du testar den på den virtuella produktions datorn. Alternativet EncryptFormatAll formaterar data disken och alla data på den kommer att gå förlorade. Innan du fortsätter bör du kontrol lera att diskarna som du vill undanta är korrekt demonterade. </br></br>
 >Om du anger den här parametern när du uppdaterar krypterings inställningarna kan det leda till en omstart före den faktiska krypteringen. I det här fallet ska du också ta bort disken som du inte vill formatera från fstab-filen. På samma sätt bör du lägga till partitionen som du vill kryptera-formaterad till fstab-filen innan du initierar krypterings åtgärden. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll kriterier
Parametern går ut om alla partitioner och krypterar dem så länge de uppfyller **alla** kriterier nedan:
- Är inte en rot-/OS/startpartition
- Är inte redan krypterad
- Är inte en BEK-volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera diskarna som skapar RAID-eller LVM-volymen i stället för RAID-eller LVM-volymen.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Använda EncryptFormatAll-parametern med Azure CLI
Använd kommandot [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell dator som körs i Azure.

-  **Kryptera en virtuell dator som körs med EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Använda parametern EncryptFormatAll med en PowerShell-cmdlet
Använd cmdleten [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) med parametern EncryptFormatAll. 

**Kryptera en virtuell dator som körs med EncryptFormatAll:** Skriptet nedan initierar till exempel variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension med parametern EncryptFormatAll. Resurs gruppen, den virtuella datorn och nyckel valvet har skapats som krav. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden.
  
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
Vi rekommenderar en LVM-in-Encrypt-installation. I följande exempel ersätter du enhets Sök vägen och mountpoints med det som passar dina användnings fall. Du kan göra följande inställningar:

1.  Lägg till de data diskar som ska skapa den virtuella datorn.

1. Formatera, montera och Lägg till de här diskarna i fstab-filen.

1. Välj en partition standard, skapa en partition som sträcker sig över hela enheten och formatera sedan partitionen. Vi använder symlinks som genererats av Azure här. Om du använder symlinks undviker du problem som rör ändring av enhets namn. Mer information finns i artikeln [Felsök problem med enhets namn](../troubleshooting/troubleshoot-device-names-problems.md) .
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. Montera diskarna:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Lägg till i fstab-fil:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Kör Azure PowerShell [set-AzVMDiskEncryptionExtension-](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-3.8.0) cmdlet med-EncryptFormatAll för att kryptera diskarna.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Om du vill använda en nyckel krypterings nyckel (KEK) skickar du URI: n för din KEK och ResourceID för nyckel valvet till parametrarna-KeyEncryptionKeyUrl och-KeyEncryptionKeyVaultId:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Konfigurera LVM ovanpå de här nya diskarna. Observera att de krypterade enheterna låses upp när den virtuella datorn har startats. Det innebär att LVM-monteringen också måste förskjutas senare.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nya virtuella datorer som skapats från kund-krypterade VHD-och krypterings nycklar
I det här scenariot kan du aktivera kryptering med hjälp av PowerShell-cmdletar eller CLI-kommandon. 

Följ anvisningarna i Azure Disk Encryption samma skript för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbereda en förkrypterad virtuell Linux-hårddisk](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicks bilder och/eller säkerhetskopiera en hanterad disk baserad virtuell dator instans utanför, och innan Azure Disk Encryption aktive ras. En ögonblicks bild av den hanterade disken kan hämtas från portalen, eller [Azure Backup](../../backup/backup-azure-vms-encryption.md) kan användas. Säkerhets kopieringar säkerställer att ett återställnings alternativ är möjligt om det uppstår oväntade fel under krypteringen. När en säkerhets kopia har gjorts kan cmdleten Set-AzVMDiskEncryptionExtension användas för att kryptera Managed disks genom att ange parametern-skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension fungerar inte mot hanterade diskbaserade virtuella datorer förrän en säkerhets kopia har gjorts och den här parametern har angetts. 
>
> Kryptering eller inaktivera kryptering kan leda till att den virtuella datorn startas om. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Använd Azure PowerShell för att kryptera virtuella datorer med förkrypterade virtuella hård diskar 
Du kan aktivera disk kryptering på din krypterade virtuella hård disk med PowerShell-cmdleten [set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). Exemplet nedan visar några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk

Du kan lägga till en ny datadisk med [AZ VM disk Attach](add-disk.md)eller [via Azure Portal](attach-disk-portal.md). Innan du kan kryptera måste du montera den nyligen anslutna data disken först. Du måste begära kryptering av data enheten eftersom enheten inte kan användas medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI

 Om den virtuella datorn tidigare har krypterats med "alla", ska parametern--volym typ vara kvar. Alla omfattar både OS-och data diskar. Om den virtuella datorn tidigare har krypterats med en volymtyp av typen "OS", ska parametern--type ändras till "alla" så att både operativ systemet och den nya datadisken tas med. Om den virtuella datorn har krypterats med endast volym typen "data" kan den vara kvar "data" som visas nedan. Det räcker inte att lägga till och ansluta en ny datadisk till en virtuell dator för kryptering. Den nyligen anslutna disken måste också formateras och monteras korrekt i den virtuella datorn innan krypteringen aktive ras. På Linux måste disken monteras i/etc/fstab med ett [beständigt block enhets namn](../troubleshooting/troubleshoot-device-names-problems.md).  

Till skillnad från PowerShell-syntaxen kräver CLI inte att användaren anger en unik sekvens-version när krypteringen aktive ras. CLI genererar automatiskt och använder sitt eget unika sekvens versions värde.

-  **Kryptera data volymer på en virtuell dator som körs:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera data volymer på en virtuell dator som körs med KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder PowerShell för att kryptera en ny disk för Linux måste en ny sekvens-version anges. Sekvens-versionen måste vara unik. Skriptet nedan genererar en GUID för sekvens-versionen. Ta en [ögonblicks bild](snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern-skipVmBackup har redan angetts i PowerShell-skripten för att kryptera en nyligen tillagd datadisk.
 

-  **Kryptera data volymer på en virtuell dator som körs:** Skriptet nedan initierar variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension. Resurs gruppen, den virtuella datorn och nyckel valvet bör redan ha skapats som krav. Ersätt MyVirtualMachineResourceGroup, MySecureVM och MySecureVault med dina värden. Godkända värden för parametern-VolumeType är alla, OS och data. Om den virtuella datorn tidigare har krypterats med volym typen "OS" eller "alla", ska parametern-VolumeType ändras till "alla" så att både operativ systemet och den nya datadisken tas med.

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
- **Kryptera data volymer på en virtuell dator som körs med KEK:** Godkända värden för parametern-VolumeType är alla, OS och data. Om den virtuella datorn tidigare har krypterats med volym typen "OS" eller "alla", ska parametern-VolumeType ändras till alla så att både operativ systemet och den nya datadisken tas med.

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
    > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 


## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Azure Disk Encryption fungerar inte för följande scenarier, funktioner och teknik för Linux:

- Kryptering av virtuella datorer på Basic-nivå eller virtuella datorer som skapats via den klassiska skapande metoden för virtuella datorer.
- Inaktivera kryptering på en operativ system enhet eller data enhet på en virtuell Linux-dator när operativ system enheten är krypterad.
- Kryptering av OS-enheten för skalnings uppsättningar för virtuella Linux-datorer.
- Kryptera anpassade avbildningar på virtuella Linux-datorer.
- Integrering med ett lokalt nyckel hanterings system.
- Azure Files (delat fil system).
- NFS (Network File System).
- Dynamiska volymer.
- Tillfälliga OS-diskar.
- Kryptering av delade/distribuerade fil system som (men inte begränsat till): DFS, GFS, DRDB och CephFS.
- Flytta en krypterad virtuell dator till en annan prenumeration eller region.
- Skapa en avbildning eller ögonblicks bild av en krypterad virtuell dator och använda den för att distribuera ytterligare virtuella datorer.
- Kernel-krasch dump (kdump).
- Oracle-ACFS (ASM Cluster File System).
- Virtuella Gen2-datorer (se: [stöd för virtuella datorer i generation 2 på Azure](generation-2.md#generation-1-vs-generation-2-capabilities)).
- NVMe-diskarna för virtuella datorer i Lsv2-serien (se: [Lsv2-serien](../lsv2-series.md)).
- En virtuell dator med "kapslade monterings punkter"; det vill säga flera monterings punkter i en enda sökväg (till exempel "/1stmountpoint/data/2stmountpoint").
- En virtuell dator med en data enhet som är monterad ovanpå en OS-mapp.
- Virtuella datorer i M-serien med Skrivningsaccelerator diskar.
- Använda ADE på en virtuell dator som har eller *någonsin* haft diskar som är krypterade med [kryptering på Server sidan med Kundhanterade nycklar](disk-encryption.md) (SSE + CMK). Att använda SSE + CMK till en datadisk på en virtuell dator som är krypterad med ADE är ett scenario som inte stöds.
- Migrera en virtuell dator som har krypterats med ADE till [kryptering på Server sidan med Kundhanterade nycklar](disk-encryption.md).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
- [Exempelskript för Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
