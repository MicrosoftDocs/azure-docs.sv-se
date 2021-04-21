---
title: Azure Disk Encryption med Azure AD App virtuella Linux IaaS-datorer (tidigare version)
description: Den här artikeln innehåller anvisningar om hur du Microsoft Azure diskkryptering för virtuella Linux IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 94440b71eee1ff9dcc4a86733582e3e5f57f6a00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764629"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Aktivera Azure Disk Encryption azure AD på virtuella Linux-datorer (tidigare version)

Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure Active Directory (Azure AD)-programparameter för att aktivera VM-diskkryptering. Med den nya versionen behöver du inte längre ange Autentiseringsuppgifter för Azure AD under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programparametrarna med hjälp av den nya versionen. Anvisningar om hur du aktiverar diskkryptering för virtuella datorer med den nya versionen finns i [Azure Disk Encryption linux VMS](disk-encryption-linux.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntaxen.

Du kan aktivera många diskkrypteringsscenarier och stegen kan variera beroende på scenariot. I följande avsnitt beskrivs scenarier i detalj för virtuella IaaS-datorer (infrastruktur som en tjänst) för Linux. Du kan bara använda diskkryptering för virtuella datorer med [VM-storlekar och operativsystem som stöds.](disk-encryption-overview.md#supported-vms-and-operating-systems) Du måste också uppfylla följande krav:

- [Ytterligare krav för virtuella datorer](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverk och grupprincip](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Lagringskrav för krypteringsnyckel](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Ta en [ögonblicksbild,](snapshot-copy-managed-disk.md)gör en säkerhetskopia eller båda innan du krypterar diskarna. Säkerhetskopieringar säkerställer att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopiering innan krypteringen sker. När en säkerhetskopia har gjorts kan du använda cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Mer information om hur du återställer krypterade virtuella datorer finns i [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Om du tidigare använde [Azure Disk Encryption Azure AD-appen för](disk-encryption-overview-aad.md) att kryptera den här virtuella datorn måste du fortsätta att använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda [Azure Disk Encryption](disk-encryption-overview.md) på den här krypterade virtuella datorn eftersom detta inte stöds, vilket innebär att växling från Azure AD-programmet för den här krypterade virtuella datorn inte stöds ännu.
 > - För att säkerställa att krypteringshemligheterna inte går över regionala gränser Azure Disk Encryption måste nyckelvalvet och de virtuella datorerna finnas i samma region. Skapa och använd ett nyckelvalv som finns i samma region som den virtuella dator som ska krypteras.
 > - När du krypterar Linux OS-volymer kan processen ta några timmar. Det är normalt att Linux OS-volymer tar längre tid än datavolymer att kryptera.
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att du undviker SSH-inloggningar medan krypteringen pågår för att undvika att blockera öppna filer som behöver nås under krypteringsprocessen. Om du vill kontrollera förloppet använder [du kommandona Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller [vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) Du kan förvänta dig att den här processen tar några timmar för en os-volym på 30 GB, plus ytterligare tid för kryptering av datavolymer. Krypteringstiden för datavolymer är proportionell mot storleken och kvantiteten för datavolymerna, såvida inte **alternativet kryptera format** används. 
 > - Inaktivering av kryptering på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"></a> Aktivera kryptering på en befintlig eller aktiv virtuell IaaS Linux-dator

I det här scenariot kan du aktivera kryptering med hjälp av Azure Resource Manager, PowerShell-cmdlets eller Azure CLI-kommandon. 

>[!IMPORTANT]
 >Det är obligatoriskt att ta en ögonblicksbild eller säkerhetskopiera en hanterad diskbaserad virtuell datorinstans utanför och innan du aktiverar Azure Disk Encryption. Du kan ta en ögonblicksbild av den hanterade disken från Azure Portal, eller så kan du använda [Azure Backup](../../backup/backup-azure-vms-encryption.md). Säkerhetskopieringar säkerställer att ett återställningsalternativ är möjligt om det uppstår oväntade fel under krypteringen. När en säkerhetskopia har gjorts använder du cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot hanterade diskbaserade virtuella datorer tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivering av kryptering kan göra att den virtuella datorn startas om. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Aktivera kryptering på en befintlig eller aktiv virtuell Linux-dator med hjälp av Azure CLI 
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk genom att installera och använda [kommandoradsverktyget Azure CLI 2.0.](/cli/azure) Du kan använda det i webbläsaren med [Azure Cloud Shell](../../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Om du vill aktivera kryptering på befintliga eller köra virtuella IaaS Linux-datorer i Azure använder du följande CLI-kommandon:

Använd kommandot [az vm encryption enable för](/cli/azure/vm/encryption#az_vm_encryption_enable) att aktivera kryptering på en virtuell IaaS-dator som körs i Azure.

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en virtuell dator som körs med kek för att omsluta klienthemligheten:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell IaaS-dator använder du [kommandot az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du [kommandot az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Inaktivering av kryptering tillåts endast på datavolymer för virtuella Linux-datorer.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"></a> Aktivera kryptering på en befintlig eller aktiv virtuell Linux-dator med hjälp av PowerShell
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure. Ta en [ögonblicksbild](snapshot-copy-managed-disk.md) eller gör en säkerhetskopia av den virtuella [Azure Backup](../../backup/backup-azure-vms-encryption.md) innan diskarna krypteras. Parametern -skipVmBackup har redan angetts i PowerShell-skript för att kryptera en virtuell Linux-dator som körs.

- **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** Följande skript initierar dina variabler och kör Set-AzVMDiskEncryptionExtension cmdlet. Resursgruppen, den virtuella datorn, nyckelvalvet, Azure AD-appen och klienthemligheten bör redan ha skapats som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden. Ändra parametern -VolumeType för att ange vilka diskar du krypterar.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Kryptera en virtuell dator som körs med kek för att omsluta klienthemligheten:** Azure Disk Encryption kan du ange en befintlig nyckel i nyckelvalvet för att omsluta diskkrypteringshemligheter som genererades när krypteringen skapades. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption nyckeln för att omsluta krypteringshemligheterna innan de skrivs till nyckelvalvet. Ändra parametern -VolumeType för att ange vilka diskar du krypterar. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[KSource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell IaaS-dator använder du cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Inaktivering av kryptering tillåts endast på datavolymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"></a> Aktivera kryptering på en befintlig eller aktiv virtuell IaaS Linux-dator med en mall

Du kan aktivera diskkryptering på en befintlig eller aktiv virtuell IaaS Linux-dator i Azure med hjälp [Resource Manager mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Välj **Distribuera till Azure i** Azure-snabbstartsmallen.

2. Välj prenumeration, resursgrupp, resursgruppsplats, parametrar, juridiska villkor och avtal. Välj **Skapa för** att aktivera kryptering på den befintliga eller körande virtuella IaaS-datorn.

I följande tabell visas Resource Manager för befintliga eller körande virtuella datorer som använder ett Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Det Azure AD-program som har behörighet att skriva hemligheter till nyckelvalvet. |
| AADClientSecret | Klienthemlighet för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| keyVaultName | Namnet på nyckelvalvet som nyckeln ska laddas upp till. Du kan hämta den med hjälp av Azure CLI-kommandot `az keyvault show --name "MySecureVault" --query KVresourceGroup` . |
|  keyEncryptionKeyURL | URL för den nyckelkrypteringsnyckel som används för att kryptera den genererade nyckeln. Den här parametern är valfri **om du väljer nokek** i listrutan **UseExistingKek.** Om du **väljer kek** i listrutan **UseExistingKek** måste du ange _värdet keyEncryptionKeyURL._ |
| volumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden som stöds är _OS_ eller _Alla_. (Se Linux-distributioner som stöds och deras versioner för operativsystem och datadiskar i avsnittet förutsättningar tidigare.) |
| sequenceVersion | Sekvensversion av BitLocker-åtgärden. Öka versionsnumret varje gång en diskkrypteringsåtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella dator som krypteringsåtgärden ska utföras på. |
| lösenfras | Skriv en stark lösenfras som datakrypteringsnyckel. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Använda funktionen EncryptFormatAll för datadiskar på virtuella Linux IaaS-datorer
Parametern EncryptFormatAll minskar tiden för att Linux-datadiskar ska krypteras. Partitioner som uppfyller vissa kriterier är formaterade (med deras aktuella filsystem). Sedan återmonteras de tillbaka till den plats där de var före kommandokörningen. Om du vill undanta en datadisk som uppfyller villkoren kan du demontera den innan du kör kommandot.

 När du har kört det här kommandot formateras alla enheter som monterats tidigare. Sedan startar krypteringslagret ovanpå den nu tomma enheten. När det här alternativet väljs krypteras även den tillfälliga disken som är ansluten till den virtuella datorn. Om den tillfälliga enheten återställs formateras den om och omkrypteras för den virtuella datorn av Azure Disk Encryption lösningen vid nästa tillfälle.

>[!WARNING]
> EncryptFormatAll ska inte användas när det behövs data på en virtuell dators datavolymer. Du kan undanta diskar från kryptering genom att demontera dem. Prova att använda parametern EncryptFormatAll på en virtuell test-dator först för att förstå funktionsparametern och dess konsekvenser innan du provar den på den virtuella produktionsdatorn. Alternativet EncryptFormatAll formaterar datadisken så att alla data på den går förlorade. Innan du fortsätter kontrollerar du att alla diskar som du vill undanta är korrekt demonterade. </br></br>
 >Om du anger den här parametern när du uppdaterar krypteringsinställningarna kan det leda till en omstart innan den faktiska krypteringen. I det här fallet vill du också ta bort den disk som du inte vill formatera från fstab-filen. På samma sätt bör du lägga till den partition som du vill kryptera formatera i fstab-filen innan du initierar krypteringsåtgärden. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"></a> EncryptFormatAlla kriterier
Parametern går igenom alla partitioner och krypterar dem så länge de *uppfyller* alla följande kriterier: 
- Är inte en rot-/OS-/startpartition
- Är inte redan krypterad
- Är inte en BEK-volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera de diskar som utgör RAID- eller LVM-volymen i stället för RAID- eller LVM-volymen.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"></a> Använda parametern EncryptFormatAll med en mall
Om du vill använda alternativet EncryptFormatAll använder du valfri Azure Resource Manager som krypterar en virtuell Linux-dator och ändrar fältet **EncryptionOperation** för AzureDiskEncryption-resursen.

1. Använd till exempel mallen Resource Manager [för att kryptera en virtuell Linux IaaS-dator som körs.](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm) 
2. Välj **Distribuera till Azure i** Azure-snabbstartsmallen.
3. Ändra fältet **EncryptionOperation** från **EnableEncryption till** **EnableEncryptionFormatAl**.
4. Välj prenumeration, resursgrupp, resursgruppsplats, andra parametrar, juridiska villkor och avtal. Välj **Skapa för** att aktivera kryptering på den befintliga eller körande virtuella IaaS-datorn.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"></a> Använd parametern EncryptFormatAll med en PowerShell-cmdlet
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) med parametern EncryptFormatAll.

**Kryptera en virtuell dator som körs med hjälp av en klienthemlighet och EncryptFormatAll:** Följande skript initierar till exempel variablerna och kör cmdleten Set-AzVMDiskEncryptionExtension med parametern EncryptFormatAll. Resursgruppen, den virtuella datorn, nyckelvalvet, Azure AD-appen och klienthemligheten bör redan ha skapats som förhandskrav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"></a> Använd parametern EncryptFormatAll med Logical Volume Manager (LVM) 
Vi rekommenderar en LVM-on-crypt-konfiguration. För alla följande exempel ersätter du enhetssökvägen och monteringspunkterna med det som passar ditt användningsfall. Den här konfigurationen kan göras på följande sätt:

- Lägg till de datadiskar som ska skapa den virtuella datorn.
- Formatera, montera och lägg till diskarna i fstab-filen.

    1. Formatera den nyligen tillagda disken. Vi använder symlinks som genereras av Azure här. Med symlinks undviker du problem som rör ändring av enhetsnamn. Mer information finns i Felsöka [problem med enhetsnamn.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. Montera diskarna.

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. Lägg till i fstab.

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. Kör den Set-AzVMDiskEncryptionExtension PowerShell-cmdleten med -EncryptFormatAll för att kryptera diskarna.

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. Konfigurera LVM ovanpå dessa nya diskar. Observera att krypterade enheter låses upp när den virtuella datorn har startats klart. Därför måste LVM-monteringen också fördröjas senare.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"></a> Nya virtuella IaaS-datorer som skapats från kundkrypterad virtuell hårddisk och krypteringsnycklar
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager, PowerShell-cmdlets eller CLI-kommandon. I följande avsnitt beskrivs i detalj Resource Manager och CLI-kommandon. 

Använd anvisningarna i bilagan för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbereda en förkrypterad virtuell Linux-hårddisk](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Det är obligatoriskt att ta en ögonblicksbild eller säkerhetskopiera en hanterad diskbaserad VM-instans utanför och innan du aktiverar Azure Disk Encryption. Du kan ta en ögonblicksbild av den hanterade disken från portalen eller använda [Azure Backup](../../backup/backup-azure-vms-encryption.md). Säkerhetskopieringar säkerställer att ett återställningsalternativ är möjligt om det uppstår oväntade fel under krypteringen. När en säkerhetskopia har gjorts använder du cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot hanterade diskbaserade virtuella datorer tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivering av kryptering kan göra att den virtuella datorn startas om.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"></a> Använd Azure PowerShell för att kryptera virtuella IaaS-datorer med förkrypterade virtuella hårddiskar 
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk med hjälp av PowerShell-cmdleten [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) I följande exempel visas några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan lägga till en ny datadisk med [hjälp av az vm disk attach](add-disk.md) eller via [Azure Portal](attach-disk-portal.md). Innan du kan kryptera måste du montera den nyligen anslutna datadisken först. Du måste begära kryptering av dataenheten eftersom enheten kommer att vara oanvändbar medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
 Om den virtuella datorn tidigare krypterades med "Alla" ska parametern --volume-type förbli Alla. Alla innehåller både OPERATIVSYSTEM- och datadiskar. Om den virtuella datorn tidigare krypterades med volymtypen "OS" bör parametern --volume-type ändras till Alla så att både operativsystemet och den nya datadisken inkluderas. Om den virtuella datorn har krypterats med endast volymtypen "Data" kan den förbli Data som visas här. Det räcker inte att lägga till och koppla en ny datadisk till en virtuell dator för kryptering. Den nyligen anslutna disken måste också vara formaterad och korrekt monterad på den virtuella datorn innan du aktiverar kryptering. I Linux måste disken monteras i /etc/fstab med ett [beständigt blockenhetsnamn](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems). 

Till skillnad från PowerShell-syntaxen kräver INTE CLI att du anger en unik sekvensversion när du aktiverar kryptering. CLI genererar och använder automatiskt sitt eget unika versionsvärde för sekvensen.

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera en virtuell dator som körs med kek för att omsluta klienthemligheten:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder PowerShell för att kryptera en ny disk för Linux måste en ny sekvensversion anges. Sekvensversionen måste vara unik. Följande skript genererar ett GUID för sekvensversionen. 
 

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** Följande skript initierar dina variabler och kör Set-AzVMDiskEncryptionExtension cmdlet. Resursgruppen, den virtuella datorn, nyckelvalvet, Azure AD-appen och klienthemligheten bör redan ha skapats som förutsättningar. Ersätt MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden. Parametern -VolumeType är inställd på datadiskar och inte os-disken. Om den virtuella datorn tidigare krypterades med volymtypen "OS" eller "Alla" bör parametern -VolumeType ändras till Alla så att både operativsystemet och den nya datadisken inkluderas.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Kryptera en virtuell dator som körs med kek för att omsluta klienthemligheten:** Azure Disk Encryption kan du ange en befintlig nyckel i nyckelvalvet för att omsluta diskkrypteringshemligheter som genererades när krypteringen skapades. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption nyckeln för att omsluta krypteringshemligheterna innan de skrivs till nyckelvalvet. Parametern -VolumeType är inställd på datadiskar och inte os-disken. Om den virtuella datorn tidigare krypterades med volymtypen "OS" eller "Alla" bör parametern -VolumeType ändras till Alla så att både operativsystemet och den nya datadisken inkluderas.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller en Resource Manager mall. 

>[!IMPORTANT]
>Inaktivering av kryptering med Azure Disk Encryption på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats. 

- **Inaktivera diskkryptering med Azure PowerShell:** Om du vill inaktivera kryptering använder du cmdleten [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du [kommandot az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager mall:** Om du vill inaktivera kryptering använder du mallen [Inaktivera kryptering på en virtuell Linux-dator som](https://aka.ms/decrypt-linuxvm) körs.
     1. Välj **Distribuera till Azure**.
     2. Välj prenumeration, resursgrupp, plats, virtuell dator, juridiska villkor och avtal.
     3. Välj **Köp för** att inaktivera diskkryptering på en virtuell Windows-dator som körs. 


## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption för Linux – översikt](disk-encryption-overview-aad.md)
- [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md)