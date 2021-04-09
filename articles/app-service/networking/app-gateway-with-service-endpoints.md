---
title: Application Gateway integrering med tjänst slut punkter – Azure App Service | Microsoft Docs
description: Beskriver hur Application Gateway integreras med Azure App Service som skyddas med tjänst slut punkter.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 58886a8f7dc505a7e68d69eb00b4a2ebd776dd5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209871"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Application Gateway integrering med tjänst slut punkter
Det finns tre varianter av App Service som kräver något annorlunda konfiguration av integreringen med Azure Application Gateway. Varianterna omfattar vanliga App Service, även kallade flera innehavare, interna Load Balancer (ILB) App Service-miljön (ASE) och externa ASE. Den här artikeln går igenom hur du konfigurerar den med App Service (flera innehavare) och diskuterar överväganden om ILB och externa ASE.

## <a name="integration-with-app-service-multi-tenant"></a>Integrering med App Service (flera innehavare)
App Service (flera innehavare) har en offentlig slut punkt mot Internet. Med [tjänst slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md) kan du bara tillåta trafik från ett särskilt undernät i ett Azure-Virtual Network och blockera allt annat. I följande scenario kommer vi att använda den här funktionen för att säkerställa att en App Service instans bara kan ta emot trafik från en speciell Application Gateway instans.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Diagram visar det Internet som flödar till en Application Gateway i en Azure-Virtual Network och flödar därifrån via en brand Väggs ikon till instanser av appar i App Service.":::

Det finns två delar i den här konfigurationen, förutom att skapa App Service och Application Gateway. Den första delen är att aktivera tjänstens slut punkter i under nätet för den Virtual Network där Application Gateway distribueras. Tjänst slut punkter ser till att all nätverks trafik som lämnar under nätet till App Service taggas med det angivna under nätets ID. Den andra delen är att ange en åtkomst begränsning för en speciell webbapp för att säkerställa att endast trafik som är Taggad med detta speciella Undernäts-ID tillåts. Du kan konfigurera den med olika verktyg beroende på preferens.

## <a name="using-azure-portal"></a>Använda Azure Portal
Med Azure Portal följer du fyra steg för att etablera och konfigurera installationen. Om du har befintliga resurser kan du hoppa över de första stegen.
1. Skapa en App Service med en av snabb starterna i App Service-dokumentationen, till exempel [.net Core snabb start](../quickstart-dotnetcore.md)
2. Skapa en Application Gateway med hjälp av [portalens snabb start](../../application-gateway/quick-create-portal.md), men hoppa över avsnittet Lägg till Server dels mål.
3. Konfigurera [app service som Server del i Application Gateway](../../application-gateway/configure-web-app-portal.md), men hoppa över avsnittet begränsa åtkomst.
4. Skapa slutligen [åtkomst begränsningen med hjälp av tjänst slut punkter](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

Du kan nu komma åt App Service via Application Gateway, men om du försöker komma åt App Service direkt, bör du få ett 403 HTTP-fel som anger att webbplatsen har stoppats.

![Skärm bild som visar texten i ett fel 403 – ej tillåtet.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
[Distributions mal len för Resource Manager][template-app-gateway-app-service-complete] tillhandahåller ett fullständigt scenario. Scenariot består av en App Service-instans låst med tjänstens slut punkter och åtkomst begränsning för att endast ta emot trafik från Application Gateway. Mallen innehåller många smarta standardvärden och unika postfixs som läggs till i resurs namnen för att det ska vara enkelt. Om du vill åsidosätta dem måste du klona lagrings platsen eller hämta mallen och redigera den. 

Om du vill använda mallen kan du använda knappen distribuera till Azure som finns i beskrivningen av mallen, eller så kan du använda lämplig PowerShell/CLI.

## <a name="using-azure-command-line-interface"></a>Använda Azures kommando rads gränssnitt
[Azure CLI-exemplet](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) etablerar en app service låst med tjänstens slut punkter och åtkomst begränsning för att endast ta emot trafik från Application Gateway. Om du bara behöver isolera trafik till en befintlig App Service från en befintlig Application Gateway, räcker följande kommando.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

I standard konfigurationen säkerställer kommandot båda installationen av tjänst slut punkts konfigurationen i under nätet och åtkomst begränsningen i App Service.

## <a name="considerations-for-ilb-ase"></a>Att tänka på för ILB ASE
ILB ASE är inte exponerad för Internet och trafik mellan instansen och en Application Gateway är därför redan isolerad till Virtual Network. Följande [instruktions guide](../environment/integrate-with-application-gateway.md) konfigurerar en ILB-ASE och integrerar den med en Application Gateway med hjälp av Azure Portal. 

Om du vill säkerställa att endast trafik från Application Gateway under nätet når ASE kan du konfigurera en nätverks säkerhets grupp (NSG) som påverkar alla webbappar i ASE. För NSG kan du ange IP-adressintervall för undernät och eventuellt portarna (80/443). Se till att du inte åsidosätter de [nödvändiga NSG-reglerna](../environment/network-info.md#network-security-groups) för att ASE ska fungera korrekt.

För att isolera trafik till en enskild webbapp måste du använda IP-baserade åtkomst begränsningar eftersom tjänst slut punkter inte fungerar för ASE. IP-adressen måste vara den privata IP-adressen för Application Gateway-instansen.

## <a name="considerations-for-external-ase"></a>Överväganden för externa ASE
Externa ASE har en offentlig belastningsutjämnare som App Service med flera innehavare. Tjänstens slut punkter fungerar inte för ASE och det är därför du måste använda IP-baserade åtkomst begränsningar med hjälp av den offentliga IP-adressen för Application Gateway-instansen. Om du vill skapa en extern ASE med hjälp av Azure Portal kan du följa den här [snabb](../environment/create-external-ase.md) starten

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager mall för slutfört scenario"

## <a name="considerations-for-kuduscm-site"></a>Överväganden för kudu/SCM-webbplats
SCM-webbplatsen, som även kallas kudu, är en administratörs webbplats som finns för varje webbapp. Det går inte att byta proxy till SCM-platsen och du vill förmodligen också låsa den till enskilda IP-adresser eller ett specifikt undernät.

Om du vill använda samma åtkomst begränsningar som huvud platsen kan du ärva inställningarna med hjälp av följande kommando.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Om du vill ange enskilda åtkomst begränsningar för SCM-platsen kan du lägga till åtkomst begränsningar med hjälp av flaggan--SCM-site, som visas nedan.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Nästa steg
Mer information om App Service-miljön finns i [App Service-miljön dokumentation](/azure/app-service/environment).

Om du vill skydda din webbapp ytterligare kan du hitta information om brand vägg för webbaserade program på Application Gateway i [dokumentationen för Azure Web Application-brandväggen](../../web-application-firewall/ag/ag-overview.md).
