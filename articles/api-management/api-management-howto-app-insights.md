---
title: Integrera Azure API Management med Azure Application Insights
titleSuffix: Azure API Management
description: Lär dig hur du loggar och visar händelser från Azure API Management i Azure Application insikter.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564262"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Så integrerar du Azure API Management med Azure Application Insights

Azure API Management möjliggör enkel integrering med Azure Application Insights – en utöknings bar tjänst för webbutvecklare som skapar och hanterar appar på flera plattformar. Den här guiden går igenom alla steg i denna integrering och beskriver strategier för att minska prestanda påverkan på din API Management tjänst instans.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här guiden måste du ha en Azure API Management-instans. Om du inte har någon, slutför du [själv studie kursen](get-started-create-service-instance.md) först.

## <a name="create-an-application-insights-instance"></a>Skapa en Application Insights-instans

Innan du kan använda Application Insights måste du först skapa en instans av tjänsten. Anvisningar för hur du skapar en instans med hjälp av Azure Portal finns i [arbets ytan-baserade Application Insights resurser](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Skapa en anslutning mellan Application Insights och API Management

1. Gå till **Azure API Management Service-instansen** i **Azure Portal**.
1. Välj **Application Insights** på menyn till vänster.
1. Klicka på **+ Lägg till**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Skärm bild som visar var du kan lägga till en ny anslutning":::
1. Välj den tidigare skapade **Application Insights** -instansen och ange en kort beskrivning.
1. Klicka på **Skapa**.
1. Du har precis skapat en Application Insights-loggad med en Instrumentation-nyckel. Den bör nu visas i listan.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Skärm bild som visar var du kan visa den nyligen skapade Application Insights loggaren med Instrumentation-tangenten":::

> [!NOTE]
> Efter scenen skapas en [loggad](/rest/api/apimanagement/2019-12-01/logger/createorupdate) entitet i API Management-instansen som innehåller Instrumentation-nyckeln för Application Insights-instansen.

## <a name="enable-application-insights-logging-for-your-api"></a>Aktivera Application Insights loggning för ditt API

1. Gå till **Azure API Management Service-instansen** i **Azure Portal**.
1. Välj **API:er** i menyn till vänster.
1. Klicka på ditt API, i det här fallet **demo konferens-API**. Välj en version om den är konfigurerad.
1. Gå till fliken **Inställningar** från det översta fältet.
1. Rulla ned till avsnittet **diagnostiska loggar** .  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="App Insights-loggning":::
1. Markera kryss rutan **Aktivera** .
1. Välj din anslutna logg i list rutan **destination** .
1. Indata **100** som **sampling (%)** och markera kryss rutan **alltid logg fel** .
1. Välj **Spara**.

> [!WARNING]
> Att åsidosätta standardvärdet **0** i **antalet nytto Last byte till logg** inställningen kan avsevärt minska prestandan för dina API: er.

> [!NOTE]
> Från scenen skapas en [diagnostisk](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entitet med namnet "applicationinsights" på API-nivå.

| Inställningsnamn                        | Värdetyp                        | Beskrivning                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivera                              | boolean                           | Anger om loggning av detta API är aktiverat.                                                                                                                                                                                                                                                                                                |
| Mål                         | Azure Application Insights-loggning | Anger Azure Application insikts logg som ska användas                                                                                                                                                                                                                                                                                           |
| Sampling (%)                        | decimal                           | Värden från 0 till 100 (procent). <br/> Anger procent andelen begär Anden som ska loggas till Application Insights. 0% sampling innebär att noll begär Anden loggas, medan 100% sampling innebär att alla begär Anden loggas. <br/> Använd den här inställningen för att minska prestanda vid loggning av begär anden till Application Insights. Se [prestanda effekter och logg sampling](#performance-implications-and-log-sampling). |
| Logga alltid fel                   | boolean                           | Om den här inställningen väljs loggas alla avbrott till Application Insights, oavsett vilken **samplings** inställning som används.   
| Logga klientens IP-adress | |  Om den här inställningen väljs kommer klientens IP-adress för API-begäranden att loggas till Application Insights.                                         |
| Utförlighet         |                                   | Anger nivån för utförlighet. Endast anpassade spår med högre allvarlighets grad kommer att loggas. Standard: information.      | 
| Korrelations protokoll |  |  Välj protokoll som används för att korrelera telemetri som skickas av flera komponenter. Standard: bakåtkompatibelt <br/>Mer information finns i [telemetri-korrelation i Application Insights](../azure-monitor/app/correlation.md).  |
| Grundläggande alternativ: sidhuvuden till loggen              | lista                              | Anger de huvuden som ska loggas i Application Insights för förfrågningar och svar.  Standard: inga huvuden loggas.                                                                                                                                                                                                             |
| Grundläggande alternativ: antal nytto Last byte som ska loggas | heltal                           | Anger hur många första byte i bröd texten loggas till Application Insights för begär Anden och svar.  Standard: 0.                                                                                                                                                                                                    |
| Avancerade alternativ: frontend-begäran  |                                   | Anger om och hur *klient dels begär Anden* ska loggas till Application Insights. *Frontend-begäran* är en inkommande begäran till Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: frontend-svar |                                   | Anger om och hur *klient delens svar* ska loggas till Application Insights. *Frontend-svar* är ett utgående svar från Azure API Management-tjänsten.                                                                                                                                                                   |
| Avancerade alternativ: backend-begäran   |                                   | Anger om och hur *Server dels begär Anden* ska loggas till Application Insights. *Backend-begäran* är en begäran utgående från Azure API Management-tjänsten.                                                                                                                                                                        |
| Avancerade alternativ: Server dels svar  |                                   | Anger om och hur *Server dels svar* ska loggas till Application Insights. *Server dels svar* är ett inkommande svar till Azure API Management-tjänsten.                                                                                                                                                                       |

> [!NOTE]
> Du kan ange loggar på olika nivåer – enskild API-logg eller en loggare för alla API: er.
>  
> Om du anger båda:
> + om de är olika loggar, kommer båda att användas (flera Plex-loggar).
> + om de är samma loggar men har olika inställningar, åsidosätter det en för Single API (mer detaljerad nivå) den för alla API: er.

## <a name="what-data-is-added-to-application-insights"></a>Vilka data som läggs till i Application Insights

Application Insights tar emot:

+ *Begär* telemetri-objekt, för varje inkommande begäran (*frontend-begäran*, frontend- *svar*).
+ Objekt för *beroende* telemetri, för alla begär Anden som vidarebefordras till en backend-tjänst (*backend-begäran*, *Server dels svar*)
+ Objekt för *undantag* av telemetri för varje misslyckad begäran:
    + misslyckades på grund av en stängd klient anslutning
    + utlöst ett *On-Error* -avsnitt i API-principerna
    + har svars kodens HTTP-statuskod som matchar 4xx eller 5xx.
+ *Spåra* telemetri-objekt, om du konfigurerar en [spårnings](api-management-advanced-policies.md#Trace) princip. `severity`Inställningen i `trace` principen måste vara lika med eller större än `verbosity` inställningen i Application Insights loggning.

> [!NOTE]
> Se [Application Insights gränser](../azure-monitor/service-limits.md#application-insights) för information om den maximala storleken och antalet mått och händelser per Application Insights instans.

## <a name="performance-implications-and-log-sampling"></a>Prestanda konsekvenser och logg sampling

> [!WARNING]
> Loggning av alla händelser kan ha allvarliga prestanda effekter, beroende på antalet inkommande begär Anden.

Med hjälp av interna belastnings test gjorde aktivering av den här funktionen en 40%-50% minskning i data flödet när begär ande frekvensen överskrider 1 000 begär Anden per sekund. Application Insights har utformats för att kunna använda statistisk analys för att utvärdera programmets prestanda. Den är inte avsedd att vara ett gransknings system och passar inte för att logga varje enskild begäran för API: er med hög volym.

Du kan ändra antalet begär Anden som loggas genom att justera **samplings** inställningen (se föregående steg). Värdet 100% innebär att alla begär Anden loggas och 0% visar ingen loggning. **Samplingen** bidrar till att minska antalet telemetri, vilket effektivt förhindrar betydande prestanda försämring samtidigt som du fortfarande har fördelarna med loggning.

Att hoppa över loggning av rubriker och brödtext för förfrågningar och svar har också positiv påverkan på att minska prestanda problemen.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [Azure Application insikter](/azure/application-insights/).
+ Överväg att [Logga med Azure Event Hubs](api-management-howto-log-event-hubs.md).
