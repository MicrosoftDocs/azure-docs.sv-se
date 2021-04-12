---
title: Diagnostisera prestandaproblem med hjälp av Azure Application Insights | Microsoft Docs
description: Självstudie om att hitta och diagnostisera prestandaproblem i dina program med hjälp av Azure Application Insights.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: 36a581cb958639f4e9d88a8828b341ef1a1919d7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383226"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Hitta och diagnostisera prestandaproblem med Azure Application Insights

Azure Application Insights samlar in telemetri från ditt program för att analysera dess drift och prestanda.  Du kan använda den här informationen till att identifiera problem som kan uppstå eller till att identifiera vilka förbättringar av programmet som skulle gynna flest användare.  Den här kursen hjälper dig att analysera prestandan både för programmets serverkomponenter och ur klientens perspektiv.  Lär dig att:

> [!div class="checklist"]
> * identifiera prestandan för åtgärder på serversidan
> * analysera åtgärder för att fastställa grundorsaken till den dåliga prestandan
> * identifiera de långsammaste åtgärderna på klientsidan
> * analysera information om sidvisningar med frågespråk.


## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med följande arbets belastningar:
    - ASP.NET och webbutveckling
    - Azure Development
- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](../app/asp-net.md).
- [Aktivera Application Insights-profileraren](../app/profiler.md#installation) för ditt program.

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com) .

## <a name="identify-slow-server-operations"></a>Identifiera långsamma serveråtgärder
Application Insights samlar in prestandainformation för de olika åtgärderna i ditt program. Genom att identifiera vilka åtgärder som tar längst tid kan du diagnostisera potentiella problem och fatta välgrundade beslut kring din pågående utveckling för att förbättra programmets prestanda.

1. Välj **Application Insights** och sedan din prenumeration.  
1. Du öppnar panelen **Prestanda** genom att antingen välja **Prestanda** på menyn **Undersök** eller klicka på diagrammet **Serversvarstid**.

    ![Prestanda](media/tutorial-performance/1-overview.png)

2. På panelen **Prestanda** visas antal och genomsnittlig varaktighet för varje åtgärd i programmet.  Du kan använda den här informationen till att identifiera vilka åtgärder som påverkar användarna mest. I det här exemplet är **GET Customers/Details** och **GET Home/Index** lämpliga kandidater att undersöka på grund av deras relativt långa varaktighet och antalet anrop.  Andra åtgärder kan ha en längre varaktighet men anropas sällan, så att förbättra dem skulle knappt märkas.  

    ![Panelen prestanda Server](media/tutorial-performance/2-server-operations.png)

3. I diagrammet visas den genomsnittliga varaktigheten för de valda åtgärderna över tid. Du kan växla till den 95:e percentilen för att hitta prestandaproblemen. Lägg till de åtgärder du är intresserad av genom att fästa dem vid diagrammet.  Det här visar att det finns några toppar värda att undersöka.  Isolera ytterligare genom att minska tidsfönstret för diagrammet.

    ![Fäståtgärder](media/tutorial-performance/3-server-operations-95th.png)

4.  Prestandapanelen till höger visar fördelningen av varaktigheterna för olika begäranden för den valda åtgärden.  Minska fönstret för att starta runt den 95:e percentilen. Insiktskortet om de tre viktigaste beroendena ger en snabb överblick över de externa beroenden som sannolikt bidrar till de långsamma transaktionerna.  Klicka på knappen med antal exempel för att visa en lista över dem. Du kan välja valfritt exempel och visa transaktionsinformationen.

5.  Överblicken visar att anropet till Fabrikamaccount Azure Table bidrar mest till åtgärdens totala varaktighet för transaktionen. Du kan också se att ett undantag orsakade felet. Du kan klicka på valfritt objekt i listan och visa information om det på höger sida. [Lär dig mer om transaktionsdiagnostiken](../app/transaction-diagnostics.md)

    ![Information om åtgärd från slut punkt till slut punkt](media/tutorial-performance/4-end-to-end.png)
    

6.  [**Profileraren**](../app/profiler-overview.md) hjälper dig att komma igång med kod nivå diagnostik genom att visa den faktiska kod som kördes för åtgärden och den tid som krävs för varje steg. Vissa åtgärder kanske inte har någon spårning eftersom Profiler körs med jämna mellanrum.  Med tiden bör fler åtgärder ha spårningar.  Du startar Profiler för åtgärden genom att klicka på **Profiler traces** (Profiler-spårningar).
5.  I spårningen ser du enskilda händelser för varje åtgärd så att du kan diagnostisera vad som gör att åtgärden tar så lång tid att utföra.  Klicka på något av de översta exemplen som har den längsta varaktigheten.
6.  Klicka på **aktiv sökväg** för att markera den angivna sökvägen till händelser som mest bidrar till den totala varaktigheten för åtgärden.  I det här exemplet ser du att det långsammaste anropet kommer från metoden *FabrikamFiberAzureStorage.GetStorageTableData*. Den del som tar mest tid är metoden *CloudTable.CreateIfNotExist*. Om den här raden med kod körs varje gång som funktionen anropas förbrukas onödigt många nätverksanrop och processorresurser. Det bästa sättet att åtgärda koden är att placera den här raden i någon startmetod som bara utförs en gång.

    ![Profiler-information](media/tutorial-performance/5-hot-path.png)

7.  **Prestandatipset** längst upp på skärmen underbygger antagandet att den långa varaktigheten beror på väntetider.  Klicka på länken **Väntar** om du vill läsa om att tolka olika typer av händelser.

    ![Prestandatips](media/tutorial-performance/6-perf-tip.png)

8.   För ytterligare analys kan du klicka på **Hämta spårning** för att ladda ned spårningen. Du kan visa dessa data med [PerfView](https://github.com/Microsoft/perfview#perfview-overview).

## <a name="use-logs-data-for-server"></a>Använd loggdata för servern
 Loggar innehåller ett rikt frågespråk som gör att du kan analysera alla data som samlas in av Application Insights. Du kan använda det till att utföra djupanalys på data om förfrågningar och prestanda.

1. Gå tillbaka till panelen åtgärds information och klicka på ![ loggarna ikon](media/tutorial-performance/app-viewinlogs-icon.png)**i loggar (analys)**

2. Loggar öppnas med en fråga för var och en av vyerna i panelen.  Du kan köra dessa frågor som de är eller ändra dem efter dina behov.  Den första frågan visar åtgärdens varaktighet över tid.

    ![loggar fråga](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Identifiera långsamma klientåtgärder
Förutom att identifiera serverprocesser att optimera så kan Application Insights även analysera ur ett klientperspektiv.  Detta kan hjälpa dig att identifiera möjliga förbättringar av klientkomponenter och att identifiera problem med olika webbläsare eller platser.

1. Välj **webbläsare** under **Undersök** och klicka sedan på **webbläsarens prestanda** eller Välj **prestanda** under **Undersök** och växla till fliken **webbläsare** genom att klicka på växlings knappen Server/webbläsare i övre högra hörnet för att öppna webbläsarens prestanda Sammanfattning. Här ges en visuell översikt över olika telemetrivärden i ditt program ur ett webbläsarperspektiv.

    ![Webbläsarsammanfattning](media/tutorial-performance/8-browser.png)

2. Välj ett av åtgärds namnen och klicka sedan på knappen blå exempel längst ned till höger och välj en åtgärd. Detta visar transaktions detaljerna från slut punkt till slut punkt och på den högra sidan kan du visa egenskaperna för **sid visning**. På så sätt kan du Visa information om klienten som begär sidan, inklusive typen av webbläsare och dess plats. Den här informationen kan hjälpa dig att avgöra om det finns prestandaproblem för specifika typer av klienter.

    ![Sidvisning](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Använd loggar data för klienten
Precis som de data som samlas in för Server prestanda gör Application Insights alla klient data tillgängliga för djup analys med hjälp av loggar.

1. Gå tillbaka till webb läsar sammanfattningen och klicka på ![ loggarna ](media/tutorial-performance/app-viewinlogs-icon.png) **Visa i loggar (analys)**

2. Loggar öppnas med en fråga för var och en av vyerna i panelen. Den första frågan visar varaktigheten för olika sidvisningar över tid.

    ![Loggar fråga](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du identifierar körningsundantag går du vidare till nästa självstudie, där du får lära dig hur du skapar aviseringar som skickas när fel inträffar.

> [!div class="nextstepaction"]
> [Aviseringar om programmets hälsotillstånd](./tutorial-alert.md)

