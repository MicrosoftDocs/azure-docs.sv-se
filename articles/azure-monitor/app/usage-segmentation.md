---
title: Användare, session och händelse analys i Azure Application insikter
description: Demografisk analys av användare i din webbapp.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6eb91734afac81e103cebea48865793fa687ad71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024837"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Användare, sessioner och händelse analys i Application Insights

Ta reda på när användarna använder din webbapp, vilka sidor de är mest intresserade av, var dina användare finns och vilka webbläsare och operativ system de använder. Analysera affärs-och användnings telemetri med hjälp av [Azure Application insikter](./app-insights-overview.md).

![Skärm bild av Application Insights användare](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Kom igång

Om du ännu inte ser data i bladet användare, sessioner eller händelser i Application Insights-portalen, lär du [dig hur du kommer igång med användnings verktygen](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Verktyget användare, sessioner och events-segmentering

Tre av användnings bladen använder samma verktyg för att segmentera och tärning från din webbapp från tre perspektiv. Genom att filtrera och dela data kan du få insikter om den relativa användningen av olika sidor och funktioner.

* **Användar verktyg**: hur många personer som använder din app och dess funktioner.  Användare räknas genom att använda anonyma ID: n som lagras i webbläsarens cookies. En enskild person som använder olika webbläsare eller datorer räknas som mer än en användare.
* **Verktyg för sessioner**: hur många sessioner av användar aktivitet som har inkluderat vissa sidor och funktioner i din app. En session räknas efter en halvtimme av inaktivitet i användaren eller efter 24 timmar kontinuerlig användning.
* **Events-verktyg**: hur ofta vissa sidor och funktioner i din app används. En sid visning räknas när en webbläsare läser in en sida från din app, förutsatt att du har [instrumenterat den](./javascript.md). 

    En anpassad händelse representerar en förekomst av något som händer i din app, ofta en användar interaktion som en knapp för att klicka eller slutföra en uppgift. Du kan infoga kod i din app för att [skapa anpassade händelser](./api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Fråga efter vissa användare

Utforska olika användar grupper genom att justera frågealternativen överst i användar verktyget:

* Visa: Välj en kohort som användarna kan analysera.
* Används: Välj anpassade händelser och sid visningar.
* Under: Välj ett tidsintervall.
* Av: Välj hur data ska buckas, antingen en viss tids period eller en annan egenskap, till exempel webbläsare eller stad.
* Dela efter: Välj en egenskap som du vill dela eller segmentera data med. 
* Lägg till filter: begränsa frågan till vissa användare, sessioner eller händelser baserat på deras egenskaper, t. ex. webbläsare eller stad. 
 
## <a name="saving-and-sharing-reports"></a>Spara och dela rapporter 
Du kan spara användar rapporter, antingen privat precis i avsnittet Mina rapporter eller delas med alla andra med åtkomst till den här Application Insights resursen i avsnittet delade rapporter.

Dela en länk till en användare, session eller händelse rapport; Klicka på **dela** i verktygsfältet och kopiera sedan länken.

Dela en kopia av data i en användare, session eller händelse rapport; Klicka på **dela** i verktygsfältet och klicka sedan på **Word-ikonen** för att skapa ett Word-dokument med data. Du kan också klicka på **ordets ikon** ovanför huvud diagrammet.

## <a name="meet-your-users"></a>Träffa dina användare

Avsnittet om att **möta dina användare** visar information om fem exempel användare som matchar den aktuella frågan. Att beakta och utforska beteendet för enskilda personer, förutom agg regeringar, kan ge insikter om hur människor faktiskt använder din app.

## <a name="next-steps"></a>Nästa steg

- Börja skicka [anpassade händelser](./api-custom-events-metrics.md#trackevent) eller [sid visningar](./api-custom-events-metrics.md#page-views)om du vill aktivera användnings upplevelser.
- Om du redan skickar anpassade händelser eller sid visningar, utforska användnings verktygen för att lära dig hur användarna använder tjänsten.
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](usage-retention.md)
    - [Användarflöden](usage-flows.md)
    - [Arbetsböcker](../visualize/workbooks-overview.md)
    - [Lägg till användar kontext](./usage-overview.md)