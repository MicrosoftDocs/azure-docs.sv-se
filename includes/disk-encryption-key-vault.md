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
ms.openlocfilehash: 26a9e931c42822218e7935f50c1f222ac33c34f2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210111"
---
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

*Om du redan har en resurs grupp kan du gå vidare till [skapa ett nyckel valv](#create-a-key-vault).*

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

Skapa en resurs grupp med hjälp av [AZ Group Create](/cli/azure/group#az-group-create) Azure CLI Command, kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell eller från [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

*Om du redan har ett nyckel valv kan du gå vidare till [Ange avancerade åtkomst principer för Key Vault](#set-key-vault-advanced-access-policies).*

Skapa ett nyckel valv med hjälp [av AZ](/cli/azure/keyvault#az-keyvault-create) -kommandot för att skapa Azure CLI, kommandot [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure PowerShell, [Azure Portal](https://portal.azure.com)eller en [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Ditt nyckel valv och virtuella datorer måste finnas i samma prenumeration. För att säkerställa att krypterings hemligheter inte korsar regionala gränser, kräver Azure Disk Encryption att Key Vault och de virtuella datorerna samplaceras i samma region. Skapa och Använd en Key Vault som finns i samma prenumeration och region som de virtuella datorer som ska krypteras. 

Varje Key Vault måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

### <a name="azure-cli"></a>Azure CLI

När du skapar ett nyckel valv med Azure CLI lägger du till flaggan "--enabled for Disk Encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

När du skapar ett nyckel valv med Azure PowerShell lägger du till flaggan "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-mall

Du kan också skapa ett nyckel valv med hjälp av [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. I mallen för Azure snabb start klickar **du på distribuera till Azure**.
2. Välj prenumeration, resurs grupp, resurs grupp plats, Key Vault namn, objekt-ID, juridiska villkor och avtal och klicka sedan på **köp**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Ställa in avancerade åtkomstprinciper för nyckelvalvet

Azure-plattformen behöver åtkomst till krypterings nycklarna eller hemligheterna i ditt nyckel valv för att göra dem tillgängliga för den virtuella datorn för att starta och dekryptera volymerna. 

Om du inte har aktiverat nyckel valvet för disk kryptering, distribution eller mall distribution vid tidpunkten för skapandet (som visas i föregående steg) måste du uppdatera dess avancerade åtkomst principer.  

### <a name="azure-cli"></a>Azure CLI

Använd [AZ-uppdateringen](/cli/azure/keyvault#az-keyvault-update) för att aktivera disk kryptering för nyckel valvet. 

 - **Aktivera Key Vault för disk kryptering:** Enabled-for-Disk-Encryption krävs. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution vid behov:** Gör att Microsoft. Compute Resource-providern kan hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till när en resurs skapas, till exempel när en virtuell dator skapas.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault vid distribution av mallar, om det behövs:** Tillåt Resource Manager att hämta hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Använd Key Vault PowerShell-cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att aktivera disk kryptering för nyckel valvet.

  - **Aktivera Key Vault för disk kryptering:** EnabledForDiskEncryption krävs för Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Aktivera Key Vault för distribution vid behov:** Gör att Microsoft. Compute Resource-providern kan hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till när en resurs skapas, till exempel när en virtuell dator skapas.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Aktivera Key Vault vid distribution av mallar, om det behövs:** Gör det möjligt för Azure Resource Manager att hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till i en mall distribution.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Välj ditt nyckel valv, gå till **åtkomst principer** och **Klicka om du vill visa avancerade åtkomst principer**.
2. Markera rutan **ge till gång till Azure Disk Encryption för volym kryptering**.
3. Välj **Aktivera åtkomst till Azure Virtual Machines för distribution** och/eller **aktivera åtkomst till Azure Resource Manager för mall distribution**, om det behövs. 
4. Klicka på **Spara**.

    ![Avancerade åtkomst principer för Azure Key Vault](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Konfigurera en nyckel krypterings nyckel (KEK)

Om du vill använda en nyckel krypterings nyckel (KEK) för ett extra säkerhets lager för krypterings nycklar lägger du till en KEK i nyckel valvet. När en nyckel krypterings nyckel anges använder Azure Disk Encryption den nyckeln för att omsluta krypterings hemligheter innan du skriver till Key Vault.

Du kan generera en ny KEK med hjälp av kommandot för att skapa en Azure CLI- [AZ](/cli/azure/keyvault/key#az-keyvault-key-create) , Azure PowerShell [Add-AzKeyVaultKey-](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet eller [Azure Portal](https://portal.azure.com/). Du måste generera en RSA-nyckel typ. Azure Disk Encryption har ännu inte stöd för att använda Elliptic kurv nycklar.

Du kan istället importera en KEK från din lokala nyckel hanterings HSM. Mer information finns i [Key Vault-dokumentationen](../articles/key-vault/keys/hsm-protected-keys.md).

KEK-URL: er för nyckel valvet måste ha versions hantering. Azure tillämpar den här begränsningen av versions hantering. Giltiga hemligheter och KEK-URL: er finns i följande exempel:

* Exempel på en giltig hemlig URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exempel på en giltig KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption har inte stöd för att ange port nummer som en del av Key Vault-hemligheter och KEK-URL: er. Exempel på URL: er för nyckel valv som inte stöds och som stöds finns i följande exempel:

  * Acceptabel URL för nyckel valv: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Oacceptabel Key Vault-URL: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Använd Azure CLI- [AZ](/cli/azure/keyvault/key#az-keyvault-key-create) för att skapa en ny KEK och lagra den i ditt nyckel valv.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Du kan istället importera en privat nyckel med hjälp av Azure CLI- [AZ för nyckel import](/cli/azure/keyvault/key#az-keyvault-key-import) :

I båda fallen anger du namnet på din KEK till Azure CLI- [AZ VM-kryptering Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) --Key-Encryption-Key-parameter. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Använd cmdleten Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) för att generera en ny KEK och lagra den i ditt nyckel valv.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Du kan istället importera en privat nyckel med hjälp av kommandot Azure PowerShell [AZ Key Vault Key import](/cli/azure/keyvault/key#az-keyvault-key-import) .

I båda fallen ska du ange ID: t för ditt KEK Key Vault och URL: en för din KEK till parametrarna Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId och-KeyEncryptionKeyUrl. Observera att det här exemplet förutsätter att du använder samma nyckel valv för både disk krypterings nyckeln och KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```