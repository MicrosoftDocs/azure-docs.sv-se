---
title: 'Snabb start: skapa en profil för HA med program – Azure CLI – Azure Traffic Manager'
description: Den här snabb starten beskriver hur du skapar en Traffic Manager-profil för att skapa ett webb program med hög tillgänglighet med hjälp av Azure CLI.
services: traffic-manager
author: duongau
mnager: kumud
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07fadd7b3129b3ca3351e0416c8aa6f49de82212
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201237"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Snabb start: skapa en Traffic Manager profil för ett webb program med hög tillgänglighet med hjälp av Azure CLI

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabb starten skapar du två instanser av ett webb program. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager profil med [AZ Network Traffic-Manager profil Create](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) som dirigerar användar trafik baserat på slut punkts prioritet.

I följande exempel ersätter du **<profile_name>** med ett unikt Traffic Manager profil namn.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Skapa webb program

För den här snabbstarten behöver du två instanser av en webbapp som distribuerats i två olika Azure-regioner (*USA, östra* och *Europa, västra*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

### <a name="create-web-app-service-plans"></a>Skapa Service planer för webb program
Skapa webb program tjänst planer med [AZ AppService-plan skapa](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) för de två instanser av webb programmet som du ska distribuera i två olika Azure-regioner.

I följande exempel ersätter du **<appspname_eastus>** och **<appspname_westeurope>** med ett unikt app Services plan namn

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Skapa en webbapp i App Service-planen
Skapa två instanser webb programmet med [AZ webapp skapa](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) i App Service-planer i Azure-regionerna *USA, östra* *och Västeuropa.*

I följande exempel ersätter du **<app1name_eastus>** och **<app2name_westeurope>** med ett unikt namn på appen och ersätter **<appspname_eastus**>och<**appspname_westeurope>med** det namn som användes för att skapa app Services planer i föregående avsnitt.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter
Lägg till de två Web Apps som Traffic Manager slut punkter med [AZ Network Traffic-Manager-slutpunkt skapa](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) till Traffic Manager-profilen enligt följande:

- Fastställ webb program-ID: t och Lägg till webbappen i Azure-regionen *USA, östra* som den primära slut punkten för att dirigera all användar trafik. 
- Fastställ webbappens ID och Lägg till webbappen som finns i Azure-regionen *Västeuropa, västra* som redundansväxling. 

När den primära slutpunkten inte är tillgänglig dirigeras trafiken automatiskt till redundansslutpunkten.

I följande exempel ersätter du **<app1name_eastus>** och **<app2name_westeurope>** med de app-namn som skapats för varje region i föregående avsnitt. Ersätt sedan **<profile_name>** med det profil namn som användes i föregående avsnitt. 

**Östra USA-slutpunkt**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Anteckna det ID som visas i utdata och används i följande kommando för att lägga till slut punkten:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Slut punkt för Västeuropa**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Anteckna det ID som visas i utdata och används i följande kommando för att lägga till slut punkten:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Testa din Traffic Manager-profil

I det här avsnittet kontrollerar domännamnet för Traffic Manager-profilen. Du kan även konfigurera den primära slutpunkten till att inte vara tillgänglig. Slutligen ser du att webbappen fortfarande är tillgänglig. Det beror på att Traffic Manager skickar trafiken till redundansslutpunkten.

I följande exempel ersätter du **<app1name_eastus>** och **<app2name_westeurope>** med de app-namn som skapats för varje region i föregående avsnitt. Ersätt sedan **<profile_name>** med det profil namn som användes i föregående avsnitt.

### <a name="determine-the-dns-name"></a>Bestämma DNS-namnet

Ta reda på DNS-namnet för den Traffic Manager profilen med [AZ Network Traffic-Manager profil show](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Kopiera värdet **RelativeDnsName** . DNS-namnet på din Traffic Manager-profil är *http://<* relativednsname *>. trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken
1. I en webbläsare anger du DNS-namnet för din Traffic Manager profil (*http://<* relativednsname *>. trafficmanager.net*) för att Visa webbappens standard webbplats.

    > [!NOTE]
    > I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten. Den är inställd på **Prioritet 1**.
2. Om du vill visa Traffic Manager redundans i praktiken inaktiverar du den primära platsen med [AZ Network Traffic-Manager Endpoint Update](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Kopiera DNS-namnet för din Traffic Manager profil (*http://<* relativednsname *>. trafficmanager.net*) om du vill visa webbplatsen i en ny webbläsarsession.
4. Kontrollera att webbappen fortfarande är tillgänglig.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort resurs grupper, webb program och alla relaterade resurser med [AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Traffic Manager profil som ger hög tillgänglighet för ditt webb program. Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)