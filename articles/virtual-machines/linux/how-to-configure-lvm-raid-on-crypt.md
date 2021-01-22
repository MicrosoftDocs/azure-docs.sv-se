---
title: Konfigurera LVM och RAID på krypterade enheter – Azure Disk Encryption
description: Den här artikeln innehåller instruktioner för att konfigurera LVM och RAID på krypterade enheter för virtuella Linux-datorer.
author: jofrance
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3f90d5a95d153405f9257258fba6ab9cc1ce9a35
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681310"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Konfigurera LVM och RAID på krypterade enheter

Den här artikeln är en steg-för-steg-process för att utföra hantering av logiska volymer (LVM) och RAID på krypterade enheter. Processen gäller för följande miljöer:

- Linux-distributioner
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Azure Disk Encryption tillägg med ett enda pass
- Azure Disk Encryption Dual-pass-tillägg


## <a name="scenarios"></a>Scenarier

Procedurerna i den här artikeln har stöd för följande scenarier:  

- Konfigurera LVM ovanpå krypterade enheter (LVM-on-Encryption)
- Konfigurera RAID ovanpå krypterade enheter (RAID-on-Encryption)

När den underliggande enheten eller enheterna har krypterats kan du skapa LVM-eller RAID-strukturer ovanpå det krypterade lagret. 

De fysiska volymerna (PVs) skapas ovanpå det krypterade skiktet. De fysiska volymerna används för att skapa volym gruppen. Du skapar volymerna och lägger till nödvändiga poster på/etc/fstab. 

![Diagram över lagren i LVM-strukturer](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

På ett liknande sätt skapas RAID-enheten ovanpå det krypterade skiktet på diskarna. Ett fil system skapas ovanpå RAID-enheten och läggs till i/etc/fstab som en vanlig enhet.

## <a name="considerations"></a>Överväganden

Vi rekommenderar att du använder LVM-on-Encryption. RAID är ett alternativ när LVM inte kan användas på grund av specifika program-eller miljö begränsningar.

Du använder alternativet **EncryptFormatAll** . Mer information om det här alternativet finns i [använda funktionen EncryptFormatAll för data diskar på virtuella Linux-datorer](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Även om du kan använda den här metoden när du också krypterar operativ systemet, krypterar vi bara data enheter här.

Procedurerna förutsätter att du redan har granskat kraven i [Azure Disk Encryption scenarier på virtuella Linux-datorer](./disk-encryption-linux.md) och [snabb start: skapa och kryptera en virtuell Linux-dator med Azure CLI](./disk-encryption-cli-quickstart.md).

Azure Disk Encryption Dual-pass-versionen är på en utgångs väg och bör inte längre användas för nya krypteringar.

## <a name="general-steps"></a>Allmänna steg

När du använder "på-kryptering"-konfigurationer använder du processen som beskrivs i följande procedurer.

>[!NOTE] 
>Vi använder variabler i artikeln. Ersätt värdena enligt detta.

### <a name="deploy-a-vm"></a>Distribuera en virtuell dator 
Följande kommandon är valfria, men vi rekommenderar att du tillämpar dem på en nyligen distribuerad virtuell dator (VM).

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```azurecli
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Koppla diskar till den virtuella datorn
Upprepa följande kommandon för `$N` antalet nya diskar som du vill ansluta till den virtuella datorn.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```azurecli
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Kontrol lera att diskarna är anslutna till den virtuella datorn
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lista över anslutna diskar i PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```azurecli
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lista över anslutna diskar i Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lista över anslutna diskar i portalen](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Operativsystem:

```bash
lsblk 
```
![Lista över anslutna diskar i operativ systemet](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurera diskarna som ska krypteras
Den här konfigurationen görs på operativ system nivå. Motsvarande diskar har kon figurer ATS för en traditionell kryptering via Azure Disk Encryption:

- Fil systemen skapas ovanpå diskarna.
- Tillfälliga monterings punkter skapas för att montera fil systemen.
- Fil system konfigureras på/etc/fstab som ska monteras vid start.

Kontrol lera enhets beteckningen som tilldelats de nya diskarna. I det här exemplet ska vi använda fyra data diskar.

```bash
lsblk 
```
![Data diskar som är anslutna till operativ systemet](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Skapa ett fil system ovanpå varje disk
Det här kommandot upprepar skapandet av ett ext4-filsystem på varje disk som definierats i delen "i" i cykeln "for".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Skapa ett ext4 fil system](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Hitta den universella unika identifieraren (UUID) för de fil system som du nyss skapade, skapa en tillfällig mapp, Lägg till motsvarande poster på/etc/fstab och montera alla fil system.

Detta kommando itererar också på varje disk som definieras i "i"-delen av cykeln "for":

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Kontrol lera att diskarna är korrekt monterade
```bash
lsblk
```
![Lista över monterade temporära fil system](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Kontrol lera också att diskarna har kon figurer ATS:

```bash
cat /etc/fstab
```
![Konfigurations information via fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Kryptera data diskarna
PowerShell med en nyckel krypterings nyckel (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI med hjälp av en KEK:

```azurecli
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>Verifiera krypterings status

Fortsätt till nästa steg bara när alla diskar är krypterade.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Krypterings status i PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```azurecli
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Krypterings status i Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Krypterings status i portalen](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

OS-nivå:

```bash
lsblk
```
![Krypterings status i operativ systemet](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Tillägget kommer att lägga till fil systemen till/var/lib/azure_disk_encryption_config/azure_crypt_mount (en gammal kryptering) eller till/etc/crypttab (nya krypteringar).

>[!NOTE] 
>Ändra inte någon av dessa filer.

Den här filen tar hand om aktivering av diskarna under start processen så att LVM eller RAID kan använda dem senare. 

Oroa dig inte över monterings punkterna för den här filen. Azure Disk Encryption förlorar möjligheten att få diskarna monterade som ett normalt fil system efter att vi skapat en fysisk volym eller en RAID-enhet ovanpå de krypterade enheterna. (Det fil system format som vi använde under förberedelse processen tas bort.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Ta bort temporära mappar och temporära fstab-poster
Du avmonterar fil systemen på de diskar som ska användas som en del av LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
Och ta bort/etc/fstab-posterna:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Kontrol lera att diskarna inte är monterade och att posterna på/etc/fstab har tagits bort

```bash
lsblk
```
![Verifiering av temporära fil system demonteras](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

Och kontrol lera att diskarna har kon figurer ATS:
```bash
cat /etc/fstab
```
![Verifiera att tillfälliga fstab-poster tas bort](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Steg för LVM-on-Encryption
Nu när de underliggande diskarna är krypterade kan du skapa LVM-strukturer.

I stället för att använda namnet på enheten använder du/dev/mapper-sökvägar för var och en av diskarna för att skapa en fysisk volym (på krypterings skiktet, inte på själva disken).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurera LVM ovanpå de krypterade skikten
#### <a name="create-the-physical-volumes"></a>Skapa de fysiska volymerna
En varning visas som frågar om det är OK att rensa fil systemets signatur. Fortsätt genom att ange **y** eller Använd **ECHO "y"** som visas:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verifierar att en fysisk volym har skapats](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>/Dev/mapper/Device namn måste ersättas för dina faktiska värden baserat på utdata från **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Verifiera informationen för fysiska volymer
```bash
pvs
```

![Information om fysiska volymer](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Skapa volym gruppen
Skapa volym gruppen med hjälp av samma enheter som redan har initierats:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Kontrol lera informationen för volym gruppen

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Information för volym gruppen](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Skapa logiska volymer

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Kontrol lera de skapade logiska volymerna

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Information för logiska volymer](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Skapa fil system ovanpå strukturerna för logiska volymer

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Skapa monterings punkter för de nya fil systemen

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Lägg till de nya fil systemen i/etc/fstab och montera dem

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Kontrol lera att de nya fil systemen är monterade

```bash
lsblk -fs
df -h
```
![Skärm bild som visar ett konsol fönster med fil system som monterats som Data0 och fil1.](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

På den här varianten av **lsblk** visar vi enheterna som visar beroendena i omvänd ordning. Med det här alternativet kan du identifiera vilka enheter som grupperas av den logiska volymen i stället för den ursprungliga/dev/SD [disk] enhets namn.

Det är viktigt att se till att alternativet **nomisslyckande** har lagts till i monterings punkt alternativen för de LVM-volymer som skapas ovanpå en enhet som är krypterad via Azure Disk Encryption. Det förhindrar att operativ systemet blir fastnat under start processen (eller i underhålls läge).

Om du inte använder alternativet **nomisslyckande** :

- Operativ systemet kommer aldrig att komma in i det stadium där Azure Disk Encryption startas och data diskarna låses upp och monteras. 
- De krypterade diskarna kommer att låsas upp i slutet av start processen. LVM-volymerna och fil systemen monteras automatiskt tills Azure Disk Encryption låser upp dem. 

Du kan testa att starta om den virtuella datorn och kontrol lera att fil systemen också automatiskt kommer att bli monterad efter start tiden. Den här processen kan ta flera minuter, beroende på fil systemets antal och storlek.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Starta om den virtuella datorn och verifiera efter omstart

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Steg för RAID-on-Encryption
Nu när de underliggande diskarna är krypterade kan du fortsätta att skapa RAID-strukturerna. Processen är densamma som för LVM, men i stället för att använda namnet på enheten använder du/dev/mapper-sökvägar för varje disk.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Konfigurera RAID ovanpå de krypterade skikten på diskarna
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Information om konfigurerad RAID via kommandot mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>/Dev/mapper/Device namn måste ersättas med dina faktiska värden, baserat på utdata från **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Kontrol lera/övervaka RAID-skapande
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Status för RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Skapa ett fil system ovanpå den nya RAID-enheten
```bash
mkfs.ext4 /dev/md10
```

Skapa en ny monterings punkt för fil systemet, Lägg till det nya fil systemet i/etc/fstab och montera den:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Kontrol lera att det nya fil systemet är monterat:

```bash
lsblk -fs
df -h
```
![Skärm bild som visar ett konsol fönster med ett fil system monterat som raiddata.](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Det är viktigt att se till att alternativet **nomisslyckande** har lagts till i monterings punkt alternativen för de RAID-volymer som skapats ovanpå en enhet som är krypterad via Azure Disk Encryption. Det förhindrar att operativ systemet blir fastnat under start processen (eller i underhålls läge).

Om du inte använder alternativet **nomisslyckande** :

- Operativ systemet kommer aldrig att komma in i det stadium där Azure Disk Encryption startas och data diskarna låses upp och monteras.
- De krypterade diskarna kommer att låsas upp i slutet av start processen. RAID-volymerna och fil systemen monteras automatiskt tills Azure Disk Encryption låser upp dem.

Du kan testa att starta om den virtuella datorn och kontrol lera att fil systemen också automatiskt kommer att bli monterad efter start tiden. Den här processen kan ta flera minuter, beroende på fil systemets antal och storlek.

```bash
shutdown -r now
```

Och när du kan logga in:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Nästa steg

- [Ändra storlek på logiska volym hanterings enheter som har krypterats med Azure Disk Encryption](how-to-resize-encrypted-lvm.md)
- [Felsökning för Azure Disk Encryption](disk-encryption-troubleshooting.md)
