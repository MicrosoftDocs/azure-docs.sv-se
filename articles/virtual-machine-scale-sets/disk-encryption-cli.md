---
title: Kryptera diskar för Azures skalnings uppsättningar med Azure CLI
description: Lär dig hur du använder Azure CLI för att kryptera VM-instanser och anslutna diskar i en skalnings uppsättning för virtuella Windows-datorer
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: e6630cbb44157f25bd2cbfcff25ec3132c74c61c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565579"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Kryptera OS och anslutna data diskar i en skalnings uppsättning för virtuella datorer med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabb starten visar hur du använder Azure CLI för att skapa och kryptera en skalnings uppsättning för virtuella datorer. Mer information om hur du använder Azure Disk Encryption till en skalnings uppsättning för virtuella datorer finns i [Azure Disk Encryption för Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.31 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Innan du kan skapa en skalningsuppsättning skapar du en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som är inställd på att uppdateras automatiskt när ändringar tillämpas och genererar SSH-nycklar om de inte finns i *~/.ssh/id_rsa*. En data disk på 32 GB är kopplad till varje virtuell dator instans och [tillägget Azures anpassade skript](../virtual-machines/extensions/custom-script-linux.md) används för att förbereda data diskarna med [AZ VMSS-tilläggs uppsättning](/cli/azure/vmss/extension):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure Key Vault aktiverat för disk kryptering

Azure Key Vault kan lagra nycklar, hemligheter eller lösen ord som gör det möjligt att på ett säkert sätt implementera dem i dina program och tjänster. Krypterings nycklar lagras i Azure Key Vault med hjälp av program varu skydd, eller så kan du importera eller generera nycklar i HSM: er (Hardware Security modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning.

Definiera egna unika *keyvault_name*. Skapa sedan ett nyckel valv med [AZ-valv skapa](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-create) i samma prenumeration och region som skalnings uppsättningen, och Ställ in- *-Enabled-för-Disk-Encryption* -åtkomst principen.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Använd en befintlig Key Vault

Det här steget krävs endast om du har ett befintligt Key Vault som du vill använda med disk kryptering. Hoppa över det här steget om du har skapat ett Key Vault i föregående avsnitt.

Definiera egna unika *keyvault_name*. Uppdatera sedan ditt nyckel valv med AZ-uppdatering av nyckel [valvet](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-update) och ange *--aktive rad-för-Disk-Encryption* -åtkomst princip.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Aktivera kryptering

Om du vill kryptera VM-instanser i en skalnings uppsättning får du först lite information om Key Vault resurs-ID med [AZ-valv show](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-show). Dessa variabler används för att starta krypterings processen med [AZ VMSS-kryptering aktivera](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

Det kan ta en minut eller två innan krypterings processen startas.

Eftersom skalnings uppsättningen är uppgraderings princip på den skalnings uppsättning som skapades i ett tidigare steg är inställd på *Automatisk*, startar automatiskt krypterings processen i VM-instanserna. Starta krypterings principen på de virtuella dator instanserna med [AZ VMSS Update-instances](/cli/azure/vmss#az-vmss-update-instances)i skalnings uppsättningar där uppgraderings principen är manuell.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivera kryptering med KEK för att omsluta nyckeln

Du kan också använda en nyckel krypterings nyckel för ökad säkerhet när du krypterar skalnings uppsättningen för den virtuella datorn.

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
>  Syntaxen för värdet för parametern Disk-Encryption-nyckel valv är den fullständiga ID-strängen:</br>
/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br></br>
> Syntaxen för värdet för nyckeln Key-Encryption-Key är fullständig URI till KEK som i:</br>
https://[nyckel valv-namn]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID]

## <a name="check-encryption-progress"></a>Kontrol lera krypterings förlopp

Om du vill kontrol lera statusen för disk kryptering använder du [AZ VMSS Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

När de virtuella dator instanserna krypteras rapporterar status koden *EncryptionState/krypterad*, som visas i följande exempel på utdata:

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

Om du inte längre vill använda diskar med krypterade virtuella dator instanser kan du inaktivera kryptering med [AZ VMSS-kryptering inaktivera](/cli/azure/vmss/encryption#az-vmss-encryption-disable) enligt följande:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Nästa steg

- I den här artikeln använde du Azure CLI för att kryptera en skalnings uppsättning för virtuella datorer. Du kan också använda [Azure PowerShell](disk-encryption-powershell.md) -eller [Azure Resource Manager-mallar](disk-encryption-azure-resource-manager.md).
- Om du vill att Azure Disk Encryption ska användas efter att ett annat tillägg har tillhandahållits, kan du använda [fil namns sekvenseringen](virtual-machine-scale-sets-extension-sequencing.md). 
- Ett exempel på en kommando fil från slut punkt till slut punkt för data disk kryptering för Linux-skalnings uppsättning hittar du [här](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Det här exemplet skapar en resurs grupp, en Linux-skalnings uppsättning, monterar en data disk på 5 GB och krypterar den virtuella datorns skal uppsättning.
