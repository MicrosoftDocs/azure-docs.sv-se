---
title: Diagnostisera körningsundantag med hjälp av Azure Application Insights | Microsoft Docs
description: Självstudie om att hitta och diagnostisera körningsundantag i dina program med hjälp av Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 98ccaef716ae2390dcbcfbc7c4a1916359115f93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100628102"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Hitta och diagnostisera körningsundantag med Azure Application Insights

Azure Application Insights samlar in telemetri från ditt program för att identifiera och diagnostisera körningsundantag.  I den här kursen går vi igenom den här processen med ditt program.  Lär dig att:

> [!div class="checklist"]
> * ändra ditt projekt för att aktivera undantagsspårning
> * identifiera undantag för olika komponenter i programmet
> * visa information om ett undantag
> * ladda ned en ögonblicksbild av undantaget till Visual Studio för felsökning
> * analysera information om misslyckade förfrågningar med frågespråk
> * skapa ett nytt arbetsobjekt för att åtgärda den felaktiga koden.


## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med följande arbets belastningar:
    - ASP.NET och webbutveckling
    - Azure Development
- Ladda ned och installera [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger).
- Aktivera [Visual Studio Snapshot Debugger](../app/snapshot-debugger.md)
- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](../app/asp-net.md). 
- I självstudien spåras hur du identifierar ett undantag i ditt program, så ändra koden i din miljö för utveckling eller testning så att du genererar ett undantag. 

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com) .


## <a name="analyze-failures"></a>Analysera fel
Application Insights samlar in eventuella fel i programmet, och du kan se frekvensen över olika åtgärder så att du kan fokusera på de som påverkar upplevelsen mest.  Du kan sedan detaljgranska informationen om dessa fel och identifiera grundorsaken.   

1. Välj **Application Insights** och sedan din prenumeration.  
2. Du kan öppna panelen **Fel** genom att antingen välja **Fel** på menyn **Undersök** eller klicka på diagrammet **Misslyckade förfrågningar**.

    ![Misslyckade förfrågningar](media/tutorial-runtime-exceptions/failed-requests.png)

3. I panelen **Misslyckade förfrågningar** visas antalet misslyckade förfrågningar och hur många användare som påverkats för varje åtgärd i programmet.  Om du sorterar informationen efter användare kan du se vilka fel som påverkar användarna mest.  I det här exemplet är **GET Employees/Create** och **GET Customers/Details** lämpliga kandidater att undersöka på grund av det stora antalet fel och användare som påverkats.  Om du väljer en åtgärd visas ytterligare information om åtgärden i den högra panelen.

    ![Panelen Misslyckade förfrågningar](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. Minska tidsfönstret och zooma in på den period där felfrekvensen har en topp.

    ![Fönstret Misslyckade förfrågningar](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. Du kan visa relaterade exempel genom att klicka på knappen med antalet filtrerade resultat. De föreslagna exemplen har relaterad telemetri från alla komponenter, även om sampling kan ha förekommit. Klicka på ett sökresultat om du vill se information om felet.

    ![Exempel på misslyckade begäranden](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. Informationen om den misslyckade begäran visar Gantt-diagrammet som visar att det uppstod två beroendefel i transaktionen. Det gav upphov till över 50 % av den totala varaktigheten för transaktionen. Den här upplevelsen visar all telemetri, för alla komponenter i ett distribuerat program, som är relaterade till det här åtgärds-ID:t. [Läs mer om den nya upplevelsen](../app/transaction-diagnostics.md) Du kan välja valfritt objekt och visa information om det på höger sida. 

    ![Information om misslyckade begäranden](media/tutorial-runtime-exceptions/failed-request-details.png)

7. I driftinformationen visas också ett FormatException-undantag som verkar ha orsakat felet.  Du kan se att orsaken är ett ogiltigt postnummer. Du kan öppna ögonblicksbilden för felsökningen om du vill se felsökningsinformation på kodnivå i Visual Studio.

    ![Undantagsinformation](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>Identifiera felaktig kod
Snapshot Debugger samlar in ögonblicksbilder av de vanligaste undantagen i ditt program, som är till hjälp när du ska diagnostisera grundorsaken i produktion.  Du kan visa de här ögonblicksbilderna i portalen, se anropsstacken och inspektera variablerna på varje nivå av stacken. Efteråt har du möjlighet att felsöka käll koden genom att hämta ögonblicks bilden och öppna den i Visual Studio 2019 Enterprise.

1. Klicka på **Open debug snapshot** (Öppna ögonblicksbild för felsökning) i egenskaperna för undantaget.
2. Panelen **Debug Snapshot** (Ögonblicksbild för felsökning) öppnas med anropsstacken för förfrågningen.  Om du klickar på en metod visas värdena för alla lokala variabler vid tidpunkten för förfrågningen.  Om du börjar med den översta metoden i det här exemplet ser vi att det finns lokala variabler som inte har något värde.

    ![Ögonblicksbild för felsökning](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. Det första anropet som har giltiga värden är **ValidZipCode**, och vi kan se att ett postnummer angavs tillsammans med bokstäver som inte kan översättas till ett heltal.  Det här verkar vara felet i koden som måste åtgärdas.

    ![Skärm bild som visar ett fel i koden som behöver korrigeras.    ](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. Sedan kan du ladda ned den här ögonblicksbilden till Visual Studio och leta rätt på den faktiska kod som behöver åtgärdas. Det gör du genom att klicka på **Ladda ned ögonblicksbild**.
5. Ögonblicksbilden läses in i Visual Studio.
6. Nu kan du köra en felsökningssession i Visual Studio Enterprise som snabbt identifierar vilken kodrad som orsakade undantaget.

    ![Undantag i kod](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Använda analysdata
Alla data som samlas in av Application Insights lagras i Azure Log Analytics, så att du har tillgång till ett funktionsrikt frågespråk som hjälper dig att analysera data på en mängd olika sätt.  Vi kan använda informationen till att analysera de förfrågningar som genererat undantaget vi undersöker. 

1. Klicka på CodeLens-informationen över koden om du vill visa telemetrin som tillhandahålls av Application Insights.

    ![Kod](media/tutorial-runtime-exceptions/codelens.png)

1. Klicka på **Analyze impact** (Analysera påverkan) för att öppna Application Insights Analytics.  Det fylls i med flera frågor som kan ge detaljerad information om misslyckade förfrågningar, till exempel vilka användare, webbläsare och regioner som påverkas.<br><br>![Skärm bild som visar Application Insights fönstret som innehåller flera frågor.](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Lägg till arbetsobjekt
Om du ansluter Application Insights till ett spårningssystem som Azure DevOps eller GitHub kan du skapa ett arbetsobjekt direkt från Application Insights.

1. Återgå till panelen **Exception Properties** (Egenskaper för undantag) i Application Insights.
2. Klicka på **Nytt arbetsobjekt**.
3. Panelen **Nytt arbetsobjekt** öppnas med detaljer om undantaget ifyllda.  Du kan lägga till ytterligare information innan du sparar objektet.

    ![Nytt arbetsobjekt](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du identifierar körningsundantag går du vidare till nästa självstudie, där du får lära dig hur du identifierar och diagnostiserar prestandaproblem.

> [!div class="nextstepaction"]
> [Identifiera prestandaproblem](./tutorial-performance.md)

