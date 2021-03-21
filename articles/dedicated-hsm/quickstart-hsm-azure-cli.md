---
title: 'Snabb start: Skapa Azure-dedikerad HSM med Azure CLI'
description: 'Skapa, Visa, lista, uppdatera och ta bort dedikerade Azure-HSM: er med hjälp av Azure CLI.'
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020966"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Snabb start: skapa en Azure-dedikerad HSM med hjälp av Azure CLI

Den här artikeln beskriver hur du skapar och hanterar en dedikerad HSM med hjälp av [AZ Dedicated-HSM](/cli/azure/ext/hardware-security-modules/dedicated-hsm) Azure CLI-tillägget.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Du kan [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte har något.
  
  Om du har mer än en Azure-prenumeration anger du den prenumeration som ska användas för fakturering med kommandot Azure CLI [AZ Account set](/cli/azure/account#az_account_set) .
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Alla krav som är uppfyllda för dedikerad HSM, inklusive registrering, godkännande och ett virtuellt nätverk och virtuell dator som ska användas för etablering. Mer information om dedikerade HSM-krav och förutsättningar finns i [självstudie: Distribuera HSM: er i ett befintligt virtuellt nätverk med hjälp av Azure CLI](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resurs grupp](../azure-resource-manager/management/overview.md) är en logisk behållare för att distribuera och hantera Azure-resurser som en grupp. Om du inte redan har en resurs grupp för dedikerad HSM skapar du en med hjälp av kommandot [AZ Group Create](/cli/azure/group#az_group_create) . I följande exempel skapas en resurs grupp med namnet `myRG` i `westus` Azure-regionen:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Skapa en dedikerad HSM

Om du vill skapa en dedikerad HSM använder du kommandot [AZ dedikerat-HSM Create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) . I följande exempel etableras en dedikerad HSM med namnet `hsm1` i `westus` regionen, `myRG` resurs gruppen och den angivna prenumerationen, det virtuella nätverket och under nätet. De obligatoriska parametrarna är `name` , `location` och `resource group` .

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

Distributionen tar cirka 25 till 30 minuter att slutföra.

## <a name="get-a-dedicated-hsm"></a>Hämta en dedikerad HSM

Om du vill hämta en aktuell dedikerad HSM kör du kommandot [AZ dedikerat-HSM show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) . I följande exempel hämtas `hsm1` dedikerad HSM i `myRG` resurs gruppen.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Uppdatera en dedikerad HSM

Använd det [AZ dedikerade-HSM-kommandot Update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) för att uppdatera en dedikerad HSM. I följande exempel uppdateras `hsm1` dedikerad HSM i `myRG` resurs gruppen och dess Taggar:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Lista med dedikerade HSM: er

Kör kommandot [AZ Dedicated-HSM List](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) för att hämta information om aktuella dedikerade HSM: er. I följande exempel visas en lista över dedikerade HSM: er i `myRG` resurs gruppen:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Ta bort en dedikerad HSM

Om du vill ta bort en dedikerad HSM använder du kommandot [AZ dedikerat-HSM Delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) . I följande exempel tar bort `hsm1` dedikerad HSM från `myRG` resurs gruppen:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Ta bort resursgruppen

Om du inte längre behöver den resurs grupp som du skapade för dedikerad HSM kan du ta bort den genom att köra kommandot [AZ Group Delete](/cli/azure/group#az_group_delete) . Det här kommandot tar bort gruppen och alla resurser i den, inklusive de som inte är relaterade till dedikerade HSM. I följande exempel tar bort `myRG` resurs gruppen och allt innehåll i den:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Dedicated HSM i [Azure Dedicated HSM](overview.md).
