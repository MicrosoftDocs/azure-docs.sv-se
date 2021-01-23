---
title: Skapa och kryptera en virtuell Linux-dator med Azure CLI
description: I den här snabb starten får du lära dig hur du använder Azure CLI för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: addfa90f5ec793600072aaaaf2786cfe3d5dad38
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737023"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Snabb start: skapa och kryptera en virtuell Linux-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabb starten visar hur du använder Azure CLI för att skapa och kryptera en virtuell Linux-dator (VM).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du väljer att installera och använda Azure CLI lokalt kräver den här snabb starten att du kör Azure CLI-version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa en Key Vault konfigurerad för krypterings nycklar

Azure Disk Encryption lagrar sin krypterings nyckel i en Azure Key Vault. Skapa en Key Vault med [AZ-valv skapa](/cli/azure/keyvault#az_keyvault_create). Om du vill aktivera Key Vault att lagra krypterings nycklar använder du parametern--enabled-for-Disk-Encryption.

> [!Important]
> Varje nyckel valv måste ha ett namn som är unikt i Azure. I exemplen nedan ersätter du <ditt-Unique-nyckel-valv> med det namn som du väljer.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera din virtuella dator med [AZ VM-kryptering](/cli/azure/vm/encryption)och ange ditt unika Key Vault namn till parametern--Disk-Encryption-nyckel.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Efter en stund kommer processen att returnera "krypterings förfrågan godkändes. Använd kommandot show för att övervaka förloppet. ". Kommandot "show" är [AZ VM show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

När kryptering är aktiverat visas följande i utdata som returneras:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, den virtuella datorn och Key Vault. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en virtuell dator, skapade en Key Vault som aktiverades för krypterings nycklar och krypterade den virtuella datorn.  Fortsätt till nästa artikel om du vill lära dig mer om fler Azure Disk Encryption för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
