---
title: Azure Application insikter om användning kohorter | Microsoft Docs
description: Analysera olika uppsättningar eller användare, sessioner, händelser eller åtgärder som har något gemensamt
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2c35356951560b11a605334aba4c26dbc38086de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583557"
---
# <a name="application-insights-cohorts"></a>Application Insights kohorter

En kohort är en uppsättning användare, sessioner, händelser eller åtgärder som har något gemensamt. I Azure Application insikter definieras kohorter av en analys fråga. I de fall där du behöver analysera en viss uppsättning användare eller händelser upprepade gånger kan kohorter ge dig mer flexibilitet för att uttrycka exakt den mängd du är intresse rad av.

![Fönstret kohorter](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorter jämfört med grundläggande filter

Kohorter används på liknande sätt som filter. Men kohorter-definitioner skapas utifrån anpassade analys frågor, så de är mycket mer anpassningsbara och komplexa. Till skillnad från filter kan du spara kohorter så att andra medlemmar i ditt team kan återanvända dem.

Du kan definiera en kohort av användare som har försökt med en ny funktion i din app. Du kan spara den här kohort i din Application Insights-resurs. Det är enkelt att analysera den här sparade gruppen av vissa användare i framtiden.

> [!NOTE]
> När de har skapats är kohorter tillgängliga från verktygen användare, sessioner, händelser och Användarflöden.

## <a name="example-engaged-users"></a>Exempel: engagerade användare

Ditt team definierar en engagerad användare som alla som använder appen fem eller fler gånger under en månad. I det här avsnittet definierar du en kohort för dessa användare.

1. Öppna verktyget kohorter.

2. Välj fliken **mallgalleriet** . Du ser en samling mallar för olika kohorter.

3. Välj **engagerade användare – efter dagar som används**.

    Det finns tre parametrar för den här kohort:
    * **Aktiviteter** där du väljer vilka händelser och sid visningar som ska räknas som "användning".
    * **Period**, definitionen för en månad.
    * **UsedAtLeastCustom**, antalet gånger som användare måste använda något inom en period för att räknas som sysselsatt.

4. Ändra **UsedAtLeastCustom** till **5 + dagar** och lämna **period** på standardvärdet 28 dagar.

    ![Engagerade användare](./media/usage-cohorts/003.png)

    Nu representerar den här kohort alla användar-ID: n som skickas med en anpassad händelse-eller sid visning på 5 separata dagar under de senaste 28.

5. Välj **Spara**.

   > [!TIP]
   > Ge ditt kohort ett namn, t. ex. "engagerade användare (5 + dagar)". Spara den i "Mina rapporter" eller "delade rapporter", beroende på om du vill att andra personer som har åtkomst till den här Application Insights resursen för att se den här kohort.

6. Välj **tillbaka till galleriet**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Vad kan du göra med hjälp av den här kohort?

Öppna användar verktyget. I list rutan **Visa** väljer du den kohort som du skapade under **användare som tillhör**.

Nu filtreras användar verktyget till denna kohort av användare:

![Fönstret användare filtrerat till en viss kohort](./media/usage-cohorts/004.png)

Några viktiga saker att känna till:

* Du kan inte skapa den här uppsättningen via normala filter. Datum logiken är mer avancerad.
* Du kan filtrera den här kohort ytterligare genom att använda normala filter i användar verktyget. Så även om kohort har definierats i 28-dagars period kan du fortfarande ändra tidsintervallet i användar verktyget till 30, 60 eller 90 dagar.

Dessa filter har stöd för mer avancerade frågor som är omöjliga att uttrycka via Frågeverktyget. Ett exempel är _personer som var engagerade under de senaste 28 dagarna. Hur har de samma personer som beter sig över de senaste 60 dagarna?_

## <a name="example-events-cohort"></a>Exempel: Events kohort

Du kan också göra kohorter av händelser. I det här avsnittet definierar du en kohort för vyerna händelser och sidor. Sedan kan du se hur du använder dem från andra verktyg. Den här kohort kan definiera en uppsättning händelser som ditt team betraktar för _aktiv användning_ eller en uppsättning som är relaterad till en viss ny funktion.

1. Öppna verktyget kohorter.

2. Välj fliken **mallgalleriet** . Du ser en samling mallar för olika kohorter.

3. Välj **händelse väljare**.

    ![Skärm bild av händelse väljaren](./media/usage-cohorts/006.png)

4. I list rutan **aktiviteter** väljer du de händelser som du vill ska finnas i kohort.

5. Spara kohort och ge den ett namn.

## <a name="example-active-users-where-you-modify-a-query"></a>Exempel: aktiva användare där du ändrar en fråga

Föregående två kohorter definierades med list rutor. Men du kan också definiera kohorter med analys frågor för total flexibilitet. Om du vill se hur skapar du en kohort av användare från Storbritannien.

![Animerad bild går genom användning av kohorter-verktyget](./media/usage-cohorts/cohorts0001.gif)

1. Öppna kohorter-verktyget, Välj fliken **mall Galleri** och välj **tomma användare kohort**.

    ![Tomma användare kohort](./media/usage-cohorts/001.png)

    Det finns tre avsnitt:
   * Ett markdown text avsnitt där du beskriver kohort i mer detalj för andra i ditt team.

   * Avsnittet Parameters, där du gör egna parametrar, t. ex. **aktiviteter** och andra nedrullningsbara List rutor från föregående två exempel.

   * Avsnittet fråga där du definierar kohort med hjälp av en analys fråga.

     I avsnittet fråga [skriver du en analys fråga](/azure/kusto/query). Frågan väljer en viss uppsättning rader som beskriver de kohort som du vill definiera. Verktyget kohorter lägger sedan implicit till "| sammanfatta med user_Id-sats i frågan. Dessa data förhandsgranskas under frågan i en tabell, så du kan se till att frågan returnerar resultat.

     > [!NOTE]
     > Om du inte ser frågan kan du prova att ändra storlek på avsnittet för att göra det högre och Visa frågan. Den animerade. gif i början av det här avsnittet visar hur storleks ändringen fungerar.

2. Kopiera och klistra in följande text i Frågeredigeraren:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Välj **Kör fråga**. Om du inte ser användar-ID: n visas i tabellen ändrar du till ett land/en region där ditt program har användare.

4. Spara och namnge kohort.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

_Jag har definierat en kohort för användare från ett visst land/region. När jag jämför den här kohort i användar verktyget och bara anger ett filter för landet/regionen visas olika resultat. Varför?_

Kohorter och filter är olika. Anta att du har en kohort av användare från Storbritannien (definieras som i föregående exempel) och jämför dess resultat med att ställa in filtret "land eller region = Storbritannien".

* Kohort-versionen visar alla händelser från användare som har skickat en eller flera händelser från Storbritannien under det aktuella tidsintervallet. Om du delar efter land eller region ser du förmodligen många länder och regioner.
* Filter versionen visar bara händelser från Storbritannien. Men om du delar efter land eller region visas endast Storbritannien.

## <a name="learn-more"></a>Läs mer

* [Analytics-frågespråk](../logs/log-analytics-tutorial.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Användare, sessioner, händelser](usage-segmentation.md)
* [Användarflöden](usage-flows.md)
* [Översikt över användning](usage-overview.md)