---
title: Azure Disk Encryption med Azure AD för virtuella Windows-datorer (tidigare version)
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Windows IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3b7f6f63953ba09e57e4586c698e16b9abb8aa1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555286"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption med Azure AD för virtuella Windows-datorer (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera disk kryptering för virtuella datorer. Med den nya versionen behöver du inte längre ange autentiseringsuppgifter för Azure AD under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programmets parametrar med den nya versionen. Om du vill visa instruktioner för att aktivera disk kryptering för virtuella datorer med den nya versionen, se [Azure Disk Encryption för virtuella Windows-datorer](disk-encryption-windows.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att behållas med AAD-syntaxen.**


Du kan aktivera många disk krypterings scenarier och stegen kan variera beroende på scenariot. I följande avsnitt beskrivs scenarierna i större detalj för virtuella Windows IaaS-datorer. Innan du kan använda disk kryptering måste [Azure Disk Encryption förutsättningar](disk-encryption-overview-aad.md) vara slutförda. 


>[!IMPORTANT]
> - Du bör [ta en ögonblicks bild](snapshot-copy-managed-disk.md) och/eller skapa en säkerhets kopia innan diskarna krypteras. Säkerhets kopieringar säkerställer att ett återställnings alternativ är möjligt om ett oväntat fel uppstår under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhets kopia innan krypteringen utförs. När du har gjort en säkerhets kopia kan du använda [cmdleten Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att kryptera hanterade diskar genom att ange parametern-skipVmBackup. Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i [säkerhetskopiera och återställa krypterade virtuella Azure-datorer](../../backup/backup-azure-vms-encryption.md). 
>
> - Kryptering eller inaktivera kryptering kan leda till att en virtuell dator startas om.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Aktivera kryptering på nya virtuella IaaS-datorer som skapats från Marketplace
Du kan aktivera disk kryptering på nya IaaS virtuella Windows-datorer från Marketplace i Azure med hjälp av en Resource Manager-mall. Mallen skapar en ny krypterad virtuell Windows-dator med hjälp av Windows Server 2012 Gallery-avbildningen.

1. Klicka på **distribuera till Azure** i [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

2. Välj prenumeration, resurs grupp, plats för resurs grupp, parametrar, juridiska villkor och avtal. Klicka på **köp** för att distribuera en ny virtuell IaaS-dator där kryptering är aktiverat.

3. När du har distribuerat mallen kontrollerar du den virtuella datorns krypterings status med önskad metod:
     - Verifiera med Azure CLI med kommandot [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Verifiera med Azure PowerShell med hjälp av cmdleten [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Välj den virtuella datorn och klicka sedan på **diskar** under **inställnings** rubriken för att kontrol lera krypterings status i portalen. I diagrammet under **kryptering** ser du om det är aktiverat. 
           ![Azure Portal disk kryptering aktiverat](../media/disk-encryption/disk-encryption-fig2.png)

I följande tabell visas parametrarna för Resource Manager-mallen för nya virtuella datorer från Marketplace-scenariot med Azure AD-klient-ID:

| Parameter | Beskrivning | 
| --- | --- |
| adminUserName | Administratörens användar namn för den virtuella datorn. |
| adminPassword | Administratörs användarens lösen ord för den virtuella datorn. |
| newStorageAccountName | Namnet på lagrings kontot där operativ system och data-VHD: ar ska lagras. |
| vmSize | Storlek på den virtuella datorn. För närvarande stöds endast standard A-, D-och G-serien. |
| virtualNetworkName | Namnet på det virtuella nätverk som den virtuella datorns nätverkskort ska tillhöra. |
| subnetName | Namnet på under nätet i det virtuella nätverk som den virtuella datorns nätverkskort ska tillhöra. |
| AADClientID | Klient-ID för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckel valv. |
| AADClientSecret | Klient hemlighet för Azure AD-programmet som har behörighet att skriva hemligheter till ditt nyckel valv. |
| keyVaultURL | URL för nyckel valvet som BitLocker-nyckeln ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -VaultName "MyKeyVault&quot; -ResourceGroupName &quot;MyKeyVaultResourceGroupName").VaultURI` eller Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL till den nyckel krypterings nyckel som används för att kryptera den genererade BitLocker-nyckeln (valfritt). </br> </br>KeyEncryptionKeyURL är en valfri parameter. Du kan ta med din egen KEK för att ytterligare skydda data krypterings nyckeln (lösen Frass hemlighet) i ditt nyckel valv. |
| keyVaultResourceGroup | Nyckel valvets resurs grupp. |
| vmName | Namnet på den virtuella dator som krypterings åtgärden ska utföras på. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Aktivera kryptering på befintliga eller aktiva virtuella IaaS-datorer i Windows
I det här scenariot kan du aktivera kryptering med hjälp av en mall, PowerShell-cmdletar eller CLI-kommandon. I följande avsnitt beskrivs mer information om hur du aktiverar Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Aktivera kryptering på befintliga eller aktiva virtuella datorer med Azure PowerShell 
Använd cmdleten [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure. Information om hur du aktiverar kryptering med Azure Disk Encryption med hjälp av PowerShell-cmdlets finns i blogg inläggen [utforska Azure Disk Encryption med Azure PowerShell del 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) och [utforska Azure Disk Encryption med Azure PowerShell-del 2](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2).

-  **Kryptera en virtuell dator som körs med en klient hemlighet:** Skriptet nedan initierar variablerna och kör Set-AzVMDiskEncryptionExtension-cmdleten. Resurs gruppen, den virtuella datorn, nyckel valvet, AAD-appen och klient hemligheten bör redan ha skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-Client-ID och My-AAD-client-Secret med dina värden.
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
- **Kryptera en virtuell dator som körs med KEK för att omsluta klient hemligheten:** Med Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckel valv för att figursätta disk krypterings hemligheter som genererades när kryptering aktiverades. När en nyckel krypterings nyckel anges använder Azure Disk Encryption den nyckeln för att omsluta krypterings hemligheter innan du skriver till Key Vault. 

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
   > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

- **Kontrol lera att diskarna är krypterade:** Använd cmdleten [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) för att kontrol lera krypterings statusen för en virtuell IaaS-dator. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera disk kryptering:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Aktivera kryptering på befintliga eller aktiva virtuella datorer med Azure CLI
Använd kommandot [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) för att aktivera kryptering på en virtuell IaaS-dator som körs i Azure.

- **Kryptera en virtuell dator som körs med en klient hemlighet:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Kryptera en virtuell dator som körs med KEK för att omsluta klient hemligheten:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn] </br> Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

- **Kontrol lera att diskarna är krypterade:** Om du vill kontrol lera krypterings statusen för en virtuell IaaS-dator använder du kommandot [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Använda Resource Manager-mallen
Du kan aktivera disk kryptering på befintliga eller köra IaaS virtuella Windows-datorer i Azure med hjälp av [Resource Manager-mallen för att kryptera en Windows-VM som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. I mallen för Azure snabb start klickar **du på distribuera till Azure**.

2. Välj prenumeration, resurs grupp, plats för resurs grupp, parametrar, juridiska villkor och avtal. Klicka på **köp** för att aktivera kryptering på den befintliga eller aktiva virtuella IaaS-datorn.

I följande tabell visas parametrarna för Resource Manager-mallen för befintliga eller aktiva virtuella datorer som använder ett Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-programmet som har behörighet att skriva hemligheter till nyckel valvet. |
| AADClientSecret | Klient hemlighet för Azure AD-programmet som har behörighet att skriva hemligheter till nyckel valvet. |
| keyVaultName | Namnet på nyckel valvet som BitLocker-nyckeln ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommandot `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL till den nyckel krypterings nyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i list rutan UseExistingKek. Om du väljer **KEK** i list rutan UseExistingKek måste du ange värdet _keyEncryptionKeyURL_ . |
| volumeType | Typ av volym som krypterings åtgärden utförs på. Giltiga värden är _OS_, _data_ och _alla_. |
| sequenceVersion | Sekvens-version av BitLocker-åtgärden. Öka det här versions numret varje gång en disk krypterings åtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella dator som krypterings åtgärden ska utföras på. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nya virtuella IaaS-datorer som skapats från kund-krypterade VHD-och krypterings nycklar
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager-mallen, PowerShell-cmdletar eller CLI-kommandon. I följande avsnitt beskrivs mer information i Resource Manager-mallen och CLI-kommandona. 

Följ anvisningarna i bilagan för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan du använda stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbereda en förkrypterad Windows-VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> Kryptera virtuella datorer med förkrypterade virtuella hård diskar med Azure PowerShell
Du kan aktivera disk kryptering på din krypterade virtuella hård disk med PowerShell-cmdleten [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Exemplet nedan visar några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk till en virtuell Windows-dator med hjälp av PowerShell](attach-disk-ps.md), eller [via Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder PowerShell för att kryptera en ny disk för virtuella Windows-datorer ska en ny version av sekvens anges. Sekvens-versionen måste vara unik. Skriptet nedan genererar en GUID för sekvens-versionen. I vissa fall kan en nyligen tillagd datadisk krypteras automatiskt av Azure Disk Encryption tillägget. Automatisk kryptering sker vanligt vis när den virtuella datorn startas om efter att den nya disken är online. Detta beror vanligt vis på att "alla" angavs för volym typen när disk kryptering tidigare kördes på den virtuella datorn. Om automatisk kryptering sker på en nyligen tillagd datadisk rekommenderar vi att du kör Set-AzVmDiskEncryptionExtension-cmdleten igen med en ny sekvens-version. Om den nya datadisken krypteras automatiskt och du inte vill vara krypterad dekrypterar du alla enheter och krypterar först om med en ny sekvens version som anger OS som typ av volym. 
 

-  **Kryptera en virtuell dator som körs med en klient hemlighet:** Skriptet nedan initierar variablerna och kör Set-AzVMDiskEncryptionExtension-cmdleten. Resurs gruppen, den virtuella datorn, nyckel valvet, AAD-appen och klient hemligheten bör redan ha skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-Client-ID och My-AAD-client-Secret med dina värden. I det här exemplet används "alla" för parametern-VolumeType, som innehåller både OS-och data volymer. Om du bara vill kryptera operativ system volymen använder du "OS" för parametern-VolumeType. 

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
- **Kryptera en virtuell dator som körs med KEK för att omsluta klient hemligheten:** Med Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckel valv för att figursätta disk krypterings hemligheter som genererades när kryptering aktiverades. När en nyckel krypterings nyckel anges använder Azure Disk Encryption den nyckeln för att omsluta krypterings hemligheter innan du skriver till Key Vault. I det här exemplet används "alla" för parametern-VolumeType, som innehåller både OS-och data volymer. Om du bara vill kryptera operativ system volymen använder du "OS" för parametern-VolumeType. 

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
    > Syntaxen för värdet för parametern disk-encryptning-nyckel valv är den fullständiga ID-strängen:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br> Syntaxen för värdet för nyckeln Key-Encryption-Key är den fullständiga URI: n till KEK som i: https://[Key Vault-Name]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
  Azure CLI-kommandot kommer automatiskt att tillhandahålla en ny version av sekvensen när du kör kommandot för att aktivera kryptering. Godkända värden för parametern yype är alla, OS och data. Du kan behöva ändra volym typs parametern till operativ system eller data om du bara krypterar en typ av disk för den virtuella datorn. I exemplen används "alla" för volym typ parametern. 

-  **Kryptera en virtuell dator som körs med en klient hemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Kryptera en virtuell dator som körs med KEK för att omsluta klient hemligheten:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Aktivera kryptering med Azure AD client Certificate-baserad autentisering.
Du kan använda autentisering med klient certifikat med eller utan KEK. Innan du använder PowerShell-skripten bör du redan ha certifikatet överfört till nyckel valvet och distribuerat till den virtuella datorn. Om du använder KEK bör KEK redan finnas. Mer information finns i avsnittet  [certifikatbaserad autentisering för Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) i krav artikeln.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Aktivera kryptering med certifikatbaserad autentisering med Azure PowerShell

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
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager-mall. 

- **Inaktivera disk kryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** 

    1. Klicka på **distribuera till Azure** från den [inaktivera disk kryptering vid körning av Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)  -mall.
    2. Välj prenumeration, resurs grupp, plats, virtuell dator, juridiska villkor och avtal.
    3.  Klicka på **köp** för att inaktivera disk kryptering på en Windows-VM som körs. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
