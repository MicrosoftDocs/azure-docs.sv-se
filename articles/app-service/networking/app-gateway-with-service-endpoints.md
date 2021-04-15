---
title: Application Gateway integrering med tjänstslutpunkter – Azure App Service | Microsoft Docs
description: Beskriver hur Application Gateway integreras med Azure App Service med tjänstslutpunkter.
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
ms.custom: seodec18
ms.openlocfilehash: f1d517ba37bbef95d1863485c8c3b6313f196c11
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374921"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Application Gateway integrering med tjänstslutpunkter
Det finns tre varianter av App Service som kräver en något annorlunda konfiguration av integreringen med Azure Application Gateway. Variationerna omfattar vanliga App Service , även kallat multi-tenant, Internal Load Balancer (ILB) App Service-miljön (ASE) och extern ASE. Den här artikeln går igenom hur du konfigurerar den med App Service (flera klient) och diskuterar överväganden om ILB och extern ASE.

## <a name="integration-with-app-service-multi-tenant"></a>Integrering med App Service (flera innehavare)
App Service (flera klient) har en offentlig Internetuppkopplingsslutpunkt. Med [hjälp av tjänstslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md) kan du endast tillåta trafik från ett visst undernät i en Azure-Virtual Network blockera allt annat. I följande scenario använder vi den här funktionen för att säkerställa att en App Service-instans endast kan ta emot trafik från en Application Gateway instans.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Diagram som visar hur Internet flödar till en Application Gateway i en Azure Virtual Network och flödar därifrån genom en brandväggsikon till instanser av appar i App Service.":::

Det finns två delar i den här konfigurationen förutom att skapa App Service och Application Gateway. Den första delen är att aktivera tjänstslutpunkter i undernätet för den Virtual Network där Application Gateway distribueras. Tjänstslutpunkter ser till att all nätverkstrafik som lämnar undernätet mot App Service taggas med det specifika undernäts-ID:t. Den andra delen är att ange en åtkomstbegränsning för den specifika webbappen för att säkerställa att endast trafik som taggats med detta specifika undernäts-ID tillåts. Du kan konfigurera den med olika verktyg beroende på inställningar.

## <a name="using-azure-portal"></a>Använda Azure Portal
Med Azure Portal följer du fyra steg för att etablera och konfigurera konfigurationen. Om du har befintliga resurser kan du hoppa över de första stegen.
1. Skapa en App Service hjälp av någon av snabbstarterna i App Service dokumentation, till exempel [snabbstart för .NET Core](../quickstart-dotnetcore.md)
2. Skapa en Application Gateway med hjälp av [portalen Snabbstart](../../application-gateway/quick-create-portal.md), men hoppa över avsnittet Lägg till mål för backend.
3. Konfigurera [App Service som en backend i Application Gateway](../../application-gateway/configure-web-app-portal.md), men hoppa över avsnittet Begränsa åtkomst.
4. Skapa slutligen [åtkomstbegränsningen med hjälp av tjänstslutpunkter](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

Du kan nu komma åt App Service via Application Gateway, men om du försöker komma åt App Service direkt bör du få ett 403 HTTP-fel som anger att webbplatsen har stoppats.

![Skärmbild som visar texten för fel 403 – Förbjuden.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
Den [Resource Manager distributionsmallen][template-app-gateway-app-service-complete] etablerar ett fullständigt scenario. Scenariot består av en App Service-instans som är låst med tjänstslutpunkter och åtkomstbegränsningar för att endast ta emot trafik från Application Gateway. Mallen innehåller många smarta standardvärden och unika postkorrigeringar som lagts till i resursnamnen för att det ska vara enkelt. Om du vill åsidosätta dem måste du klona lagringsplatsen eller ladda ned mallen och redigera den. 

Om du vill tillämpa mallen kan du använda knappen Distribuera till Azure som finns i beskrivningen av mallen, eller så kan du använda rätt PowerShell/CLI.

## <a name="using-azure-command-line-interface"></a>Använda Azure-kommandoradsgränssnittet
[Azure CLI-exemplet etablerar](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) en App Service låst med tjänstslutpunkter och åtkomstbegränsningar för att endast ta emot trafik från Application Gateway. Om du bara behöver isolera trafik till en befintlig App Service från en befintlig Application Gateway är följande kommando tillräckligt.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

I standardkonfigurationen ser kommandot till att både konfigurationen av tjänstslutpunkten i undernätet och åtkomstbegränsningen i App Service.

## <a name="considerations-for-ilb-ase"></a>Överväganden för ILB ASE
ILB ASE exponeras inte för Internet och trafik mellan instansen och en Application Gateway är därför redan isolerad till Virtual Network. Följande guide [konfigurerar en](../environment/integrate-with-application-gateway.md) ILB ASE och integrerar den med en Application Gateway med Azure Portal. 

Om du vill säkerställa att endast trafik från Application Gateway-undernätet når ASE kan du konfigurera en nätverkssäkerhetsgrupp (NSG) som påverkar alla webbappar i ASE. För NSG kan du ange undernätets IP-intervall och eventuellt portarna (80/443). Se till att du inte åsidosätter de [NSG-regler som krävs](../environment/network-info.md#network-security-groups) för att ASE ska fungera korrekt.

Om du vill isolera trafik till en enskild webbapp måste du använda IP-baserade åtkomstbegränsningar eftersom tjänstslutpunkter inte fungerar för ASE. IP-adressen ska vara den privata IP-adressen för Application Gateway instansen.

## <a name="considerations-for-external-ase"></a>Överväganden för extern ASE
Extern ASE har en offentlig lastbalanserare som t.ex. App Service. Tjänstslutpunkter fungerar inte för ASE och därför måste du använda IP-baserade åtkomstbegränsningar med hjälp av den offentliga IP-adressen för Application Gateway instansen. Om du vill skapa en extern ASE med Azure Portal kan du följa den här [snabbstarten](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager mall för fullständigt scenario"

## <a name="considerations-for-kuduscm-site"></a>Överväganden för kudu/scm-webbplatsen
SCM-webbplatsen, även kallad kudu, är en administratörswebbplats som finns för varje webbapp. Det går inte att använda omvänd proxy för scm-webbplatsen och du vill förmodligen även låsa den till enskilda IP-adresser eller ett specifikt undernät.

Om du vill använda samma åtkomstbegränsningar som huvudplatsen kan du ärva inställningarna med hjälp av följande kommando.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Om du vill ange individuella åtkomstbegränsningar för scm-webbplatsen kan du lägga till åtkomstbegränsningar med flaggan --scm-site enligt nedan.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Nästa steg
Mer information om App Service-miljön finns i [App Service-miljön dokumentationen](/azure/app-service/environment).

Information om hur du skyddar webbappen ytterligare Web Application Firewall i Application Gateway finns i dokumentationen [Azure Web Application Firewall .](../../web-application-firewall/ag/ag-overview.md)
