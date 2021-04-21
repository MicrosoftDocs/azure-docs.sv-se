---
title: Azure Disk Encryption med Azure AD för virtuella Windows-datorer (tidigare version)
description: Den här artikeln innehåller anvisningar om hur du Microsoft Azure diskkryptering för virtuella Windows IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 081ead434dbdc3b9c348e3fa35068a638bab6e62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776869"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption med Azure AD för virtuella Windows-datorer (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera diskkryptering för virtuella datorer. Med den nya versionen behöver du inte längre ange Azure AD-autentiseringsuppgifter under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programparametrarna med den nya versionen. Anvisningar om hur du aktiverar diskkryptering för virtuella datorer med den nya versionen finns [i Azure Disk Encryption för virtuella Windows-datorer.](disk-encryption-windows.md) Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntaxen.**


Du kan aktivera många diskkrypteringsscenarier och stegen kan variera beroende på scenariot. Följande avsnitt beskriver scenarierna i detalj för virtuella Windows IaaS-datorer. Innan du kan använda diskkryptering [måste Azure Disk Encryption förhandskrav](disk-encryption-overview-aad.md) slutföras. 


>[!IMPORTANT]
> - Du bör [ta en ögonblicksbild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhetskopia innan diskarna krypteras. Säkerhetskopieringar säkerställer att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan krypteringen sker. När en säkerhetskopia har gjorts kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern -skipVmBackup. Mer information om hur du återställer krypterade virtuella datorer finns i [Back up and restore encrypted Azure VM](../../backup/backup-azure-vms-encryption.md)( Back up and restore encrypted Azure VM ). 
>
> - Kryptering eller inaktivering av kryptering kan göra att en virtuell dator startas om.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Aktivera kryptering på nya virtuella IaaS-datorer som skapats från Marketplace
Du kan aktivera diskkryptering på nya virtuella IaaS Windows-datorer från Marketplace i Azure med en Resource Manager mall. Mallen skapar en ny krypterad virtuell Windows-dator med hjälp av Windows Server 2012-galleriavbildningen.

1. Klicka Resource Manager [distribuera](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)till **Azure i mallen**.

2. Välj prenumeration, resursgrupp, resursgruppsplats, parametrar, juridiska villkor och avtal. Klicka **på Köp** för att distribuera en ny virtuell IaaS-dator där kryptering är aktiverat.

3. När du har distribuerat mallen kontrollerar du krypteringsstatusen för den virtuella datorn med önskad metod:
     - Verifiera med Azure CLI med hjälp av [kommandot az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Verifiera med Azure PowerShell med hjälp av cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Välj den virtuella datorn och klicka sedan **på Diskar** under rubriken **Inställningar för** att verifiera krypteringsstatusen i portalen. I diagrammet under **Kryptering** ser du om det är aktiverat. 
           ![Azure Portal – Diskkryptering aktiverat](../media/disk-encryption/disk-encryption-fig2.png)

I följande tabell visas de Resource Manager mallparametrarna för nya virtuella datorer från Marketplace-scenariot med hjälp av Azure AD-klient-ID:

| Parameter | Beskrivning | 
| --- | --- |
| adminUserName | Administratörsanvändarnamn för den virtuella datorn. |
| adminPassword | Administratörsanvändarlösenord för den virtuella datorn. |
| newStorageAccountName | Namnet på det lagringskonto som ska lagra os- och data-VHD:er. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast standard A-, D- och G-serien. |
| virtualNetworkName | Namnet på det virtuella nätverk som det virtuella nätverkskortet ska tillhöra. |
| subnetName | Namnet på undernätet i det virtuella nätverk som nätverkskortet för den virtuella datorn ska tillhöra. |
| AADClientID | Klient-ID för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| AADClientSecret | Klienthemlighet för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| keyVaultURL | URL för nyckelvalvet som BitLocker-nyckeln ska laddas upp till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -VaultName "MyKeyVault&quot; -ResourceGroupName &quot;MyKeyVaultResourceGroupName").VaultURI` eller Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL för nyckelkrypteringsnyckeln som används för att kryptera den genererade BitLocker-nyckeln (valfritt). </br> </br>KeyEncryptionKeyURL är en valfri parameter. Du kan använda din egen KEK för att ytterligare skydda datakrypteringsnyckeln (lösenfrashemligheten) i nyckelvalvet. |
| keyVaultResourceGroup | Resursgrupp för nyckelvalvet. |
| vmName | Namnet på den virtuella dator som krypteringsåtgärden ska utföras på. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Aktivera kryptering på befintliga eller körande virtuella IaaS Windows-datorer
I det här scenariot kan du aktivera kryptering med hjälp av en mall, PowerShell-cmdlets eller CLI-kommandon. I följande avsnitt förklaras i detalj hur du aktiverar Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Aktivera kryptering på befintliga eller körande virtuella datorer med Azure PowerShell 
Använd [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure. Information om hur du aktiverar kryptering med Azure Disk Encryption med hjälp av PowerShell-cmdlets finns i blogginläggen [Utforska Azure Disk Encryption med Azure PowerShell – del 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) och Utforska Azure Disk Encryption med Azure PowerShell – del [2.](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2)

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** Skriptet nedan initierar dina variabler och kör Set-AzVMDiskEncryptionExtension cmdlet. Resursgruppen, den virtuella datorn, nyckelvalvet, AAD-appen och klienthemligheten bör redan ha skapats som förhandskrav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden.
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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Kryptera en virtuell dator som körs med KEK för att omsluta klienthemligheten:** Azure Disk Encryption kan du ange en befintlig nyckel i nyckelvalvet för att omsluta diskkrypteringshemligheter som genererades när krypteringen skapades. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption nyckeln för att omsluta krypteringshemligheterna innan de skrivs till Key Vault. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell IaaS-dator använder du cmdleten [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Aktivera kryptering på befintliga eller körande virtuella datorer med Azure CLI
Använd kommandot [az vm encryption enable för](/cli/azure/vm/encryption#az_vm_encryption_enable) att aktivera kryptering på en virtuell IaaS-dator som körs i Azure.

- **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Kryptera en virtuell dator som körs med KEK för att omsluta klienthemligheten:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera krypteringsstatusen för en virtuell IaaS-dator använder du [kommandot az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du [kommandot az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Använda Resource Manager mall
Du kan aktivera diskkryptering på befintliga eller köra virtuella IaaS Windows-datorer i Azure med hjälp av Resource Manager för att kryptera en [virtuell Windows-dator som körs.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)


1. I Azure-snabbstartsmallen klickar du **på Distribuera till Azure**.

2. Välj prenumeration, resursgrupp, resursgruppsplats, parametrar, juridiska villkor och avtal. Klicka **på Köp** för att aktivera kryptering på den befintliga eller körande virtuella IaaS-datorn.

I följande tabell visas de Resource Manager för befintliga eller körande virtuella datorer som använder ett Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-programmet som har behörighet att skriva hemligheter till nyckelvalvet. |
| AADClientSecret | Klienthemlighet för Azure AD-programmet som har behörighet att skriva hemligheter till nyckelvalvet. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckeln ska laddas upp till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommandot `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL för nyckelkrypteringsnyckeln som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri **om du väljer nokek** i listrutan UseExistingKek. Om du **väljer kek** i listrutan UseExistingKek måste du ange _värdet keyEncryptionKeyURL._ |
| volumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS,_ _Data_ och _Alla._ |
| sequenceVersion | Sekvensversion av BitLocker-åtgärden. Öka versionsnumret varje gång en diskkrypteringsåtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella dator som krypteringsåtgärden ska utföras på. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nya virtuella IaaS-datorer som skapats från kundkrypterad virtuell hårddisk och krypteringsnycklar
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager, PowerShell-cmdlets eller CLI-kommandon. I följande avsnitt beskrivs i detalj Resource Manager och CLI-kommandon. 

Använd anvisningarna i bilagan för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbereda en förkrypterad virtuell Windows-hårddisk](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> Kryptera virtuella datorer med förkrypterade virtuella hårddiskar med Azure PowerShell
Du kan aktivera diskkryptering på din krypterade virtuella hårddisk med hjälp av PowerShell-cmdleten [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) I exemplet nedan visas några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk till en virtuell Windows-dator](attach-disk-ps.md)med hjälp av PowerShell eller via [Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för virtuella Windows-datorer ska en ny sekvensversion anges. Sekvensversionen måste vara unik. Skriptet nedan genererar ett GUID för sekvensversionen. I vissa fall kan en nyligen tillagd datadisk krypteras automatiskt av Azure Disk Encryption tillägg. Automatisk kryptering sker vanligtvis när den virtuella datorn startas om när den nya disken är online. Detta beror vanligtvis på att "Alla" angavs för volymtypen när diskkryptering tidigare kördes på den virtuella datorn. Om automatisk kryptering sker på en nyligen tillagd datadisk rekommenderar vi att du kör Set-AzVmDiskEncryptionExtension-cmdleten igen med en ny sekvensversion. Om den nya datadisken krypteras automatiskt och du inte vill krypteras, dekrypterar du först alla enheter och krypterar sedan om med en ny sekvensversion som anger operativsystemet som volymtyp. 
 

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:** Skriptet nedan initierar dina variabler och kör Set-AzVMDiskEncryptionExtension cmdlet. Resursgruppen, den virtuella datorn, nyckelvalvet, AAD-appen och klienthemligheten bör redan ha skapats som förutsättningar. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID och My-AAD-client-secret med dina värden. I det här exemplet används "Alla" för parametern -VolumeType, som innehåller både OS- och Datavolymer. Om du bara vill kryptera OS-volymen använder du "OS" för parametern -VolumeType. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Kryptera en virtuell dator som körs med KEK för att omsluta klienthemligheten:** Azure Disk Encryption kan du ange en befintlig nyckel i nyckelvalvet för att omsluta diskkrypteringshemligheter som genererades när krypteringen skapades. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption nyckeln för att omsluta krypteringshemligheterna innan de skrivs till Key Vault. I det här exemplet används "Alla" för parametern -VolumeType, som innehåller både OS- och Datavolymer. Om du bara vill kryptera OS-volymen använder du "OS" för parametern -VolumeType. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
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

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n för KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
  Azure CLI-kommandot tillhandahåller automatiskt en ny sekvensversion när du kör kommandot för att aktivera kryptering. Godkända värden för volym-yype-parametern är Alla, OS och Data. Du kan behöva ändra volymtypparametern till OS eller Data om du bara krypterar en typ av disk för den virtuella datorn. I exemplen används "Alla" för parametern volymtyp. 

-  **Kryptera en virtuell dator som körs med hjälp av en klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Kryptera en virtuell dator som körs med KEK för att omsluta klienthemligheten:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Aktivera kryptering med hjälp av certifikatbaserad autentisering för Azure AD-klienten.
Du kan använda klientcertifikatautentisering med eller utan KEK. Innan du använder PowerShell-skripten bör du redan ha certifikatet överfört till nyckelvalvet och distribuerats till den virtuella datorn. Om du också använder KEK bör kek redan finnas. Mer information finns i avsnittet  [Certifikatbaserad autentisering för Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) i artikeln om förutsättningar.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Aktivera kryptering med hjälp av certifikatbaserad autentisering med Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Aktivera kryptering med certifikatbaserad autentisering och en KEK med Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Inaktivera kryptering
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager mall. 

- **Inaktivera diskkryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du [kommandot az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager mall:** 

    1. Klicka **på Distribuera till Azure från** mallen Inaktivera [diskkryptering vid körning av virtuella Windows-datorer.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Välj prenumeration, resursgrupp, plats, virtuell dator, juridiska villkor och avtal.
    3.  Klicka **på Köp** för att inaktivera diskkryptering på en virtuell Windows-dator som körs. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Disk Encryption översikt](disk-encryption-overview.md)
