---
title: ta med fil
description: ta med fil
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 98922829e83f84078c3d8cadae15844dba194c93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800276"
---
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

*Om du redan har en resursgrupp kan du gå vidare till [Skapa ett nyckelvalv.](#create-a-key-vault)*

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

Skapa en resursgrupp med hjälp av Azure CLI-kommandot [az group create,](/cli/azure/group#az_group_create) [kommandot New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell eller [från Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

*Om du redan har ett nyckelvalv kan du gå vidare till [Ange avancerade åtkomstprinciper för nyckelvalvet.](#set-key-vault-advanced-access-policies)*

Skapa ett nyckelvalv med hjälp av Azure CLI-kommandot [az keyvault create,](/cli/azure/keyvault#az_keyvault_create) [](https://portal.azure.com)Azure [Powershell-kommandot New-AzKeyvault,](/powershell/module/az.keyvault/new-azkeyvault) Azure Portal eller [en Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Ditt nyckelvalv och dina virtuella datorer måste finnas i samma prenumeration. För att säkerställa att krypteringshemligheter inte går över regionala gränser kräver Azure Disk Encryption att Key Vault och de virtuella datorerna finns i samma region. Skapa och använd en Key Vault som finns i samma prenumeration och region som de virtuella datorer som ska krypteras. 

Varje Key Vault måste ha ett unikt namn. Ersätt <your-unique-keyvault-name> med namnet på ditt nyckelvalv i följande exempel.

### <a name="azure-cli"></a>Azure CLI

När du skapar ett nyckelvalv med Azure CLI lägger du till flaggan "--enabled-for-disk-encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

När du skapar ett nyckelvalv med Azure PowerShell lägger du till flaggan "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-mall

Du kan också skapa ett nyckelvalv med hjälp av [Resource Manager mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. I Azure-snabbstartsmallen klickar du **på Distribuera till Azure**.
2. Välj prenumeration, resursgrupp, resursgruppsplats, Key Vault objekt-ID, juridiska villkor och avtal och klicka sedan på **Köp.** 


##  <a name="set-key-vault-advanced-access-policies"></a>Ställa in avancerade åtkomstprinciper för nyckelvalvet

Azure-plattformen behöver åtkomst till krypteringsnycklarna eller hemligheterna i nyckelvalvet för att göra dem tillgängliga för den virtuella datorn för start och dekryptering av volymerna. 

Om du inte aktiverar nyckelvalvet för diskkryptering, distribution eller malldistribution vid tidpunkten för skapandet (som visas i föregående steg) måste du uppdatera dess avancerade åtkomstprinciper.  

### <a name="azure-cli"></a>Azure CLI

Använd [az keyvault update för](/cli/azure/keyvault#az_keyvault_update) att aktivera diskkryptering för nyckelvalvet. 

 - **Aktivera Key Vault för diskkryptering:** Aktiverat för diskkryptering krävs. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution om det behövs:** Gör det möjligt för resursprovidern Microsoft.Compute att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras till vid resursskapande, till exempel när du skapar en virtuell dator.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault för malldistribution om det behövs:** Tillåt Resource Manager att hämta hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Använd PowerShell-cmdleten [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för nyckelvalvet för att aktivera diskkryptering för nyckelvalvet.

  - **Aktivera Key Vault för diskkryptering:** EnabledForDiskEncryption krävs för Azure Disk-kryptering.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Aktivera Key Vault för distribution om det behövs:** Gör det möjligt för resursprovidern Microsoft.Compute att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras till vid resursskapande, till exempel när du skapar en virtuell dator.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Aktivera Key Vault för malldistribution om det behövs:** Gör det Azure Resource Manager att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras till i en malldistribution.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Välj ditt nyckelvalv, gå till **Åtkomstprinciper** och Klicka **för att visa avancerade åtkomstprinciper.**
2. Markera rutan Aktivera åtkomst **till Azure Disk Encryption för volymkryptering.**
3. Välj **Aktivera åtkomst till Azure Virtual Machines för distribution** och/eller Aktivera åtkomst till Azure Resource Manager för **malldistribution** om det behövs. 
4. Klicka på **Spara**.

    ![Avancerade åtkomstprinciper för Azure Key Vault](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Konfigurera en nyckelkrypteringsnyckel (KEK)

Om du vill använda en nyckelkrypteringsnyckel (KEK) för ett extra säkerhetslager för krypteringsnycklar lägger du till en KEK i nyckelvalvet. När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption nyckeln för att omsluta krypteringshemligheterna innan de skrivs till Key Vault.

Du kan generera en ny KEK med hjälp av Azure [CLI-kommandot az keyvault key create,](/cli/azure/keyvault/key#az_keyvault_key_create) Azure PowerShell [Add-AzKeyVaultKey-cmdleten](/powershell/module/az.keyvault/add-azkeyvaultkey) [eller Azure Portal](https://portal.azure.com/). Du måste generera en RSA-nyckeltyp. Azure Disk Encryption har ännu inte stöd för att använda Elliptic Curve-nycklar.

Du kan i stället importera en kek från din lokala nyckelhanterings-HSM. Mer information finns i [Key Vault dokumentation.](../articles/key-vault/keys/hsm-protected-keys.md)

Nyckelvalvs-KEK-URL:er måste vara versionshantering. Azure tillämpar den här begränsningen av versionshantering. Giltiga hemligheter och KEK-URL:er finns i följande exempel:

* Exempel på en giltig hemlig URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exempel på en giltig KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption stöder inte att portnummer anges som en del av key vault-hemligheter och KEK-URL:er. Exempel på URL:er för nyckelvalv som inte stöds och som stöds finns i följande exempel:

  * Url till acceptabelt nyckelvalv: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Oacceptabel nyckelvalvs-URL: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Använd kommandot Azure CLI [az keyvault key create för](/cli/azure/keyvault/key#az_keyvault_key_create) att generera en ny KEK och lagra den i ditt nyckelvalv.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Du kan i stället importera en privat nyckel med hjälp av kommandot Azure CLI [az keyvault key import:](/cli/azure/keyvault/key#az_keyvault_key_import)

I båda fallen anger du namnet på din kek till Azure CLI [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) --key-encryption-key parameter. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Använd cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) Azure PowerShell skapa en ny KEK och lagra den i ditt nyckelvalv.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Du kan i stället importera en privat nyckel med hjälp Azure PowerShell [kommandot az keyvault key import.](/cli/azure/keyvault/key#az_keyvault_key_import)

I båda fallen anger du ID:t för ditt KEK-nyckelvalv och URL:en för din KEK till parametrarna Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) -KeyEncryptionKeyVaultId och -KeyEncryptionKeyUrl. Observera att det här exemplet förutsätter att du använder samma nyckelvalv för både diskkrypteringsnyckeln och KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```