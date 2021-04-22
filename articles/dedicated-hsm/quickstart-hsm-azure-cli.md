---
title: 'Snabbstart: Skapa Azure Dedicated HSM med Azure CLI'
description: Skapa, visa, lista, uppdatera och ta bort Dedikerade HSM:er i Azure med hjälp av Azure CLI.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 80d5bbb54715c5a1a5102f8991f366e273145edc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868959"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Snabbstart: Skapa en Azure Dedicated HSM med hjälp av Azure CLI

I den här artikeln beskrivs hur du skapar och hanterar Azure Dedicated HSM med hjälp av [Azure CLI-tillägget az dedicated-hsm.](/cli/azure/dedicated-hsm)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Du [kan skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) om du inte har ett.
  
  Om du har fler än en Azure-prenumeration anger du den prenumeration som ska användas för fakturering med kommandot Azure CLI [az account set.](/cli/azure/account#az_account_set)
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Alla krav är uppfyllda för en dedikerad HSM, inklusive registrering, godkännande och ett virtuellt nätverk och en virtuell dator som ska användas för etablering. Mer information om dedikerade krav och krav för HSM finns i [Självstudie: Distribuera HSM:er](tutorial-deploy-hsm-cli.md)till ett befintligt virtuellt nätverk med hjälp av Azure CLI.
  

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resursgrupp](../azure-resource-manager/management/overview.md) är en logisk container för distribution och hantering av Azure-resurser som en grupp. Om du inte redan har en resursgrupp för den dedikerade HSM:en skapar du en med hjälp av [kommandot az group create.](/cli/azure/group#az_group_create) I följande exempel skapas en resursgrupp med `myRG` namnet i `westus` Azure-regionen:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Skapa en dedikerad HSM

Om du vill skapa en dedikerad HSM använder du [kommandot az dedicated-hsm create.](/cli/azure/dedicated-hsm#az_dedicated_hsm_create) I följande exempel har en dedikerad HSM med namnet i regionen, resursgruppen och den `hsm1` `westus` angivna `myRG` prenumerationen, det virtuella nätverket och undernätet. De obligatoriska parametrarna `name` är `location` , och `resource group` .

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

## <a name="get-a-dedicated-hsm"></a>Skaffa en dedikerad HSM

Om du vill hämta en aktuell dedikerad HSM kör du [kommandot az dedicated-hsm show.](/cli/azure/dedicated-hsm#az_dedicated_hsm_show) I följande exempel hämtar dedikerad `hsm1` HSM i `myRG` resursgruppen.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Uppdatera en dedikerad HSM

Använd kommandot [az dedicated-hsm update](/cli/azure/dedicated-hsm#az_dedicated_hsm_update) för att uppdatera en dedikerad HSM. I följande exempel uppdateras den `hsm1` dedikerade HSM:en `myRG` i resursgruppen och dess taggar:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Lista dedikerade HSM:er

Kör kommandot [az dedicated-hsm list för](/cli/azure/dedicated-hsm#az_dedicated_hsm_list) att hämta information om aktuella dedikerade HSM:er. I följande exempel visas de dedikerade HSM:erna i `myRG` resursgruppen:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Ta bort en dedikerad HSM

Om du vill ta bort en dedikerad HSM använder du [kommandot az dedicated-hsm delete.](/cli/azure/dedicated-hsm#az_dedicated_hsm_delete) I följande exempel tas den `hsm1` dedikerade HSM:en bort från `myRG` resursgruppen:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Ta bort resursgruppen

Om du inte längre behöver resursgruppen som du skapade för dedikerad HSM kan du ta bort den genom att köra [kommandot az group delete.](/cli/azure/group#az_group_delete) Det här kommandot tar bort gruppen och alla resurser i den, inklusive alla som inte är relaterade till dedikerad HSM. I följande exempel tas `myRG` resursgruppen och allt i den bort:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Dedicated HSM finns i [Azure Dedicated HSM](overview.md).
