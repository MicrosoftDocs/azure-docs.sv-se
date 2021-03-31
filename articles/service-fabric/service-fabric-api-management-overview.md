---
title: Översikt över Azure Service Fabric med API Management
description: Den här artikeln är en introduktion till att använda Azure API Management som en gateway för dina Service Fabric-program.
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 32f47d62cc9dda7cc88421dbf616bf69ffe152fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575694"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric med Azure API Management-översikt

Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. I Service Fabric kan en gateway vara en tillstånds lös tjänst, till exempel ett [ASP.net Core program](service-fabric-reliable-services-communication-aspnetcore.md)eller en annan tjänst som har utformats för trafik ingångar, till exempel [Event Hubs](../event-hubs/index.yml), [IoT Hub](../iot-hub/index.yml)eller [Azure API Management](../api-management/index.yml).

Den här artikeln är en introduktion till att använda Azure API Management som en gateway för dina Service Fabric-program. API Management integreras direkt med Service Fabric, så att du kan publicera API: er med en omfattande uppsättning regler för routning till Server delens Service Fabric tjänster.

## <a name="availability"></a>Tillgänglighet

> [!IMPORTANT]
> Den här funktionen är tillgänglig på nivån **Premium** och **developer** of API Management på grund av det nödvändiga stödet för virtuellt nätverk.

## <a name="architecture"></a>Arkitektur

En vanlig Service Fabric-arkitektur använder ett webb program med en enda sida som gör HTTP-anrop till backend-tjänster som visar HTTP-API: er. [Exempel programmet Service Fabric kom igång](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) visar ett exempel på den här arkitekturen.

I det här scenariot fungerar en tillstånds lös webb tjänst som gateway i Service Fabric-programmet. Den här metoden kräver att du skriver en webb tjänst som kan proxy HTTP-begäranden till backend-tjänster, som du ser i följande diagram:

![Diagram som visar hur en tillstånds lös webb tjänst fungerar som gateway i Service Fabric-programmet.][sf-web-app-stateless-gateway]

När program växer i komplexitet, så gör de gatewayer som måste presentera ett API framför myriaden backend-tjänster. Azure API Management är utformat för att hantera komplexa API: er med regler för routning, åtkomst kontroll, hastighets begränsning, övervakning, händelse loggning och cachelagring av svar med minimalt arbete på din sida. Azure API Management stöder Service Fabric tjänst identifiering, partitions matchning och replik val för att intelligent dirigera begär Anden direkt till backend-tjänster i Service Fabric så du behöver inte skriva din egen tillstånds lösa API-Gateway. 

I det här scenariot betjänas webb gränssnittet fortfarande via en webb tjänst, medan HTTP API-anrop hanteras och dirigeras via Azure API Management, som du ser i följande diagram:

![Diagram som visar hur webb gränssnittet fortfarande betjänas via en webb tjänst, medan HTTP API-anrop hanteras och dirigeras via Azure-API Management.][sf-apim-web-app]

## <a name="application-scenarios"></a>Programscenarier

Tjänster i Service Fabric kan vara antingen tillstånds lösa eller tillstånds känsliga, och de kan partitioneras med ett av tre scheman: Singleton, int-64-intervall och namngett. Matchning av tjänst slut punkten kräver att en speciell partition av en angiven tjänst instans identifieras. När du löser en slut punkt för en tjänst måste både tjänst instans namnet (till exempel `fabric:/myapp/myservice` ) och den specifika partitionen för tjänsten anges, förutom vid singleton-partition.

Azure API Management kan användas med valfri kombination av tillstånds lösa tjänster, tillstånds känsliga tjänster och scheman för partitionering.

## <a name="send-traffic-to-a-stateless-service"></a>Skicka trafik till en tillstånds lös tjänst

I det enklaste fallet vidarebefordras trafiken till en tillstånds lös tjänst instans. För att åstadkomma detta innehåller en API Management-åtgärd en princip för inkommande bearbetning med en Service Fabric backend-server som mappar till en bestämd tillstånds lös tjänst instans i Service Fabric Server delen. Begär Anden som skickas till tjänsten skickas till en slumpmässig instans av tjänsten.

**Exempel**

I följande scenario innehåller ett Service Fabric-program en tillstånds lös tjänst med namnet `fabric:/app/fooservice` , som exponerar ett internt HTTP-API. Namnet på tjänst instansen är känt och kan hårdkodas direkt i den API Management principen för inkommande bearbetning. 

![Diagram som visar ett Service Fabric-program som innehåller en tillstånds lös tjänst som exponerar ett internt HTTP-API.][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Skicka trafik till en tillstånds känslig tjänst

Precis som i det tillstånds lösa tjänst scenariot kan trafiken vidarebefordras till en tillstånds känslig tjänst instans. I det här fallet innehåller en API Management-åtgärd en princip för inkommande bearbetning med en Service Fabric backend-server som mappar en begäran till en speciell partition av en särskilt *tillstånds känslig* tjänst instans. Partitionen för att mappa varje begäran till beräknas via en lambda-metod som använder vissa indata från den inkommande HTTP-begäran, t. ex. ett värde i URL-sökvägen. Principen kan konfigureras att endast skicka begär anden till den primära repliken eller till en slumpmässig replik för Läs åtgärder.

**Exempel**

I följande scenario innehåller ett Service Fabric-program en partitionerad tillstånds känslig tjänst med namnet `fabric:/app/userservice` som exponerar ett internt HTTP-API. Namnet på tjänst instansen är känt och kan hårdkodas direkt i den API Management principen för inkommande bearbetning.  

Tjänsten partitioneras med hjälp av ett Int64-partitionsschema med två partitioner och ett nyckel intervall som sträcker sig över `Int64.MinValue` till `Int64.MaxValue` . Backend-principen beräknar en partitionsnyckel inom intervallet genom att konvertera `id` värdet som anges i sökvägen för URL-begäran till ett 64-bitars heltal, även om alla algoritmer kan användas för att beräkna partitionsnyckel. 

![Översikt över Service Fabric med Azure API Management-topologi][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Skicka trafik till flera tillstånds lösa tjänster

I mer avancerade scenarier kan du definiera en API Management-åtgärd som mappar begär anden till fler än en tjänst instans. I det här fallet innehåller varje åtgärd en princip som mappar begär anden till en specifik tjänst instans baserat på värden från den inkommande HTTP-begäran, till exempel URL-sökvägen eller frågesträngen, och i händelse av tillstånds känsliga tjänster, en partition inom tjänst instansen.

För att uppnå detta innehåller en API Management-åtgärd en princip för inkommande bearbetning med en Service Fabric backend-server som mappar till en tillstånds lös tjänst instans i Service Fabric backend baserat på värden som hämtats från den inkommande HTTP-begäran. Begär anden till en tjänst skickas till en slumpmässig instans av tjänsten.

**Exempel**

I det här exemplet skapas en ny tillstånds lös tjänst instans för varje användare av ett program med ett dynamiskt genererat namn med hjälp av följande formel:

- `fabric:/app/users/<username>`

  Varje tjänst har ett unikt namn, men namnen är inte kända, eftersom tjänsterna skapas som svar på användarens eller administratörens indata och kan därför inte hårdkodas i APIM-principer eller regler för routning. I stället genereras namnet på den tjänst som en begäran ska skickas till i definition av backend-principen från det `name` värde som anges i sökvägen för URL-begäran. Exempel:

  - En begäran till `/api/users/foo` dirigeras till tjänst instansen `fabric:/app/users/foo`
  - En begäran till `/api/users/bar` dirigeras till tjänst instansen `fabric:/app/users/bar`

![Diagram som visar ett exempel där en ny tillstånds lös tjänst instans skapas för varje användare av ett program med ett dynamiskt genererat namn.][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Skicka trafik till flera tillstånds känsliga tjänster

Precis som i det tillstånds lösa tjänst exemplet kan en API Management-åtgärd mappa begär anden till fler än en **tillstånds känslig** tjänst instans, vilket innebär att du även kan behöva utföra en partitions upplösning för varje tillstånds känslig tjänst instans.

För att uppnå detta innehåller en API Management-åtgärd en princip för inkommande bearbetning med en Service Fabric backend-server som mappar till en tillstånds känslig tjänst instans i Service Fabric backend baserat på värden som hämtats från den inkommande HTTP-begäran. Förutom att mappa en begäran till en angiven tjänst instans kan begäran också mappas till en speciell partition inom tjänst instansen och eventuellt till antingen den primära repliken eller en slumpmässig sekundär replik i partitionen.

**Exempel**

I det här exemplet skapas en ny tillstånds känslig tjänst instans för varje användare av programmet med ett dynamiskt genererat namn med hjälp av följande formel:

- `fabric:/app/users/<username>`

  Varje tjänst har ett unikt namn, men namnen är inte kända, eftersom tjänsterna skapas som svar på användarens eller administratörens indata och kan därför inte hårdkodas i APIM-principer eller regler för routning. I stället genereras namnet på den tjänst som en begäran skickas till i definitionen av backend-principen från det `name` värde som tillhandahöll sökvägen till URL-begäran. Exempel:

  - En begäran till `/api/users/foo` dirigeras till tjänst instansen `fabric:/app/users/foo`
  - En begäran till `/api/users/bar` dirigeras till tjänst instansen `fabric:/app/users/bar`

Varje tjänst instans partitioneras också med hjälp av ett Int64-partitionsschema med två partitioner och ett nyckel intervall som sträcker sig över `Int64.MinValue` till `Int64.MaxValue` . Backend-principen beräknar en partitionsnyckel inom intervallet genom att konvertera `id` värdet som anges i sökvägen för URL-begäran till ett 64-bitars heltal, även om alla algoritmer kan användas för att beräkna partitionsnyckel. 

![Diagram som visar att varje tjänst instans också partitioneras med hjälp av Int64-partitionsschema med två partitioner och ett nyckel intervall som sträcker sig över Int64. MinValue till Int64. MaxValue.][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Nästa steg

Följ [själv studie kursen](service-fabric-tutorial-deploy-api-management.md) för att konfigurera ditt första Service Fabric-kluster med API Management-och Flow-begäranden via API Management till dina tjänster.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
