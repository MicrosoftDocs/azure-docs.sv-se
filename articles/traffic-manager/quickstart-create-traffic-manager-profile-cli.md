---
title: 'Snabbstart: Skapa en profil för hög åtkomst av program – Azure CLI – Azure Traffic Manager'
description: Den här snabbstartsartikeln beskriver hur du skapar en Traffic Manager-profil för att skapa en webbapp med hög åtkomst med hjälp av Azure CLI.
services: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 18c77a2b4cbf61979a2ba085640a03c209e890e7
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727932"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Snabbstart: Skapa en Traffic Manager för en webbapp med hög tillgänglig användning med Azure CLI

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabbstarten skapar du två instanser av en webbapp. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Diagram över Traffic Manager distributionsmiljö med CLI." border="false":::

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager med [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-create) som dirigerar användartrafik baserat på slutpunktsprioritet.

I följande exempel ersätter du **<profile_name>** med ett unikt Traffic Manager profilnamn.

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

## <a name="create-web-apps"></a>Skapa webbappar

För den här snabbstarten behöver du två instanser av en webbapp som distribuerats i två olika Azure-regioner (*USA, östra* och *Europa, västra*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

### <a name="create-web-app-service-plans"></a>Skapa webbapptjänstplaner
Skapa web app service-planer [med az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) för de två instanserna av webbappen som du distribuerar i två olika Azure-regioner.

I följande exempel ersätter du **<appspname_eastus>** **och<appspname_westeurope>** med ett unikt App Service plannamn

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
Skapa två instanser av webbappen med [az webapp create](/cli/azure/webapp#az-webapp-create) i App Service i Azure-regionerna *USA,* östra *och Europa,* västra.

I följande exempel ersätter **du<app1name_eastus>** **och<app2name_westeurope>** med ett unikt appnamn och **ersätter<appspname_eastus>** och **<appspname_westeurope>** med det namn som användes för att skapa App Service-planerna i föregående avsnitt.

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
Lägg till de Web Apps som Traffic Manager slutpunkter med [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create) till Traffic Manager profilen enligt följande:

- Fastställ webbapps-ID:t och  lägg till webbappen i Azure-regionen USA, östra som primär slutpunkt för att dirigera all användartrafik. 
- Fastställ webbappens ID och lägg till webbappen i *Azure-regionen Europa,* västra som redundansslutpunkt. 

När den primära slutpunkten inte är tillgänglig dirigeras trafiken automatiskt till redundansslutpunkten.

I följande exempel ersätter du **<app1name_eastus>** **och<app2name_westeurope>** appnamnen som skapades för varje region i föregående avsnitt. Ersätt sedan **<profile_name>** med profilnamnet som användes i föregående avsnitt. 

**Slutpunkt för USA, östra**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Anteckna ID:t som visas i utdata och använd i följande kommando för att lägga till slutpunkten:

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

**Slutpunkt för Europa, västra**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Anteckna ID:t som visas i utdata och använd i följande kommando för att lägga till slutpunkten:

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

## <a name="test-your-traffic-manager-profile"></a>Testa din Traffic Manager profil

I det här avsnittet kontrollerar domännamnet för Traffic Manager-profilen. Du kan även konfigurera den primära slutpunkten till att inte vara tillgänglig. Slutligen ser du att webbappen fortfarande är tillgänglig. Det beror på att Traffic Manager skickar trafiken till redundansslutpunkten.

I följande exempel ersätter du **<app1name_eastus>** **och<app2name_westeurope>** appnamnen som skapades för varje region i föregående avsnitt. Ersätt sedan **<profile_name>** med profilnamnet som användes i föregående avsnitt.

### <a name="determine-the-dns-name"></a>Bestämma DNS-namnet

Fastställ DNS-namnet för Traffic Manager med [az network traffic-manager profile show](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Kopiera värdet **RelativeDnsName.** DNS-namnet för din Traffic Manager profil är *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken
1. I en webbläsare anger du DNS-namnet på din Traffic Manager-profil (*http://<* relativednsname *>.trafficmanager.net*) för att visa webbappens standardwebbplats.

    > [!NOTE]
    > I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten. Den är inställd på **Prioritet 1**.
2. Om du Traffic Manager redundans i praktiken inaktiverar du din primära plats med [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Kopiera DNS-namnet för din Traffic Manager profil (*http://<* relativednsname *>.trafficmanager.net*) för att visa webbplatsen i en ny webbläsarsession.
4. Kontrollera att webbappen fortfarande är tillgänglig.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort resursgrupperna, webbprogrammen och alla relaterade resurser med hjälp av [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat Traffic Manager profil som ger hög tillgänglighet för webbappen. Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)