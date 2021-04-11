---
title: Förstå dina kunder i Azure Application Insights | Microsoft Docs
description: Självstudiekurs om att använda Azure Application Insights till att förstå hur kunderna använder din app.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/20/2017
ms.custom: mvc
ms.openlocfilehash: 25c85571b0c5ab1cf1a89564dd93e305d026ce69
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383039"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Använda Azure Application Insights till att förstå hur kunderna använder din app

Azure Application Insights samlar in användningsinformation för att hjälpa dig att förstå hur dina användare interagerar med appen.  Den här självstudien går igenom de olika resurser som är tillgängliga för att analysera den här informationen.  Du lär dig hur du:

> [!div class="checklist"]
> * Analyserar information om användare som har åtkomst till din app
> * Använder sessionsinformation för att analysera hur kunder använder din app
> * Definierar trattar där du kan jämföra din önskade användaraktivitet med den faktiska aktiviteten 
> * Skapar en arbetsbok för att konsolidera visualiseringar och frågor i ett enda dokument
> * Grupperar liknande användare för att analysera dem tillsammans
> * Lär dig vilka användare som kommer tillbaka till din app
> * Inspekterar hur användarna navigerar genom appen


## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med följande arbets belastningar:
    - ASP.NET och webbutveckling
    - Azure Development
- Ladda ned och installera [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger).
- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](../app/asp-net.md). 
- [Skicka telemetridata från appen](../app/usage-overview.md#send-telemetry-from-your-app) för att lägga till anpassade händelser/sidvisningar
- Skicka [användarkontext](./usage-overview.md) för att spåra vad en användare gör över tid och utnyttja användningsfunktionerna fullt ut.

## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com) .

## <a name="get-information-about-your-users"></a>Få information om dina användare
På panelen **Användare** kan du få förstå viktig information om dina användare på flera olika sätt. Du kan använda den här panelen till att förstå sådan information som var dina användare ansluter från, information om deras klient och vilka områden i appen de använder. 

1. Välj **Application Insights** och sedan din prenumeration.
2. Välj **Användare** på menyn.
3. Standardvyn visar antalet unika användare som har anslutit till din app de senaste 24 timmarna.  Du kan ändra tidsfönstret och ange olika villkor för att filtrera den här informationen.

    ![Frågebyggaren](media/tutorial-users/QueryBuilder.png)

6. Klicka på listrutan **Under** och ändra tidsfönstret till 7 dagar.  Det här ökar data som ingår i de olika diagrammen i panelen.

    ![Ändra tidsintervallet](media/tutorial-users/TimeRange.png)

4. Klicka på listrutan **Dela med** för att lägga till en uppdelning av en användaregenskap i diagrammet.  Välja **land eller region**.  Diagrammet innehåller samma data, men du kan visa en uppdelning av antalet användare för varje land/region.

    ![Diagram för land eller region](media/tutorial-users/CountryorRegion.png)

5. Placera markören över olika staplar i diagrammet och Observera att antalet för varje land/region endast visar tids perioden som representeras av det fältet.
6. Ta en titt på kolumnen **Insikter** till höger som utför analyser av dina användardata.  Det här ger information som antalet unika sessioner under tidsperioden och poster med gemensamma egenskaper som utgör en betydande del av användardata 

    ![Kolumnen Insikter](media/tutorial-users/insights.png)


## <a name="analyze-user-sessions"></a>Analysera användarsessioner
Panelen **Sessioner** liknar panelen **Användare**.  **Användare** hjälper dig att förstå information om användarna som har åtkomst till din app och **Sessioner** hjälper dig att förstå hur de användare använder din app.  

1. Välj **Sessioner** på menyn.
2. Ta en titt på diagrammet och observera att du har samma alternativ för att filtrera och dela upp data som på panelen **Användare**.

    ![Frågebyggaren för sessioner](media/tutorial-users/SessionsBuilder.png)

3. I fönstret **Sample of these sessions** (Exempel på dessa sessioner) till höger listar sessioner som innehåller ett stort antal händelser.  Det här är intressanta sessioner att analysera.

    ![Exempel på dessa sessioner](media/tutorial-users/SessionsSample.png)

4. Klicka på en av sessionerna för att visa dess **sessionstidslinje**, som visar varje åtgärd i sessionerna.  Det här kan hjälpa dig att identifiera information som sessioner med ett stort antal undantag.

    ![Sessionstidslinje](media/tutorial-users/SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Gruppera liknande användare
En **kohort** är en uppsättning användare grupperade på liknande egenskaper.  Du kan använda kohorter för att filtrera data i andra panelen, så att du kan analysera olika grupper av användare.  Till exempel kanske du bara vill analysera användare som har slutfört ett köp.

1.  Välj **Kohorter** på menyn.
2.  Klicka på **Nytt** om du vill skapa en kohort.
3.  Välj listrutan **Who used** (Som har använt) och välj en åtgärd.  Bara användare som har använt den här åtgärden inom rapportens tidsperiod inkluderas.

    ![Kohort som har utfört angivna åtgärder](media/tutorial-users/CohortsDropdown.png)

4.  Välj **Användare** på menyn.
5.  I listrutan **Visa** väljer du den kohort du precis har skapat.  Data för diagrammet begränsas till de användarna.

    ![Kohort i användarverktyget](media/tutorial-users/UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Jämför önskad aktivitet med verkligheten
Medan de tidigare panelerna fokuserar på vad användarna av din app har gjort fokuserar **trattar** på vad du vill att användarna ska göra.  En tratt representerar en uppsättning steg i din app och procentandelen användare som har gått mellan stegen.  Du kan till exempel skapa en tratt som mäter procentandelen användare som ansluter till appen som söker efter en produkt.  Du kan sedan se procentandelen användare som lägger till den produkten i en kundvagn och sedan procentandelen av dem som slutför ett köp.

1. Välj **Trattar** på menyn och klicka sedan på **Ny**. 

    ![Skärm bild som visar hur du skapar en ny tratt.](media/tutorial-users/funnelsnew.png)

2. Ange ett **trattnamn**.
3. Skapa en tratt med minst två steg genom att välja en åtgärd för varje steg.  Listan över åtgärder skapas utifrån användningsdata som samlas in av Application Insights.

    ![Skärm bild som visar hur du skapar steg i en ny tratt.](media/tutorial-users/funnelsedit.png)

4. Klicka på **Spara** för att spara tratten och visa sedan resultaten.  Fönstret till höger om tratten visar de vanligaste händelserna före den första aktiviteten och efter den sista aktiviteten för att hjälpa dig att förstå användartendenser runt den specifika sekvensen.

    ![Skärm bild som visar händelse resultatet av en nyligen skapad tratt.](media/tutorial-users/funnelsright.png)


## <a name="learn-which-customers-return"></a>Få reda på vilka kunder som kommer tillbaka
**Kvarhållning** hjälper dig att första vilka användare som kommer tillbaka till appen.  

1. Välj **Kvarhållning** på menyn.
2. Som standard innehåller den analyserade informationen användare som har utfört en åtgärd och sedan kommit tillbaka och utfört en åtgärd.  Du kan ändra det här filtret så att det, till exempel, bara inkluderar de användare som kommer tillbaka efter att ha slutfört ett köp.

    ![Skärm bild som visar hur du anger ett bevarande filter.](media/tutorial-users/retentionquery.png)

3. De återkommande användarna som matchar villkoren visas i grafiskt format och tabellformat för olika tidsvaraktigheter.  Det vanliga mönstret visar en gradvis nedgång i antalet återkommande användare över tid.  En plötslig nedgång från en tidsperiod till nästa kan ge upphov till oro. 

    ![Skärm bild som visar ett diagram för användare som matchar de villkor som angetts för ett bevarande filter.](media/tutorial-users/retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analysera användarnavigering
Ett **användarflöde** visualiserar hur användare navigerar mellan sidorna och funktionerna i din app.  Det här hjälper dig att besvara frågor som var användarna normalt går från en viss sida, hur de normalt avslutar din app och om det finns några åtgärder som upprepas regelbundet.

1.  Välj **Användarflöden** på menyn.
2.  Klicka på **Nytt** för att skapa ett nytt användarflöde och klicka sedan på **Redigera** för att redigera dess egenskaper.
3.  Öka **tidsintervallet** till 7 dagar och välj sedan den inledande händelsen.  Flödet spårar användarsessioner som börjar med den händelsen.

    ![Skärm bild som visar hur du skapar ett nytt användar flöde.](media/tutorial-users/flowsedit.png)

4.  Användarflödet visas och du kan se olika användarvägar och deras antal sessioner.  Blå linjer anger en åtgärd som användaren har utfört efter den aktuella åtgärden.  En blå linje anger att användarsessionen är slut.

    ![Skärm bild som visar visning av användar Sök vägar och antal sessioner för ett användar flöde.](media/tutorial-users/flows.png)

5.  Om du vill ta bort en händelse från flödet klickar du på **x** i hörnet av åtgärden och klickar sedan på **Skapa diagram**.  Diagrammet ritas om med instanserna av den händelsen borttagna.  Klicka på **Redigera** för att se att händelsen nu har lagts till i **Exkluderade händelser**.

    ![Skärm bild som visar en lista över undantagna händelser för ett användar flöde.](media/tutorial-users/flowsexclude.png)

## <a name="consolidate-usage-data"></a>Konsolidera användningsdata
**Arbetsböcker** kombinerar datavisualiseringar, analysfrågor och text till interaktiva dokument.  Du kan använda arbetsböcker för att gruppera gemensam användningsinformation, konsolidera information från en viss incident eller rapportera tillbaka till ditt team om appens användning.

1.  Välj **Arbetsböcker** på menyn.
2.  Klicka på **Nytt** för att skapa en ny arbetsbok.
3.  En fråga har redan angetts som innehåller alla användningsdata på sista dagen visade som ett stapeldiagram.  Du kan använda den här frågan, redigera den manuellt eller klicka på **Exempelfrågor** för att välja från andra användbara frågor.

    ![Skärm bild som visar en lista över exempel frågor som du kan använda.](media/tutorial-users/samplequeries.png)

4.  Klicka på **Klar med redigeringen**.
5.  Klicka på **Redigera** i det övre fönstret för att redigera texten högst upp i arbetsboken.  Det här formateras med markdown.

    ![Skärm bild som visar hur du redigerar texten överst i arbets boken.](media/tutorial-users/markdown.png)

6.  Klicka på **Lägg till användare** för att lägga till ett diagram med användarinformation.  Redigera informationen om diagrammet om du vill och klicka sedan på **Klar med redigeringen** för att spara det.


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du analyserar dina användare kan du gå vidare till nästa självstudie och lära dig hur du skapar anpassade instrumentpaneler som kombinerar den här informationen med andra användbara data om din app.

> [!div class="nextstepaction"]
> [Skapa anpassade instrumentpaneler](./tutorial-app-dashboards.md)