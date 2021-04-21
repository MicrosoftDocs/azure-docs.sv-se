---
title: 'Snabbstart: Konfigurera hög tillgänglighet med Azure Front Door – Azure CLI'
description: Den här snabbstarten visar hur du använder Azure Front Door för att skapa en global webbapp med hög tillgänglighet och höga prestanda med Hjälp av Azure CLI.
services: front-door
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/19/2021
ms.author: duau
ms.openlocfilehash: 99204a2d4c3a2455f0916878fb09a348dc79ac7a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778788"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Snabbstart: Skapa en Front Door för en global webbapp med hög tillgänglig användning med Azure CLI

Kom igång med Azure Front Door hjälp av Azure CLI för att skapa ett globalt webbprogram med hög kapacitet och hög prestanda.

Den Front Door dirigerar webbtrafik till specifika resurser i en backend-pool. Du har definierat frontend-domänen, lagt till resurser i en serverpool och skapat en routningsregel. Den här artikeln använder en enkel konfiguration av en serverdelspool med två webbappresurser och en enda routningsregel med standardsökvägen som matchar "/*".

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Diagram över Front Door distributionsmiljö med hjälp av Azure CLI." border="false":::

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure CLI installerat lokalt eller Azure Cloud Shell
- Kontrollera att tillägget front-door har lagts till i Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resursgrupp eller skapa en ny.

För den här snabbstarten behöver du två resursgrupper. En i *USA, centrala* och den andra i *USA, södra centrala.*

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDEast \
    --location eastus
```

## <a name="create-two-instances-of-a-web-app"></a>Skapa två instanser av en webbapp

Två instanser av en webbapp som körs i olika Azure-regioner krävs för den här snabbstarten. Båda webbprograminstanserna körs i aktivt/aktivt läge, så att någon av dem kan använda trafik.

Om du inte redan har en webbapp kan du använda följande skript för att konfigurera två exempelwebbappar.

### <a name="create-app-service-plans"></a>Skapa App Service-planer

Innan du kan skapa webbapparna behöver du två App Service-planer, en i *USA, centrala* och den andra i *USA, östra.*

Skapa App Service-planer [med az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanEastUS \
--resource-group myRGFDEast
```

### <a name="create-web-apps"></a>Skapa webbappar

Om du kör följande kommandon skapas en webbapp i var och en av App Service-planerna i föregående steg. Webbappnamn måste vara globalt unika.

Skapa en webbapp [med az webapp create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso-1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso-2 \
--resource-group myRGFDEast \
--plan myAppServicePlanEastUS
```

Anteckna standardvärdnamnet för varje webbapp så att du kan definiera backend-adresserna när du distribuerar Front Door i nästa steg.

## <a name="create-the-front-door"></a>Skapa Front Door

Skapa en grundläggande Front Door med standardinställningar för belastningsutjämning, hälsoavsökning och routningsregler genom att köra så här:

Skapa Front Door med [az network front-door create:](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true)

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso-1.azurewebsites.net webappcontoso-2.azurewebsites.net 
```

**--resource-group:** Ange en resursgrupp där du vill distribuera Front Door.

**--name:** Ange ett globalt unikt namn för din Azure Front Door. 

**--accepted-protocols:** Godkända värden är **http** och **https**. Om du vill använda båda kan du se båda avgränsade med blanksteg.

**--backend-address:** Definiera båda webbappens värdnamn här avgränsade med ett blanksteg.

När distributionen är klar anteckning du om värdnamnet i *avsnittet frontEndpoints.*

## <a name="test-the-front-door"></a>Testa Front Door

Öppna en webbläsare och ange det värdnamn som du får från kommandona. Den Front Door dirigerar din begäran till en av backend-resurserna.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Front Door testsida":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med Front Door kan du ta bort båda resursgrupperna. När du tar bort resursgruppen tar du även bort Front Door alla relaterade resurser. 

Om du vill ta bort resursgruppen använder [du az group delete](/cli/azure/group#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDEast
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:
* Front Door
* Två webbappar

Om du vill lära dig hur du lägger till en anpassad domän Front Door du fortsätta till Front Door självstudierna.

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](front-door-custom-domain.md)
