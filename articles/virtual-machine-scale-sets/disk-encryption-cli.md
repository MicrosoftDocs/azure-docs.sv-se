---
title: Kryptera diskar för Azure Scale Sets med Azure CLI
description: Lär dig hur du använder Azure CLI för att kryptera VM-instanser och anslutna diskar i en VM-skalningsuppsättning i Windows
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: af870b3be9c2ab7022a05c9cf9e3a662c5850214
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875079"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Kryptera operativsystem och anslutna datadiskar i en VM-skalningsuppsättning med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure CLI för att skapa och kryptera en VM-skalningsuppsättning. Mer information om hur du tillämpar Azure Disk Encryption på en VM-skalningsuppsättning finns [i Azure Disk Encryption för Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.31 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Innan du kan skapa en skalningsuppsättning skapar du en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som är inställd på att uppdateras automatiskt när ändringar tillämpas och genererar SSH-nycklar om de inte finns i *~/.ssh/id_rsa*. En datadisk på 32 GB är ansluten till varje vm-instans och [tillägget](../virtual-machines/extensions/custom-script-linux.md) för anpassat skript i Azure används för att förbereda datadiskarna med [az vmss extension set](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure-nyckelvalv som är aktiverat för diskkryptering

Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör att du på ett säkert sätt kan implementera dem i dina program och tjänster. Kryptografiska nycklar lagras i Azure Key Vault med hjälp av programvaruskydd, eller så kan du importera eller generera dina nycklar i maskinvarusäkerhetsmoduler (HSM)-certifierade enligt FIPS 140-2 level 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning.

Definiera din egen unika *keyvault_name*. Skapa sedan ett KeyVault med [az keyvault create](/cli/azure/keyvault#az_keyvault_create) i samma prenumeration och region som skalningsuppsättningen och ange åtkomstprincipen *--enabled-for-disk-encryption.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Använda en befintlig Key Vault

Det här steget krävs bara om du har en befintlig Key Vault som du vill använda med diskkryptering. Hoppa över det här steget om du Key Vault i föregående avsnitt.

Definiera din egen unika *keyvault_name*. Sedan uppdaterade du ditt KeyVault med [az keyvault update](/cli/azure/keyvault#az_keyvault_update) och konfigurerade åtkomstprincipen *--enabled-for-disk-encryption.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Aktivera kryptering

Om du vill kryptera VM-instanser i en skalningsuppsättning hämtar du först information om Key Vault resurs-ID:t [med az keyvault show](/cli/azure/keyvault#az_keyvault_show). Dessa variabler används för att sedan starta krypteringsprocessen med [az vmss encryption enable](/cli/azure/vmss/encryption#az_vmss_encryption_enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Det kan ta ett par minuter innan krypteringsprocessen startar.

Eftersom skalningsuppsättningen är uppgraderingsprincip på skalningsuppsättningen som skapades i ett tidigare steg är inställd på automatisk *,* startar VM-instanserna automatiskt krypteringsprocessen. På skalningsuppsättningar där uppgraderingsprincipen ska manuella startar du krypteringsprincipen på de virtuella datorinstanserna med [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivera kryptering med KEK för att omsluta nyckeln

Du kan också använda en nyckelkrypteringsnyckel för ökad säkerhet när du krypterar VM-skalningsuppsättningen.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:en för KEK som i:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Kontrollera krypteringsförloppet

Om du vill kontrollera status för diskkryptering använder [du az vmss encryption show](/cli/azure/vmss/encryption#az_vmss_encryption_show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

När VM-instanser krypteras rapporterar statuskoden *EncryptionState/encrypted*, som du ser i följande exempelutdata:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Inaktivera kryptering

Om du inte längre vill använda krypterade VM-instansers diskar kan du inaktivera kryptering med [az vmss encryption disable på](/cli/azure/vmss/encryption#az_vmss_encryption_disable) följande sätt:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Nästa steg

- I den här artikeln använde du Azure CLI för att kryptera en VM-skalningsuppsättning. Du kan också använda [Azure PowerShell](disk-encryption-powershell.md) eller [Azure Resource Manager mallar](disk-encryption-azure-resource-manager.md).
- Om du vill Azure Disk Encryption när ett annat tillägg har etablerats kan du använda [ordningsföljden för tillägget](virtual-machine-scale-sets-extension-sequencing.md). 
- Ett exempel på en batchfil från slutpunkt till slutpunkt för datadiskkryptering för Linux-skalningsuppsättning finns [här.](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat) Det här exemplet skapar en resursgrupp, En Linux-skalningsuppsättning, monterar en 5 GB datadisk och krypterar VM-skalningsuppsättningen.
