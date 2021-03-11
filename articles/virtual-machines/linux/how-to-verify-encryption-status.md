---
title: Verifiera krypterings status för Linux – Azure Disk Encryption
description: Den här artikeln innehåller anvisningar om hur du verifierar krypterings statusen från plattforms-och OS-nivåerna.
author: kailashmsft
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 04654672cc5806465ec9f75b695772dcb2037eab
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564143"
---
# <a name="verify-encryption-status-for-linux"></a>Verifiera krypterings status för Linux 

Omfånget för den här artikeln är att verifiera krypterings statusen för en virtuell dator med hjälp av olika metoder: Azure Portal, PowerShell, Azure CLI eller operativ systemet för den virtuella datorn (VM). 

Du kan verifiera krypterings statusen under eller efter krypteringen genom att antingen:

- Kontrollerar diskarna som är anslutna till en viss virtuell dator. 
- Fråga krypterings inställningarna på varje disk, oavsett om disken är ansluten eller ej ansluten.

Det här scenariot gäller för Azure Disk Encryption dubbla pass och ett enda pass-tillägg. Linux-distributioner är den enda miljön för det här scenariot.

>[!NOTE] 
>Vi använder variabler i artikeln. Ersätt värdena enligt detta.

## <a name="portal"></a>Portal

I Azure Portal, i avsnittet **tillägg** , väljer du Azure Disk Encryption tillägget i listan. Informationen för **status meddelandet** anger aktuell krypterings status:

![Portal kontroll med status, version och status meddelande markerat](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

I listan över tillägg ser du motsvarande Azure Disk Encryption tilläggs version. Version 0. x motsvarar Azure Disk Encryption dubbelt pass och version 1. x motsvarar Azure Disk Encryption enda pass.

Du kan få mer information genom att välja tillägget och sedan välja **Visa detaljerad status**. Detaljerad status för krypterings processen visas i JSON-format.

![Portal kontroll med länken "Visa detaljerad status" markerad](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Detaljerad status i JSON-format](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Ett annat sätt att verifiera krypterings statusen är att titta i avsnittet **disk inställningar** .

![Krypterings status för OS-diskar och data diskar](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Denna status innebär att diskarna har krypterings inställningarna stämplade, inte att de faktiskt har krypterats på operativ system nivå.
>
> Som design, stämplas diskarna först och krypteras senare. Om krypterings processen Miss lyckas kan diskarna tillföras stämplade men inte krypterade. 
>
> För att bekräfta att diskarna verkligen är krypterade kan du kontrol lera krypteringen för varje disk på operativ system nivå.

## <a name="powershell"></a>PowerShell

Du kan verifiera den *allmänna* krypterings statusen för en krypterad virtuell dator med hjälp av följande PowerShell-kommandon:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Allmän krypterings status i PowerShell](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Du kan avbilda krypterings inställningarna från varje disk med hjälp av följande PowerShell-kommandon.

### <a name="single-pass"></a>Enda steg
I ett enda steg stämplas krypterings inställningarna på varje disk (OS och data). Du kan avbilda krypterings inställningarna för en OS-disk i ett enda steg på följande sätt:

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Krypterings inställningar för en OS-disk](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Om det inte finns några tidsbegränsade krypterings inställningar för disken är utdata tomt:

![Tomma utdata](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Använd följande kommandon för att avbilda krypterings inställningar för data diskar:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Krypterings inställningar för data diskar](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dubbelt pass
I ett dubbelt pass stämplas krypterings inställningarna i den virtuella dator modellen och inte på varje enskild disk.

Om du vill kontrol lera att krypterings inställningarna har stämplats i ett dubbelt pass använder du följande kommandon:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Krypterings inställningar i ett dubbelt pass](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Ej anslutna diskar

Kontrol lera krypterings inställningarna för diskar som inte är kopplade till en virtuell dator.

### <a name="managed-disks"></a>Hanterade diskar
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI

Du kan verifiera den *allmänna* krypterings statusen för en krypterad virtuell dator med hjälp av följande Azure CLI-kommandon:

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Allmän krypterings status från Azure CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Enda steg
Du kan verifiera krypterings inställningarna för varje disk med hjälp av följande Azure CLI-kommandon:

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Inställningar för data kryptering](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Om det inte finns några tidsstämplade krypterings inställningar på disken, ser du att text **disken inte är krypterad**.

Använd följande kommandon för att få detaljerade inställningar för status och kryptering.

OS-disk:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Detaljerade status-och krypterings inställningar för OS-disken](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Data diskar:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Detaljerade status-och krypterings inställningar för data diskarna](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Dubbelt pass

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Allmänna krypterings inställningar för dubbel pass via Azure CLI](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

Du kan också kontrol lera krypterings inställningarna för den virtuella datorns modell lagrings profil för OS-disken:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![VM-profil för dubbel pass via Azure CLI](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Ej anslutna diskar

Kontrol lera krypterings inställningarna för diskar som inte är kopplade till en virtuell dator.

### <a name="managed-disks"></a>Hanterade diskar

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Ohanterade diskar

Ohanterade diskar är VHD-filer som lagras som Page blobbar i Azure Storage-konton.

För att få information om en speciell disk måste du ange:

- ID för det lagrings konto som innehåller disken.
- En anslutnings sträng för just det lagrings kontot.
- Namnet på behållaren som lagrar disken.
- Disk namnet.

Det här kommandot visar alla ID: n för alla dina lagrings konton:

```azurecli
az storage account list --query [].[id] -o tsv
```
Lagrings kontots ID: n visas i följande format:

/Subscriptions/ \<subscription id> /resourceGroups/- \<resource group name> /providers/Microsoft.Storage/storageAccounts/\<storage account name>

Välj lämpligt ID och lagra det på en variabel:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Det här kommandot hämtar anslutnings strängen för ett visst lagrings konto och lagrar det på en variabel:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Följande kommando visar alla behållare under ett lagrings konto:
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Den behållare som används för diskar kallas vanligt vis "VHD."

Lagra behållar namnet på en variabel: 
```bash
ContainerName="name of the container"
```

Använd det här kommandot för att visa en lista över alla blobbar i en viss behållare:
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Välj den disk som du vill fråga efter och lagra namnet på en variabel:
```bash
DiskName="diskname.vhd"
```
Fråga om disk krypterings inställningarna:
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>Operativsystem
Verifiera om datadiskpartitionerna är krypterade (och OS-disken inte).

När en partition eller disk krypteras visas den som en **krypterings** typ. När den inte är krypterad visas den som en **del/disk** -typ.

```bash
lsblk
```

![Operativ system krypterings skikt för en partition](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Du kan få mer information genom att använda följande **lsblk** -variant. 

Du ser ett **krypterings** typ lager som är monterat av tillägget. I följande exempel visas logiska volymer och normala diskar med **kryptering \_ LUKS FSTYPE**.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Operativ system krypterings skikt för logiska volymer och normala diskar](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Som ett extra steg kan du kontrol lera om data disken har lästa nycklar:

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

Och du kan kontrol lera vilka **DM** -enheter som listas som **kryptering**:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Nästa steg

- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
