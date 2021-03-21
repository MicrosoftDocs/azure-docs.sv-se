---
title: 'Snabb start: Konfigurera hög tillgänglighet med Azures front dörr – Azure CLI'
description: Den här snabb starten visar hur du använder Azures front dörr för att skapa ett globalt och högpresterande globalt webb program med hjälp av Azure CLI.
services: front-door
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 0b82c11dcd615dfbdcfd70f5f90edd7ba41b4562
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201625"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Snabb start: skapa en front dörr för ett globalt webb program med hög tillgänglighet med hjälp av Azure CLI

Kom igång med Azures frontend genom att använda Azure CLI för att skapa en hög tillgänglig global webbapp med hög prestanda.

Front dörren dirigerar webb trafik till vissa resurser i en backend-pool. Du definierade frontend-domänen, lägger till resurser i en backend-pool och skapar en regel för routning. Den här artikeln använder en enkel konfiguration av en backend-pool med två webb program resurser och en regel för routning med standard Sök väg som matchar "/*".

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure CLI installerat lokalt eller Azure Cloud Shell
- Kontrol lera att tillägget för front dörren har lagts till i Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resurs grupp eller skapa en ny.

I den här snabb starten behöver du två resurs grupper. En i *Central USA* och den andra i *södra centrala USA*.

Skapa en resurs grupp med [AZ Group Create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>Skapa två instanser av en webbapp

Två instanser av ett webb program som körs i olika Azure-regioner krävs för den här snabb starten. Båda instanserna av webb programmet körs i aktivt/aktivt läge, så att en kan betjäna trafik.

Om du inte redan har en webbapp använder du följande skript för att konfigurera två exempel på webbappar.

### <a name="create-app-service-plans"></a>Skapa App Service-planer

Innan du kan skapa webbappar behöver du två App Service-planer, en i *Central USA* och den andra i *södra centrala USA*.

Skapa App Service-planer med [AZ AppService plan Create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
--resource-group myRGFDSouthCentral
```

### <a name="create-web-apps"></a>Skapa webb program

Genom att köra följande kommandon skapar du en webbapp i var och en av App Service-planerna i föregående steg. Webb program namn måste vara globalt unika.

Skapa en webbapp med [AZ webapp Create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

Anteckna standard värd namnet för varje webbapp så att du kan definiera server dels adresserna när du distribuerar front dörren i nästa steg.

## <a name="create-the-front-door"></a>Skapa den främre dörren

Skapa en grundläggande front dörr med standardinställningar för belastnings utjämning, hälso avsökning och regler för routning genom att köra:

Skapa en frontend-dörr med [AZ Network frontend-dörr skapa](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--resurs grupp:** Ange en resurs grupp där du vill distribuera den främre dörren.

**--Namn:** Ange ett globalt unikt namn för din Azure-front dörr. 

**--godkända-protokoll:** Godkända värden är **http** och **https**. Om du vill använda båda, är de både åtskilda med ett blank steg.

**--backend-adress:** Definiera både Web Apps-värdnamn här avgränsade med blank steg.

Anteckna värd namnet i avsnittet *frontEndpoints* när distributionen har slutförts.

## <a name="test-the-front-door"></a>Testa front dörren

Öppna en webbläsare och ange värd namnet hämta från-kommandona. Front dörren dirigerar din begäran till en av Server dels resurserna.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Sidan test av front dörr":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort båda resurs grupperna när du inte längre behöver de resurser som du skapade med frontend-dörren. När du tar bort resurs gruppen tar du även bort frontend-dörren och alla relaterade resurser. 

Ta bort resurs gruppen med hjälp av [AZ Group Delete](/cli/azure/group#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:
* Front Door
* Två webb program

Om du vill lära dig hur du lägger till en anpassad domän i din frontend-guide kan du fortsätta till självstudierna för front dörren.

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](front-door-custom-domain.md)
