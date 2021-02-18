---
title: Aktivera disk kryptering för Service Fabric hanterade kluster (för hands version) noder
description: Lär dig hur du aktiverar disk kryptering för Azure Service Fabric hanterade klusternoder i Windows med en ARM-mall.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642586"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Aktivera disk kryptering för Service Fabric hanterade kluster (för hands version) noder

I den här guiden får du lära dig hur du aktiverar disk kryptering på Service Fabric hanterade klusternoder i Windows med hjälp av funktionen [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) för [skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) via Azure Resource Manager (arm) mallar.

> [!IMPORTANT]
> Skalnings uppsättningen för den virtuella datorns skalnings uppsättning har ännu inte stöd för avbildnings uppgradering eller avbildning. Använd inte om du behöver uppgradera din operativ system avbildning.

## <a name="register-for-azure-disk-encryption"></a>Registrera dig för Azure Disk Encryption

För hands versionen av disk kryptering för den virtuella datorns skalnings uppsättning krävs själv registrering. Kör följande kommando:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

Kontrol lera status för registreringen genom att köra:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

När statusen har ändrats till *registrerad* är du redo att gå vidare.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Etablera en Key Vault för disk kryptering

Azure Disk Encryption kräver ett Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter. Ditt Key Vault och Service Fabric hanterade klustret måste finnas i samma Azure-region och prenumeration. Så länge dessa krav uppfylls kan du använda antingen en ny eller befintlig Key Vault genom att aktivera den för disk kryptering.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Skapa Key Vault med disk kryptering aktiverat

Kör följande kommandon för att skapa en ny Key Vault för disk kryptering. Kontrol lera att regionen för Key Vault [stöds för Service Fabric hanterade kluster](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) och är i samma region som klustret.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Uppdatera befintliga Key Vault för att aktivera disk kryptering

Kör följande kommandon för att aktivera disk kryptering för en befintlig Key Vault.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Uppdatera mall-och parameter filerna för disk kryptering

Följande steg vägleder dig genom de ändringar som krävs för att aktivera disk kryptering i ett [befintligt hanterat kluster](tutorial-managed-cluster-deploy.md). Alternativt kan du distribuera ett nytt Service Fabric hanterat kluster med disk kryptering aktiverat med den här mallen: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Lägg till följande parametrar i mallen och ersätt din egen prenumeration, resurs grupp namn och valv namn under `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Lägg sedan till det `AzureDiskEncryption` virtuella dator tillägget till de hanterade klusternodernas typer i mallen:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Slutligen uppdaterar du parameter filen och ersätter din egen prenumeration, resurs grupp och nyckel valv namn i *keyVaultResourceId*:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>Distribuera och verifiera ändringarna

När du är klar distribuerar du ändringarna för att aktivera disk kryptering i ditt hanterade kluster.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

Du kan kontrol lera disk krypterings status på en nodtyps underliggande skalnings uppsättning med hjälp av `Get-AzVmssDiskEncryption` kommandot. Först måste du hitta namnet på det hanterade klustrets stödjande resurs grupp (som innehåller det underliggande virtuella nätverket, belastningsutjämnaren, offentlig IP, NSG, skalnings uppsättning (ar) och lagrings konton). Se till att ändra `VmssName` till det namn på klustret som du vill kontrol lera (enligt vad som anges i distributions mal len).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

Utdata bör se ut ungefär så här:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Nästa steg

[Exempel: standard-SKU Service Fabric hanterat kluster, 1 nodtyp med disk kryptering aktiverat](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption för virtuella Windows-datorer](../virtual-machines/windows/disk-encryption-overview.md)

[Kryptera skalnings uppsättningar för virtuella datorer med Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
