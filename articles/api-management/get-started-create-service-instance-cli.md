---
title: Snabbstart – Skapa en Azure API Management instans med CLI
description: Skapa en ny Azure API Management-tjänstinstans med hjälp av Azure CLI.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 19fc2e1629e7f67063e3cc3eec8cb3707b6dd2e4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775862"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Snabbstart: Skapa en ny Azure API Management-tjänstinstans med hjälp av Azure CLI

Azure API Management (APIM) hjälper organisationer att publicera API:er till externa partner och interna utvecklare så att de kan få ut maximalt av sina data och tjänster. API Management lägger grunden till ett effektivt API-program genom engagerade utvecklare, affärsinsikter, analyser, hög säkerhet och skydd. Med APIM kan du skapa och hantera moderna API-gatewayer för befintliga backend-tjänster som finns var som helst. Mer information finns i [Översikt](api-management-key-concepts.md).

Den här snabbstarten beskriver stegen för att skapa en API Management instans med [az apim-kommandon](/cli/azure/apim) i Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.11.1 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure API Management instanser, precis som alla Azure-resurser, måste distribueras till en resursgrupp. Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.

Skapa först en resursgrupp med namnet *myResourceGroup på* platsen USA, centrala med följande [az group create-kommando:](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Skapa en ny tjänst

Nu när du har en resursgrupp kan du skapa en API Management-tjänstinstans. Skapa ett med hjälp av [kommandot az apim create](/cli/azure/apim#az_apim_create) och ange ett tjänstnamn och information om utgivaren. Tjänstnamnet måste vara unikt i Azure. 

I följande exempel används *myapim* som tjänstnamn. Uppdatera namnet till ett unikt värde. Uppdatera även namnet på API-utgivarens organisation och e-postadressen för att ta emot meddelanden. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Som standard skapar kommandot instansen på developer-nivån, ett ekonomiskt alternativ för att utvärdera Azure API Management. Den här nivån är inte till för produktionsanvändning. Mer information om att skala API Management-nivåerna finns i avsnittet [Uppgradera och skala](upgrade-and-scale.md). 

> [!TIP]
> Det kan ta mellan 30 och 40 minuter att skapa och aktivera en API Management på den här nivån. Föregående kommando använder alternativet `--no-wait` så att kommandot returneras omedelbart när tjänsten skapas.

Kontrollera statusen för distributionen genom att köra [kommandot az apim show:](/cli/azure/apim#az_apim_show)

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Inledningsvis liknar utdata följande och visar `Activating` statusen:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Efter aktiveringen är statusen `Online` och tjänstinstansen har en gateway-adress och en offentlig IP-adress. För tillfället exponerar inte de här adresserna något innehåll. Exempel:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

När din API Management-tjänstinstans är online är du redo att använda den. Börja med självstudien för [att importera och publicera ditt första API.](import-and-publish.md)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda [kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen och API Management-tjänstinstansen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera och publicera ditt första API](import-and-publish.md)
