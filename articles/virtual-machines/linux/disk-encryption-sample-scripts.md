---
title: Exempelskript för Azure Disk Encryption
description: Den här artikeln är bilagan till Microsoft Azure disk kryptering för virtuella Linux-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: f11677d9ebc31f1c1f7cc6332b07b69f8e35ad52
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561185"
---
# <a name="azure-disk-encryption-sample-scripts-for-linux-vms"></a>Azure Disk Encryption exempel skript för virtuella Linux-datorer

Den här artikeln innehåller exempel skript för att förbereda förkrypterade virtuella hård diskar och andra uppgifter.  

> [!NOTE]
> Alla skript avser den senaste, icke-AAD-versionen av ADE, förutom där det anges.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Exempel på PowerShell-skript för Azure Disk Encryption 

- **Lista alla krypterade virtuella datorer i din prenumeration**
  
  Du kan hitta alla ADE-krypterade virtuella datorer och tilläggs versionen i alla resurs grupper som finns i en prenumeration med hjälp av [det här PowerShell-skriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1).

  Dessa cmdletar visar också alla ADE-krypterade virtuella datorer (men inte tilläggs versionen):

   ```azurepowershell-interactive
   $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
   $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
   Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
   ```

- **Visa en lista över alla krypterade VMSS-instanser i din prenumeration**
    
    Du kan hitta alla ADE-krypterade VMSS-instanser och tilläggs versionen i alla resurs grupper som finns i en prenumeration med hjälp av [det här PowerShell-skriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1).

- **Lista alla disk krypterings hemligheter som används för att kryptera virtuella datorer i ett nyckel valv** 

   ```azurepowershell-interactive
   Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
   ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Använda PowerShell-skriptet Azure Disk Encryption krav
Om du redan är bekant med kraven för Azure Disk Encryption kan du använda [PowerShell-skriptet Azure Disk Encryption krav](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Ett exempel på hur du använder PowerShell-skriptet finns i [kryptera en snabb start för virtuell dator](disk-encryption-powershell-quickstart.md). Du kan ta bort kommentarerna från ett avsnitt i skriptet, med början på rad 211, för att kryptera alla diskar för befintliga virtuella datorer i en befintlig resurs grupp. 

I följande tabell visas vilka parametrar som kan användas i PowerShell-skriptet: 


|Parameter|Beskrivning|Erforderlig?|
|------|------|------|
|$resourceGroupName| Namnet på den resurs grupp som nyckel valvet tillhör.  En ny resurs grupp med det här namnet kommer att skapas om det inte finns någon.| Sant|
|$keyVaultName|Namnet på det nyckel valv där krypterings nycklar ska placeras. Ett nytt valv med det här namnet kommer att skapas om det inte finns något.| Sant|
|$location|Plats för nyckel valvet. Se till att det nyckel valv och de virtuella datorer som ska krypteras finns på samma plats. Hämta en innehållsplatslista med `Get-AzLocation`.|Sant|
|$subscriptionId|Identifierare för den Azure-prenumeration som ska användas.  Du kan hämta ditt prenumerations-ID med `Get-AzSubscription`.|Sant|
|$aadAppName|Namnet på det Azure AD-program som ska användas för att skriva hemligheter till nyckel valv. Om det inte redan finns ett program med det namnet skapas ett nytt. Om den här appen redan finns skickar du aadClientSecret-parametern till skriptet.|Falskt|
|$aadClientSecret|Klient hemlighet för Azure AD-programmet som skapades tidigare.|Falskt|
|$keyEncryptionKeyName|Namn på valfri nyckel krypterings nyckel i nyckel valvet. En ny nyckel med det här namnet kommer att skapas om det inte finns någon.|Falskt|

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Kryptera eller dekryptera virtuella datorer utan en Azure AD-App

- [Aktivera disk kryptering på en befintlig eller virtuell Linux-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Inaktivera kryptering på en virtuell Linux-dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Det går bara att inaktivera kryptering på data volymer för virtuella Linux-datorer.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Kryptera eller dekryptera virtuella datorer med en Azure AD-App (tidigare version)
 
- [Aktivera disk kryptering på en befintlig eller virtuell Linux-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Inaktivera kryptering på en virtuell Linux-dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Det går bara att inaktivera kryptering på data volymer för virtuella Linux-datorer. 


- [Skapa en ny krypterad hanterad disk från en förkrypterad VHD/Storage-BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Skapar en ny krypterad hanterad disk som tillhandahöll en förkrypterad virtuell hård disk och dess motsvarande krypterings inställningar

## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Kryptera en OS-enhet på en virtuell Linux-dator som körs

### <a name="prerequisites-for-os-disk-encryption"></a>Krav för disk kryptering i operativ system

* Den virtuella datorn måste använda en distribution som är kompatibel med disk kryptering för operativ system som anges i [Azure Disk Encryption operativ system som stöds](disk-encryption-overview.md#supported-vms) 
* Den virtuella datorn måste skapas från Marketplace-avbildningen i Azure Resource Manager.
* Virtuell Azure-dator med minst 4 GB RAM-minne (rekommenderad storlek är 7 GB).
* (För RHEL och CentOS) Inaktivera SELinux. Information om hur du inaktiverar SELinux finns i "4.4.2. Inaktivera SELinux i SELinux- [användarens och administratörs guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) på den virtuella datorn.
* När du har inaktiverat SELinux startar du om den virtuella datorn minst en gång.

### <a name="steps"></a>Steg
1. Skapa en virtuell dator genom att använda en av de angivna distributionerna tidigare.

   För CentOS 7,2 stöds operativ system disk kryptering via en särskild avbildning. Om du vill använda den här avbildningen anger du "7,2 n" som SKU när du skapar den virtuella datorn:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurera den virtuella datorn efter dina behov. Om du ska kryptera alla (OS + data)-enheter måste data enheterna anges och monteras från/etc/fstab.

   > [!NOTE]
   > Använd UUID =... Ange data enheter i/etc/fstab i stället för att ange block enhets namnet (till exempel/dev/sdb1). Under krypteringen ändras ordningen på enheterna på den virtuella datorn. Om den virtuella datorn är beroende av en speciell ordning av block enheter, kommer den inte att montera dem efter kryptering.

3. Logga ut från SSH-sessionerna.

4. Om du vill kryptera operativ systemet anger du volumeType som **alla** eller **OS** när du aktiverar kryptering.

   > [!NOTE]
   > Alla processer för användar utrymme som inte körs som `systemd` tjänster ska avlivas med en `SIGKILL` . Starta om den virtuella datorn. När du aktiverar disk kryptering för operativ systemet på en virtuell dator som körs kan du planera om nedtid för virtuella datorer.

5. Övervaka regelbundet förloppet för krypteringen med hjälp av anvisningarna i [Nästa avsnitt](#monitoring-os-encryption-progress).

6. När Get-AzVmDiskEncryptionStatus visar "VMRestartPending" startar du om den virtuella datorn antingen genom att logga in på den eller med hjälp av portalen, PowerShell eller CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Innan du startar om rekommenderar vi att du sparar [startdiagnostiken](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) för den virtuella datorn.

## <a name="monitoring-os-encryption-progress"></a>Övervaka operativ system krypterings förlopp
Du kan övervaka operativ systemets krypterings förlopp på tre sätt:

* Använd `Get-AzVmDiskEncryptionStatus` cmdleten och kontrol lera fältet ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  När den virtuella datorn har nått "OS disk Encryption startade" tar det cirka 40 till 50 minuter på en virtuell dator med Premium Storage.

  På grund av [problem #388](https://github.com/Azure/WALinuxAgent/issues/388) i WALinuxAgent `OsVolumeEncrypted` och `DataVolumesEncrypted` visas som `Unknown` i vissa distributioner. Med WALinuxAgent version 2.1.5 och senare åtgärdas det här problemet automatiskt. Om du ser `Unknown` i utdata kan du kontrol lera disk krypterings statusen med hjälp av Azure Resource Explorer.

  Gå till [Azure Resource Explorer](https://resources.azure.com/)och expandera sedan den här hierarkin på urvals panelen till vänster:

  ```
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ```                

  Rulla nedåt i InstanceView för att se enhetens krypterings status.

  ![Vy för VM-instans](./media/disk-encryption/vm-instanceview.png)

* Titta på [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Meddelanden från ADE-tillägget ska föregås av `[AzureDiskEncryption]` .

* Logga in på den virtuella datorn via SSH och hämta tilläggs loggen från:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Vi rekommenderar att du inte loggar in på den virtuella datorn medan operativ system kryptering pågår. Kopiera bara loggarna när de andra två metoderna har misslyckats.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Förbereda en förkrypterad virtuell Linux-hårddisk
Förberedelsen för förkrypterade virtuella hård diskar kan variera beroende på distribution. Exempel på förberedelse av Ubuntu 16, openSUSE 13,2 och CentOS 7 är tillgängliga. 

### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurera kryptering under installationen av distributionen genom att utföra följande steg:

1. Välj **Konfigurera krypterade volymer** när du partitionerar diskarna.

   ![Ubuntu 16,04-konfiguration – konfigurera krypterade volymer](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Skapa en separat startenhet som inte får vara krypterad. Kryptera rot enheten.

   ![Ubuntu 16,04-installation – välj enheter som ska krypteras](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Ange en lösen fras. Det här är den lösen fras som du laddade upp till nyckel valvet.

   ![Ubuntu 16,04-installation-ange lösen fras](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Slutför partitionering.

   ![Ubuntu 16,04-installation-Slutför partitionering](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. När du startar den virtuella datorn och uppmanas att ange en lösen fras använder du den lösen fras du angav i steg 3.

   ![Ubuntu 16,04-installation-ange lösen fras vid start](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Förbered den virtuella datorn för uppladdning i Azure med hjälp av [de här anvisningarna](./create-upload-ubuntu.md?toc=/azure/virtual-machines/linux/toc.json). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

Konfigurera kryptering så att den fungerar med Azure genom att utföra följande steg:

1. Skapa en fil under/usr/local/sbin/azure_crypt_key. sh med innehållet i följande skript. Var uppmärksam på fil namns namnet eftersom det är det lösen Frass fil namn som används av Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Ändra config config i */etc/crypttab*. Den bör se ut så här:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Lägg till körbara behörigheter i skriptet:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Redigera */etc/initramfs-tools/modules* genom att lägga till rader:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Kör `update-initramfs -u -k all` för att uppdatera initramfs så att `keyscript` börjar gälla.

7. Nu kan du avetablera den virtuella datorn.

   ![Ubuntu 16,04-installation-Update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Fortsätt till nästa steg och ladda upp den virtuella hård disken i Azure.

### <a name="opensuse-132"></a>openSUSE 13,2
Gör så här för att konfigurera kryptering under installationen av distributionen:
1. När du partitionerar diskarna väljer du **kryptera volym grupp** och anger ett lösen ord. Det här är det lösen ord som du överför till ditt nyckel valv.

   ![openSUSE 13,2-konfiguration – kryptera volym grupp](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Starta den virtuella datorn med ditt lösen ord.

   ![openSUSE 13,2-installation-ange lösen fras vid start](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Förbered den virtuella datorn för uppladdning till Azure genom att följa anvisningarna i [förbereda en virtuell SLES-eller openSUSE-dator för Azure](./suse-create-upload-vhd.md?toc=/azure/virtual-machines/linux/toc.json#prepare-opensuse-131). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

Gör så här för att konfigurera kryptering för att arbeta med Azure:
1. Redigera/etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Kommentera de här raderna i slutet av filen/usr/lib/dracut/modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Lägg till följande rad i början av filen/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Och ändra alla förekomster av:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   till:
   ```bash
    if [ 1 ]; then
   ```
4. Redigera/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och Lägg till det i "# Open LUKS Device":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Kör `/usr/sbin/dracut -f -v` för att uppdatera initrd.

6. Nu kan du avetablera den virtuella datorn och ladda upp den virtuella hård disken i Azure.

### <a name="centos-7-and-rhel-7"></a>CentOS 7 och RHEL 7

Gör så här för att konfigurera kryptering under installationen av distributionen:
1. Välj **kryptera mina data** när du partitionerar diskar.

   ![CentOS 7-installation-installations mål](./media/disk-encryption/centos-encrypt-fig1.png)

2. Kontrol lera att **kryptera** har valts för rotnoden.

   ![Installation av CentOS 7 – Välj kryptera för rotnod](./media/disk-encryption/centos-encrypt-fig2.png)

3. Ange en lösen fras. Det här är den lösen fras som du överför till ditt nyckel valv.

   ![CentOS 7-installation-ange lösen fras](./media/disk-encryption/centos-encrypt-fig3.png)

4. När du startar den virtuella datorn och uppmanas att ange en lösen fras använder du den lösen fras du angav i steg 3.

   ![CentOS 7-installation-ange lösen fras på Start](./media/disk-encryption/centos-encrypt-fig4.png)

5. Förbered den virtuella datorn för uppladdning i Azure med hjälp av anvisningarna "CentOS 7.0 +" i [förbereda en CentOS-baserad virtuell dator för Azure](./create-upload-centos.md?toc=/azure/virtual-machines/linux/toc.json#centos-70). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

6. Nu kan du avetablera den virtuella datorn och ladda upp den virtuella hård disken i Azure.

Gör så här för att konfigurera kryptering för att arbeta med Azure:

1. Redigera/etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Kommentera de här raderna i slutet av filen/usr/lib/dracut/modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Lägg till följande rad i början av filen/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Och ändra alla förekomster av:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   på
   ```bash
    if [ 1 ]; then
   ```
4. Redigera/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och Lägg till följande efter "# Open LUKS Device":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Kör "/usr/sbin/dracut-f-v" för att uppdatera initrd.

    ![CentOS 7-installation-kör/usr/sbin/dracut-f-v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Ladda upp krypterad virtuell hård disk till ett Azure Storage-konto
När DM-Crypt kryptering har Aktiver ATS måste den lokala krypterade virtuella hård disken överföras till ditt lagrings konto.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Överför hemligheten för den förkrypterade virtuella datorn till ditt nyckel valv
När du krypterar med hjälp av en Azure AD-App (tidigare version) måste disk krypterings hemligheten som du har fått laddas upp som en hemlighet i ditt nyckel valv. Nyckel valvet måste ha disk kryptering och-behörigheter aktiverade för Azure AD-klienten.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Disk krypterings hemligheten är inte krypterad med en KEK
Använd [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)för att ställa in hemligheten i nyckel valvet. Lösen frasen kodas som en Base64-sträng och överförs sedan till nyckel valvet. Se dessutom till att följande Taggar anges när du skapar hemligheten i nyckel valvet.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Använd `$secretUrl` i nästa steg för att [koppla OS-disken utan att använda KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Disk krypterings hemlighet krypterad med en KEK
Innan du överför hemligheten till nyckel valvet kan du också kryptera den med hjälp av en nyckel krypterings nyckel. Använd wrap- [API: et](/rest/api/keyvault/wrapkey) för att först kryptera hemligheten med nyckel krypterings nyckeln. Utdata från den här figur sättningen är en Base64-kodad sträng, som du sedan kan ladda upp som en hemlighet med hjälp av [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdleten.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Använd `$KeyEncryptionKey` och `$secretUrl` i nästa steg för att [koppla OS-disken med hjälp av KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Ange en hemlig URL när du ansluter en OS-disk

###  <a name="without-using-a-kek"></a>Utan att använda en KEK
När du ansluter OS-disken måste du skicka `$secretUrl` . URL: en skapades i avsnittet "disk krypterings hemlighet som inte är krypterad med ett KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Använda en KEK
När du ansluter OS-disken skickar du `$KeyEncryptionKey` och `$secretUrl` . URL: en skapades i avsnittet "disk krypterings hemlighet krypterad med ett KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
