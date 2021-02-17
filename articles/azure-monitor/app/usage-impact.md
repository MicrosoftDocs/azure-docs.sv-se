---
title: Användnings påverkan för Azure Application Insights | Microsoft-dokument
description: Analysera hur olika egenskaper kan påverka konverterings takten för delar av dina appar.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 51f938743d8da3c9a1dea546320662701e2b88c8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583527"
---
# <a name="impact-analysis-with-application-insights"></a>Effekt analys med Application Insights

Påverkan analyserar hur inläsnings tider och andra egenskaper påverkar konverterings takten för olika delar av din app. För att göra det mer exakt, upptäcks hur **någon dimension** i **vyn sid visning**, **Anpassad händelse** eller **begäran** påverkar användningen av en annan **sidvy** eller **Anpassad händelse**. 

![Verktyget effekt](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Är du fortfarande osäker på vilken påverkan det gör?

Ett sätt att tänka på påverkas är att det ultimata verktyget för att lösa argument med någon i ditt team om hur långsamma på en viss aspekt av din webbplats påverkar om användarna är fästa. Även om användarna kan tolerera en viss mängd långsamma effekter ger du bättre inblick i hur du kan balansera optimering och prestanda för att maximera användar konverteringen.

Men att analysera prestanda är bara en del av Påverkanens funktioner. Eftersom påverkan har stöd för anpassade händelser och dimensioner, är det bara några klick att svara på frågor som om hur ser ut som användarens webbläsare.

![Skärm bilds konvertering av webbläsare](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Din Application Insights-resurs måste innehålla sidvyer eller anpassade händelser för att kunna använda verktyget effekt. [Lär dig hur du konfigurerar din app för att samla in sid visningar automatiskt med Application Insights JavaScript SDK](./javascript.md). Tänk också på att eftersom du analyserar korrelation, exempel storlek.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Påverkar sid inläsnings tiden hur många personer som ska konvertera på min sida?

Om du vill börja besvara frågor med verktyget effekt väljer du en inledande sidvy, en anpassad händelse eller en begäran.

![Skärm bild som visar var du kan välja en inledande sidvy, anpassad händelse eller begäran.](./media/usage-impact/0002-dropdown.png)

1. Välj en sidvy från List rutan **för sid visning** .
2. Lämna **analysera hur** List rutan i standard valet av **varaktighet** (i den här kontextens **varaktighet** är ett alias för **sid inläsnings tid**.)
3. För att **påverkar användningen av** List rutan väljer du en anpassad händelse. Den här händelsen ska motsvara ett UI-element i sid visningen som du valde i steg 1.

![Skärm bild av resultat](./media/usage-impact/0003-results.png)

I denna instans som **produktens sid** inläsnings tid ökar konverterings hastigheten till **köp produkt som du klickar på** . Baserat på distributionen ovan kan en optimal sid inläsnings tid på 3,5 sekunder vara avsedd att uppnå en potentiell 55% konverterings takt. Ytterligare prestanda förbättringar för att minska inläsnings tiden under 3,5 sekunder motsvarar inte för närvarande ytterligare konverterings förmåner.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Vad händer om jag spårar sid visningar eller inläsnings tider på anpassade sätt?

Påverkan stöder både standard-och anpassade egenskaper och mått. Använd vad du vill. I stället för varaktighet använder du filter på de primära och sekundära händelserna för att få mer information.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Kan användare från olika länder eller regioner konverteras till olika priser?

1. Välj en sidvy från List rutan **för sid visning** .
2. Välj land eller region i **analysera hur dess** listruta
3. För att **påverkar användningen av** List rutan väljer du en anpassad händelse som motsvarar ett gränssnitts element i sid visningen som du valde i steg 1.

I det här fallet får resultaten inte längre till en kontinuerlig x-axel-modell som i det första exemplet. I stället presenteras en visualisering som liknar en segmenterad tratt. Sortera efter **användning** för att Visa variationen av konverteringen till din anpassade händelse baserat på land/region.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hur beräknar verktyget effekt dessa konverterings hastigheter?

Verktyget effekt är i taket beroende av [korrelationskoefficienten till Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Resultaten beräknas mellan-1 och 1 med-1 som representerar en negativ linjär korrelation och 1 som representerar en positiv linjär korrelation.

Den grundläggande fördelningen av hur påverkan analys fungerar är följande:

Låt _A_ = vyn huvud sid visning/anpassad händelse/begäran som du väljer i den första List rutan. (**För sid visning**).

Låt _B_ = den sekundära sid visningen/anpassade händelsen som du väljer (**påverkar användningen av**).

Påverkan ser ut som ett exempel på alla sessioner från användare i det valda tidsintervallet. För varje session söker den efter varje förekomst av _A_.

Sessioner delas sedan upp i två olika typer av _undersessioner_ baserat på ett av två villkor:

- En konverterad undersession består av en session som slutar med ett _B_ _-evenemang och omfattar alla händelser_ som inträffar före _B_.
- En avkonverterad undersession inträffar när alla _en_ inträffar utan en Terminal _B_.

Hur påverkan beräknas i slut ändan beroende på om vi analyserar av mått eller dimension. För mått är alla _en_ under session genomsnitts värde. För dimensioner är värdet för varje _A_ till _1/N_ till värdet som tilldelats _B_ där _N_ är numret på _en_ av undersessionerna.

## <a name="next-steps"></a>Nästa steg

- Börja skicka [anpassade händelser](./api-custom-events-metrics.md#trackevent) eller [sid visningar](./api-custom-events-metrics.md#page-views)om du vill aktivera användnings upplevelser.
- Om du redan skickar anpassade händelser eller sid visningar, utforska användnings verktygen för att lära dig hur användarna använder tjänsten.
    - [Trattar](usage-funnels.md)
    - [Kvarhållning](usage-retention.md)
    - [Användarflöden](usage-flows.md)
    - [Arbetsböcker](../visualize/workbooks-overview.md)
    - [Lägg till användar kontext](usage-send-user-context.md)

