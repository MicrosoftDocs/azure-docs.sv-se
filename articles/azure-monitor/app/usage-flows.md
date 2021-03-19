---
title: Azure Application insikter Användarflöden analyserar navigerings flöden
description: Analysera hur användare navigerar mellan sidor och funktioner i din webbapp.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a31ab24b96fa44787d08801a0680f94ff98bb5a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87309277"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analysera användar navigerings mönster med Användarflöden i Application Insights

![Application Insights Användarflöden-verktyget](./media/usage-flows/flows.png)

Verktyget Användarflöden visualiserar hur användare navigerar mellan sidor och funktioner på din webbplats. Det är bra för att besvara frågor som:

* Hur kommer användarna att navigera bort från en sida på din webbplats?
* Vad kan användarna klicka på på en sida på din webbplats?
* Var finns de platser som användare omsättnings mest från din plats?
* Finns det platser där användarna upprepar samma åtgärd över och över?

Verktyget Användarflöden startar från en inledande sidvy, en anpassad händelse eller ett undantag som du anger. Med den här inledande händelsen visar Användarflöden händelser som inträffat tidigare och efteråt under användarsessioner. Linjer med varierande tjocklek visar hur många gånger varje sökväg följs av användare. Särskilda **sessioner startade** noder visar var efterföljande noder startade en session. Noder som **avslutas av sessionen** visar hur många användare som inte skickade några sid visningar eller anpassade händelser efter den föregående noden, som markerar var användarna förmodligen har lämnat platsen.

> [!NOTE]
> Din Application Insights-resurs måste innehålla sidvyer eller anpassade händelser för att använda Användarflöden-verktyget. [Lär dig hur du konfigurerar din app för att samla in sid visningar automatiskt med Application Insights JavaScript SDK](./javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Börja med att välja en inledande händelse

![Välj en inledande händelse för Användarflöden](./media/usage-flows/initial-event.png)

Om du vill börja besvara frågor med Användarflöden-verktyget väljer du en inledande sidvy, en anpassad händelse eller ett undantag som ska fungera som utgångs punkt för visualiseringen:

1. Klicka på länken i rubriken **vad gör användarna efter...?** eller klicka på knappen **Redigera** .
2. Välj en sidvy, en anpassad händelse eller ett undantag från den **inledande händelse** List rutan.
3. Klicka på **skapa graf**.

I kolumnen "steg 1" i visualiseringen visas vad användare oftast precis precis efter den inledande händelsen, ordnat längst ned från de flesta till minsta frekventa. I "steg 2" och efterföljande kolumner visas vad användare har gjort därefter, vilket skapar en bild av alla sätt som användare har navigerat på din webbplats.

Som standard samplar verktyget Användarflöden slumpmässigt bara de senaste 24 timmarna av sid visningar och anpassad händelse från din webbplats. Du kan öka tidsintervallet och ändra balansen för prestanda och exakthet för slumpmässig sampling på Redigera-menyn.

Om några av sid visningarna, anpassade händelser och undantag inte är relevanta för dig, klickar du på **X** på de noder som du vill dölja. När du har valt de noder som du vill dölja klickar du på knappen **skapa diagram** under visualiseringen. Om du vill se alla noder som du har dolt klickar du på knappen **Redigera** och tittar sedan på avsnittet **undantagna händelser** .

Om sidvyer eller anpassade händelser saknas som du förväntar dig att se i visualiseringen:

* Markera avsnittet **exkluderade händelser** på **Redigera** -menyn.
* Använd plus-knapparna på **andra** noder för att inkludera mindre frekventa händelser i visualiseringen.
* Om sid visningen eller den anpassade händelsen som du förväntar dig skickas sällan av användarna kan du prova att öka tidsintervallet för visualiseringen på **Redigera** -menyn.
* Se till att sid visning, anpassad händelse eller undantag som du förväntar dig har kon figurer ATS att samlas in av Application Insights SDK i käll koden för din webbplats. [Lär dig mer om att samla in anpassade händelser.](./api-custom-events-metrics.md)

Om du vill se fler steg i visualiseringen använder du de **föregående stegen** och list rutorna för **Nästa steg** ovanför visualiseringen.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>När du har besökt en sida eller funktion, var ska användarna gå och vad klickar de klickar på?

![Använd Användarflöden för att förstå var användarna klickar](./media/usage-flows/one-step.png)

Om din inledande händelse är en sidvy, är den första kolumnen ("steg 1") i visualiseringen ett snabbt sätt att förstå vad användarna gjorde direkt efter att ha besökt sidan. Försök att öppna din webbplats i ett fönster bredvid Användarflöden visualiseringen. Jämför dina förväntningar på hur användare interagerar med sidan i listan över händelser i kolumnen "steg 1". Ofta kan ett GRÄNSSNITTs element på sidan som verkar vara obetydligt för ditt team vara bland de mest använda på sidan. Det kan vara en bra utgångs punkt för design förbättringar av webbplatsen.

Om den första händelsen är en anpassad händelse visar den första kolumnen vad användare precis har gjort efter att åtgärden utfördes. Precis som med sidvyer bör du fundera över om det observerade beteendet för dina användare matchar ditt teams mål och förväntningar. Om den valda inledande händelsen är "tillagt objekt i shopping vagnen", kan du till exempel titta för att se om "gå till utcheckning" och "slutfört köp" visas i visualiseringen strax därefter. Om användar beteendet skiljer sig från dina förväntningar använder du visualiseringen för att förstå hur användare får "fångade" av din webbplats nuvarande design.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Var finns de platser som användare omsättnings mest från din plats?

Se efter om **sessionen avslutades** som visas högt i en kolumn i visualiseringen, särskilt tidigt i ett flöde. Det innebär att många användare som troligen är sammanslagna från din plats efter att ha gått till föregående sökväg till sidor och användar gränssnitts interaktioner Ibland förväntas omsättningen efter att ett köp har slutförts på en eCommerce-webbplats, till exempel, men vanligt vis är omsättningen ett tecken på design problem, dåliga prestanda eller andra problem med din webbplats som kan förbättras.

Tänk på att sessioner som **avslutas av sessionen** endast baseras på telemetri som samlas in av den här Application Insights resursen. Om Application Insights inte tar emot telemetri för vissa användar interaktioner kan användarna fortfarande ha interagerat med webbplatsen på dessa sätt när Användarflödens verktyget säger att sessionen är avslutad.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Finns det platser där användarna upprepar samma åtgärd över och över?

Leta efter en sid visning eller en anpassad händelse som upprepas av många användare i efterföljande steg i visualiseringen. Det innebär vanligt vis att användarna utför repetitiva åtgärder på din webbplats. Om du hittar upprepning bör du tänka på att ändra designen för din webbplats eller lägga till nya funktioner för att minska upprepningen. Du kan till exempel lägga till Mass redigerings funktioner om du hittar användare som utför repetitiva åtgärder på varje rad i ett tabell element.

## <a name="common-questions"></a>Vanliga frågor

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Representerar den inledande händelsen första gången händelsen visas i en session eller när som helst i en session?

Den första händelsen i visualiseringen representerar bara första gången som en användare skickade den sid visningen eller anpassade händelsen under en session. Om användarna kan skicka den första händelsen flera gånger i en session, visar kolumnen "steg 1" bara hur användare beter sig efter den *första* instansen av inledande händelse, inte alla instanser.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Några av noderna i min visualisering är för hög nivå. Till exempel en nod som bara säger "klickning". Hur kan jag dela upp det i mer detaljerade noder?

Använd alternativen **dela efter** på **Redigera** -menyn:

1. Välj den händelse som du vill dela upp i **händelse** menyn.
2. Välj en dimension på menyn **dimension** . Om du till exempel har en händelse med namnet "knapp klickning", provar du en anpassad egenskap med namnet "knapp namn".

## <a name="next-steps"></a>Nästa steg

* [Översikt över användning](usage-overview.md)
* [Användare, sessioner och händelser](usage-segmentation.md)
* [Kvarhållning](usage-retention.md)
* [Lägga till anpassade händelser i appen](./api-custom-events-metrics.md)

