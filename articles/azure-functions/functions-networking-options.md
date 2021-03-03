---
title: Nätverksalternativ för Azure Functions
description: En översikt över alla nätverks alternativ som är tillgängliga i Azure Functions.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: ceef827f7406f8915d205349372a43626c917e4b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729240"
---
# <a name="azure-functions-networking-options"></a>Nätverksalternativ för Azure Functions

I den här artikeln beskrivs de nätverksfunktioner som är tillgängliga i värd alternativen för Azure Functions. Alla följande nätverks alternativ ger dig möjlighet att komma åt resurser utan att använda Internet adresser eller för att begränsa Internet åtkomst till en Function-app.

Värd modellerna har olika nivåer av nätverks isolering tillgänglig. Genom att välja rätt kan du uppfylla kraven på nätverks isolering.

Du kan vara värd för funktions appar på ett par olika sätt:

* Du kan välja bland plan alternativ som körs i en infrastruktur för flera innehavare, med olika nivåer av anslutningar för virtuella nätverk och skalnings alternativ:
    * [Förbruknings planen](consumption-plan.md) skalas dynamiskt som svar på belastning och erbjuder minimala alternativ för nätverks isolering.
    * [Premium-planen](functions-premium-plan.md) kan också skalas dynamiskt och erbjuder mer omfattande nätverks isolering.
    * Azure [App Service plan](dedicated-plan.md) fungerar med en fast skala och erbjuder nätverks isolering som liknar Premium planen.
* Du kan köra funktioner i en [App Service-miljön](../app-service/environment/intro.md). Den här metoden distribuerar din funktion till det virtuella nätverket och ger fullständig nätverks kontroll och isolering.

## <a name="matrix-of-networking-features"></a>Matris med nätverksfunktioner

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>Begränsningar för inkommande åtkomst

Du kan använda åtkomst begränsningar för att definiera en prioriterad lista över IP-adresser som tillåts eller nekas åtkomst till din app. Listan kan innehålla IPv4-och IPv6-adresser eller vissa virtuella nätverks under nät som använder [tjänst slut punkter](#use-service-endpoints). När det finns en eller flera poster finns implicit "Neka alla" i slutet av listan. IP-begränsningar fungerar med alla funktions värd alternativ.

Åtkomst begränsningar är tillgängliga i [Premium](functions-premium-plan.md)-, [konsumtions](consumption-plan.md)-och [App Service](dedicated-plan.md).

> [!NOTE]
> Med nätverks begränsningar på plats kan du distribuera enbart från det virtuella nätverket eller när du har placerat IP-adressen för den dator som du använder för att komma åt Azure Portal på listan Betrodda mottagare. Du kan dock fortfarande hantera funktionen med hjälp av portalen.

Läs mer i [Azure App Service statiska åtkomst begränsningar](../app-service/app-service-ip-restrictions.md).

### <a name="use-service-endpoints"></a>Använda tjänstens slutpunkter

Genom att använda tjänst slut punkter kan du begränsa åtkomsten till de valda Azure-undernäten för virtuella nätverk. Om du vill begränsa åtkomsten till ett speciellt undernät skapar du en begränsnings regel med en **Virtual Network** typ. Du kan sedan välja den prenumeration, det virtuella nätverk och det undernät som du vill tillåta eller neka åtkomst till. 

Om tjänst slut punkter inte redan har Aktiver ATS med Microsoft. Web för det undernät som du har valt, aktive ras de automatiskt om du inte markerar kryss rutan **Ignorera saknade Microsoft. webb tjänst slut punkter** . Scenariot där du kanske vill aktivera tjänstens slut punkter i appen, men inte under nätet beror huvudsakligen på om du har behörighet att aktivera dem i under nätet. 

Om du behöver någon annan för att aktivera tjänstens slut punkter i under nätet markerar du kryss rutan **Ignorera saknade Microsoft. webb tjänst slut punkter** . Din app konfigureras för tjänst slut punkter i förväntat att de ska aktive ras senare i under nätet. 

![Skärm bild av fönstret "Lägg till IP-begränsning" med Virtual Network typen vald.](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Du kan inte använda tjänstens slut punkter för att begränsa åtkomsten till appar som körs i en App Service-miljön. När din app är i ett App Service-miljön kan du kontrol lera åtkomsten till den genom att använda regler för IP-åtkomst. 

Information om hur du konfigurerar tjänst slut punkter finns i [upprätta Azure Functions privat plats åtkomst](functions-create-private-site-access.md).

## <a name="private-endpoint-connections"></a>Anslutningar för privata slut punkter

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

Om du vill anropa andra tjänster som har en privat slut punkts anslutning, t. ex. lagring eller Service Bus, måste du konfigurera appen så att [utgående anrop till privata slut punkter](#private-endpoints)görs.

## <a name="virtual-network-integration"></a>Virtual Network-integration

Med integrering med virtuella nätverk får du till gång till resurser i ett virtuellt nätverk med hjälp av din Function-app.
Azure Functions stöder två typer av integrering av virtuella nätverk:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Integrering av virtuella nätverk i Azure Functions använder delad infrastruktur med App Service Web Apps. Mer information om de två typerna av integrering med virtuella nätverk finns i:

* [Regional integrering av virtuella nätverk](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway – nödvändig virtuell nätverks integrering](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Information om hur du konfigurerar integrering av virtuella nätverk finns i [integrera en Function-app med ett virtuellt Azure-nätverk](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Regional integrering av virtuella nätverk

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Anslut till tjänst slut punktens säkra resurser

För att ge en högre säkerhets nivå kan du begränsa ett antal Azure-tjänster till ett virtuellt nätverk med hjälp av tjänstens slut punkter. Du måste sedan integrera din Function-app med det virtuella nätverket för att få åtkomst till resursen. Den här konfigurationen stöds på alla planer som stöder integrering av virtuella nätverk.

Mer information finns i [tjänst slut punkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Begränsa ditt lagrings konto till ett virtuellt nätverk 

När du skapar en Function-app måste du skapa eller länka till ett allmänt Azure Storage konto som har stöd för BLOB-, Queue-och table-lagring.  Du kan ersätta det här lagrings kontot med ett som skyddas av tjänst slut punkter eller privat slut punkt.  Den här funktionen fungerar för närvarande endast för alla virtuella nätverk som stöds av virtuella nätverk som innehåller standard och Premium, förutom för på de Flex-stämplar där VNet endast är tillgängligt för Premium SKU. Så här konfigurerar du en funktion med ett lagrings konto som är begränsat till ett privat nätverk:

1. Skapa en funktion med ett lagrings konto där tjänstens slut punkter inte är aktiverade.
1. Konfigurera funktionen för att ansluta till ditt virtuella nätverk.
1. Skapa eller konfigurera ett annat lagrings konto.  Det här är lagrings kontot som vi skyddar med tjänstens slut punkter och ansluter vår funktion.
1. [Skapa en fil resurs](../storage/files/storage-how-to-create-file-share.md#create-file-share) på det skyddade lagrings kontot.
1. Aktivera tjänstens slut punkter eller privata slut punkter för lagrings kontot.  
    * Om du använder privata slut punkts anslutningar behöver lagrings kontot en privat slut punkt för-och-under `file` `blob` resurserna.  Om du använder vissa funktioner som Durable Functions, behöver du också `queue` och `table` kan nås via en privat slut punkt anslutning.
    * Om du använder tjänst slut punkter aktiverar du det undernät som är dedikerat för dina funktions program för lagrings konton.
1. Valfritt Kopiera filen och blob-innehållet från funktionen app Storage-kontot till det skyddade lagrings kontot och fil resursen.
1. Kopiera anslutnings strängen för det här lagrings kontot.
1. Uppdatera **program inställningarna** under **konfigurationen** för Function-appen till följande:
    - `AzureWebJobsStorage` till anslutnings strängen för det skyddade lagrings kontot.
    - `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` till anslutnings strängen för det skyddade lagrings kontot.
    - `WEBSITE_CONTENTSHARE` till namnet på fil resursen som skapats i det skyddade lagrings kontot.
    - Skapa en ny inställning med namnet `WEBSITE_CONTENTOVERVNET` och värdet för `1` .
    - Om lagrings kontot använder anslutningar för privata slut punkter, verifiera eller Lägg till följande inställningar
        - `WEBSITE_VNET_ROUTE_ALL` med värdet `1` .
        - `WEBSITE_DNS_SERVER` med värdet `168.63.129.16` 
1. Spara program inställningarna.  

Function-appen startas om och kommer nu att anslutas till ett skyddat lagrings konto.

## <a name="use-key-vault-references"></a>Använda Key Vault-referenser

Du kan använda Azure Key Vault referenser för att använda hemligheter från Azure Key Vault i Azure Functions program utan att behöva göra några kod ändringar. Azure Key Vault är en tjänst som tillhandahåller centraliserad hemlighetshantering med fullständig kontroll över åtkomstprinciper och granskningshistorik.

[Key Vault referenser](../app-service/app-service-key-vault-references.md) fungerar för närvarande inte om ditt nyckel valv skyddas med tjänst slut punkter. Om du vill ansluta till ett nyckel valv med hjälp av integrering med virtuella nätverk måste du anropa Key Vault i din program kod.

## <a name="virtual-network-triggers-non-http"></a>Virtuella nätverks utlösare (icke-HTTP)

För närvarande kan du använda icke-HTTP-utlösare i ett virtuellt nätverk på något av två sätt:

+ Kör din Function-app i en Premium-plan och aktivera stöd för virtuell nätverks utlösare.
+ Kör din Function-app i en App Service plan eller App Service-miljön.

### <a name="premium-plan-with-virtual-network-triggers"></a>Premium plan med virtuella nätverks utlösare

När du kör en Premium-plan kan du ansluta funktioner som inte är HTTP-utlösare till tjänster som körs i ett virtuellt nätverk. För att göra detta måste du aktivera stöd för virtuell nätverks utlösare för din Function-app. Inställningen för **körnings skalnings övervakning** finns i [Azure Portal](https://portal.azure.com) under **konfigurations**  >  **funktionens körnings inställningar**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Du kan också aktivera virtuella nätverks utlösare med hjälp av följande Azure CLI-kommando:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> Aktivering av virtuella nätverks utlösare kan påverka programmets prestanda, eftersom dina App Service plan instanser måste övervaka dina utlösare för att fastställa när de ska skalas. Den här effekten är förmodligen mycket liten.

Virtuella nätverks utlösare stöds i version 2. x och senare av Functions-körningen. Följande typer av icke-HTTP-utlösare stöds.

| Anknytning | Lägsta version |
|-----------|---------| 
|[Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 eller senare |
|[Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 eller senare|
|[Microsoft. Azure. WebJobs. Extensions. Service Bus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 eller senare|
|[Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 eller senare|
|[Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 eller senare|

> [!IMPORTANT]
> När du aktiverar stöd för virtuell nätverks utlösare är det bara de utlösare typer som visas i den föregående tabellen dynamiskt med ditt program. Du kan fortfarande använda utlösare som inte finns i tabellen, men de skalas inte förbi antalet förvärmade instanser. En fullständig lista över utlösare finns i [utlösare och bindningar](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service plan och App Service-miljön med virtuella nätverks utlösare

När din Function-App körs i antingen en App Service plan eller en App Service-miljön, kan du använda funktioner som inte är HTTP-utlösare. För att funktionerna ska kunna aktive ras korrekt måste du vara ansluten till ett virtuellt nätverk med åtkomst till den resurs som definierats i utlösaren.

Anta till exempel att du vill konfigurera Azure Cosmos DB att endast acceptera trafik från ett virtuellt nätverk. I så fall måste du distribuera din Function-app i en App Service plan som tillhandahåller integrering av virtuella nätverk med det virtuella nätverket. Integrering aktiverar en funktion som utlöses av den Azure Cosmos DB resursen.

## <a name="hybrid-connections"></a>Hybridanslutningar

[Hybridanslutningar](../azure-relay/relay-hybrid-connections-protocol.md) är en funktion i Azure Relay som du kan använda för att få åtkomst till program resurser i andra nätverk. Den ger åtkomst från din app till en program slut punkt. Du kan inte använda den för att få åtkomst till ditt program. Hybridanslutningar är tillgängligt för funktioner som körs i Windows i alla utom förbruknings planen.

Som används i Azure Functions motsvarar varje hybrid anslutning en enskild TCP-värd och port-kombination. Det innebär att hybrid anslutningens slut punkt kan finnas på alla operativ system och program så länge som du har åtkomst till en TCP-lyssnings port. Hybridanslutningar funktionen känner inte igen eller bryr sig om vad applikations protokollet är eller vad du använder. Den ger bara nätverks åtkomst.

Mer information finns i [App Service dokumentationen för hybridanslutningar](../app-service/app-service-hybrid-connections.md). Samma konfigurations steg har stöd för Azure Functions.

>[!IMPORTANT]
> Hybridanslutningar stöds endast på Windows-planer. Linux stöds inte.

## <a name="outbound-ip-restrictions"></a>Utgående IP-begränsningar

Utgående IP-begränsningar är tillgängliga i en Premium-plan, App Service plan eller App Service-miljön. Du kan konfigurera utgående begränsningar för det virtuella nätverk där App Service-miljön har distribuerats.

När du integrerar en Function-app i en Premium-plan eller en App Service plan med ett virtuellt nätverk kan appen fortfarande göra utgående anrop till Internet som standard. Genom att lägga till program inställningen `WEBSITE_VNET_ROUTE_ALL=1` tvingar du all utgående trafik att skickas till det virtuella nätverket, där regler för nätverks säkerhets grupper kan användas för att begränsa trafiken.

## <a name="automation"></a>Automation
Med följande API: er kan du hantera regionala virtuella nätverks integreringar program mässigt:

+ **Azure CLI**: Använd [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) kommandona för att lägga till, Visa eller ta bort en regional integrering av virtuella nätverk.  
+ **Arm-mallar**: regional integrering av virtuella nätverk kan aktive ras med hjälp av en Azure Resource Manager mall. Ett fullständigt exempel finns i [snabb starts mal len för funktioner](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om nätverk och Azure Functions:

* [Följ självstudien om att komma igång med integrering av virtuella nätverk](./functions-create-vnet.md)
* [Läs vanliga frågor och svar om funktionen Networking](./functions-networking-faq.md)
* [Lär dig mer om virtuell nätverks integrering med App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Lär dig mer om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera fler nätverksfunktioner och kontroll funktioner med App Service miljöer](../app-service/environment/intro.md)
* [Anslut till enskilda lokala resurser utan brand Väggs ändringar med hjälp av Hybridanslutningar](../app-service/app-service-hybrid-connections.md)
