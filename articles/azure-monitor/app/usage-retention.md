---
title: Analysera webb programs kvarhållning av användare med Azure Application insikter
description: Hur många användare kommer tillbaka till din app?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 37de27e8dc707133595b71b39e45bdec554289e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583381"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analys av användar kvarhållning för webb program med Application Insights

Med funktionen kvarhållning i [Azure Application insikter](./app-insights-overview.md) kan du analysera hur många användare som kommer tillbaka till din app och hur ofta de utför specifika uppgifter eller uppnå mål. Om du till exempel kör en spel webbplats kan du jämföra antalet användare som återvänder till webbplatsen efter att ha förlorat ett spel med det antal som returneras efter att ha vunnit. Den här kunskapen kan hjälpa dig att förbättra både din användar upplevelse och din affärs strategi.

## <a name="get-started"></a>Kom igång

Om du ännu inte ser data i kvarhållning verktyget i Application Insights Portal, lär du [dig hur du kommer igång med användnings verktygen](usage-overview.md).

## <a name="the-retention-tool"></a>Kvarhållning-verktyget

![Kvarhållningsverktyg](./media/usage-retention/retention.png)

1. I verktygsfältet kan användarna skapa nya bevarande rapporter, öppna befintliga bevarande rapporter, Spara aktuell kvarhållning eller Spara som, återställa ändringar som gjorts i sparade rapporter, uppdatera data i rapporten, dela rapporten via e-post eller Direct Link och komma åt dokumentations sidan. 
2. Som standard visar kvarhållning alla användare som gjorde något och sedan kom tillbaka och gjorde något mer under en viss period. Du kan välja olika kombinationer av händelser för att begränsa fokuseringen på specifika användar aktiviteter.
3. Lägg till ett eller flera filter för egenskaper. Du kan till exempel fokusera på användare i ett visst land eller en viss region. Klicka på **Uppdatera** när du har angett filtren. 
4. Det övergripande kvarhållning diagrammet visar en sammanfattning av användar kvarhållning för den valda tids perioden. 
5. Rutnätet visar antalet användare som behålls enligt Frågeverktyget i #2. Varje rad representerar en kohort användare som utförde en händelse under den tids period som visas. Varje cell i raden visar hur många av de kohort som returnerade minst en gång i en senare period. Vissa användare kan returnera mer än en period. 
6. Insikts korten visar de fem främsta inledande händelserna och de fem främsta returnerade händelserna för att ge användarna en bättre förståelse för deras kvarhållning rapport. 

![Mus hovring för kvarhållning](./media/usage-retention/hover.png)

Användare kan hovra över celler på kvarhållningsintervallet för att få åtkomst till analys knappen och verktygs tips som förklarar vad cellen innebär. Analytics-knappen tar användare till analys verktyget med en i förväg ifylld fråga för att generera användare från cellen. 

## <a name="use-business-events-to-track-retention"></a>Använd affärs händelser för att spåra kvarhållning

För att få den mest användbara bevarande analysen kan du mäta händelser som representerar betydande affärs aktiviteter. 

Många användare kan till exempel öppna en sida i din app utan att spela upp spelet som visas. Att bara spåra sid visningarna skulle därför ge en felaktig uppskattning av hur många personer som går tillbaka till att spela spelet efter att ha besökt det tidigare. För att få en tydlig bild av att returnera spelare, ska appen skicka en anpassad händelse när en användare faktiskt spelar.  

Det är en bra idé att koda anpassade händelser som representerar viktiga affärs åtgärder och använda dem för din bevarande analys. Om du vill avbilda spel resultatet måste du skriva en kodrad för att skicka en anpassad händelse till Application Insights. Om du skriver det i webb side koden eller i Node.JS, ser det ut så här:

```JavaScript
    appinsights.trackEvent("won game");
```

Eller i ASP.NET-Server kod:

```csharp
   telemetry.TrackEvent("won game");
```

[Läs mer om hur du skriver anpassade händelser](./api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Nästa steg
- Börja skicka [anpassade händelser](./api-custom-events-metrics.md#trackevent) eller [sid visningar](./api-custom-events-metrics.md#page-views)om du vill aktivera användnings upplevelser.
- Om du redan skickar anpassade händelser eller sid visningar, utforska användnings verktygen för att lära dig hur användarna använder tjänsten.
    - [Användare, sessioner, händelser](usage-segmentation.md)
    - [Trattar](usage-funnels.md)
    - [Användarflöden](usage-flows.md)
    - [Arbetsböcker](../visualize/workbooks-overview.md)
    - [Lägg till användar kontext](usage-send-user-context.md)

