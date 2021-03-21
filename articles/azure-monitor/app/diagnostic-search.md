---
title: Använda search i Azure Application Insights | Microsoft Docs
description: Sök och filtrera RAW-telemetri som skickas av din webbapp.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: f7c2229a2e497fb052db79d61fcfeff140897798
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579545"
---
# <a name="using-search-in-application-insights"></a>Använda Sök i Application Insights

Sök är en funktion i [Application Insights](./app-insights-overview.md) som du använder för att söka efter och utforska enskilda telemetri objekt, till exempel sidvyer, undantag eller webb förfrågningar. Du kan också Visa logg spårningar och händelser som du har kodat.

(Om du vill ha mer komplexa frågor över dina data använder du [Analytics](../logs/log-analytics-tutorial.md).)

## <a name="where-do-you-see-search"></a>Var ser du sökningen?

### <a name="in-the-azure-portal"></a>I Azure-portalen

Du kan öppna diagnostisk sökning från fliken Application Insights översikt i ditt program (finns i det övre fältet) eller under undersöka till vänster.

![Fliken Sök](./media/diagnostic-search/view-custom-events.png)

Gå till den nedrullningsbara menyn händelse typer om du vill se en lista över telemetri-objekt – server begär Anden, sid visningar, anpassade händelser som du har kodat och så vidare. Längst upp i listan resultat är ett sammanfattnings diagram som visar antalet händelser över tid.

Klicka på från den nedrullningsbara menyn eller uppdatera för att hämta nya händelser.

### <a name="in-visual-studio"></a>I Visual Studio 2013

I Visual Studio finns det också ett Application Insights Sök fönster. Det är mest användbart när du vill visa telemetri-händelser som genererats av det program som du felsöker. Men det kan också visa de händelser som samlas in från den publicerade appen på Azure Portal.

Öppna Sök-fönstret i Visual Studio:

![Visual Studio Open Application Insights-sökning](./media/diagnostic-search/32.png)

Sök fönstret innehåller funktioner som liknar webb portalen:

![Fönstret Sök i Visual Studio Application Insights](./media/diagnostic-search/34.png)

Fliken spåra åtgärd är tillgänglig när du öppnar en begäran eller sid visning. En "åtgärd" är en sekvens med händelser som associeras med till en enskild begäran eller sid visning. Till exempel kan beroende anrop, undantag, spårnings loggar och anpassade händelser ingå i en enda åtgärd. På fliken spåra åtgärd visas grafiskt tid och varaktighet för dessa händelser i förhållande till begäran eller sid visning.

## <a name="inspect-individual-items"></a>Granska enskilda objekt

Markera ett objekt för telemetri för att se nyckel fält och relaterade objekt.

![Skärm bild av en enskild beroende förfrågan](./media/diagnostic-search/telemetry-item.png)

Då startas vyn transaktions detaljer från slut punkt till slut punkt.

## <a name="filter-event-types"></a>Filtrera händelse typer

Öppna den nedrullningsbara menyn händelse typer och välj de händelse typer som du vill se. (Om du senare vill återställa filtren klickar du på Återställ.)

Händelse typerna är:

* **Spåra**  -  [Diagnostikloggar](./asp-net-trace-logs.md) , inklusive TrackTrace, Log4Net, NLog och system. Diagnostics. trace-anrop.
* **Begär** HTTP-förfrågningar som tagits emot av serverprogrammet, inklusive sidor, skript, bilder, formatfiler och data. Dessa händelser används för att skapa översikts diagram över förfrågningar och svar.
* **Sid visning**  -  [Telemetri som skickas av webb klienten](./javascript.md), används för att skapa sid visnings rapporter.
* **Anpassad händelse** – om du infogade anrop till TrackEvent () för att kunna [övervaka användningen](./api-custom-events-metrics.md)kan du söka efter dem här.
* **Undantag** – ej fångade [undantag på servern](./asp-net-exceptions.md)och de som du loggar med hjälp av TrackException ().
* **Beroende**  -  [Anropar från serverprogrammet](./asp-net-dependencies.md) till andra tjänster som REST-API: er eller databaser och AJAX-anrop från din [klient kod](./javascript.md).
* **Tillgänglighet** – resultat av [tillgänglighets test](./monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrera efter egenskaps värden

Du kan filtrera händelser på värdena för egenskaperna. Vilka egenskaper som är tillgängliga beror på vilka händelse typer du har valt. Klicka på filter ikonen ![Filter ikon](./media/diagnostic-search/filter-icon.png) för att starta.

Om du väljer Nej värden för en viss egenskap har samma resultat som att välja alla värden. Den stänger av filtreringen av egenskapen.

Observera att antalet till höger om filter värdena visar hur många förekomster som finns i den aktuella filtrerade uppsättningen.

## <a name="find-events-with-the-same-property"></a>Hitta händelser med samma egenskap

Om du vill hitta alla objekt med samma egenskaps värde kan du antingen skriva det i Sök fältet eller klicka på kryss rutan när du tittar på Egenskaper på fliken filter.

![Klicka på kryss rutan för en egenskap på fliken filter](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Sök i data

> [!NOTE]
> Om du vill skriva mer komplexa frågor öppnar du [**loggar (analys)**](../logs/log-analytics-tutorial.md) från början av bladet Sök.
>

Du kan söka efter villkor i alla egenskaps värden. Detta är användbart om du har skrivit [anpassade händelser](./api-custom-events-metrics.md) med egenskaps värden.

Du kanske vill ange ett tidsintervall, eftersom sökningar över ett kortare intervall är snabbare.

![Öppna diagnostisk sökning](./media/diagnostic-search/search-property.png)

Sök efter fullständiga ord, inte del strängar. Använd citat tecken för att omsluta specialtecken.

| Sträng | Hittades *inte* | Hittade |
| --- | --- | --- |
| HomeController. om |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|USA|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Här följer de Sök uttryck som du kan använda:

| Exempelfråga | Effekt |
| --- | --- |
| `apple` |Sök efter alla händelser inom tidsintervallet vars fält innehåller ordet "Apple" |
| `apple AND banana` <br/>`apple banana` |Hitta händelser som innehåller båda orden. Använd versal "och", inte "och". <br/>Kort form. |
| `apple OR banana` |Hitta händelser som innehåller något av orden. Använd "eller", not "eller". |
| `apple NOT banana` |Hitta händelser som innehåller ett ord men inte det andra. |

## <a name="sampling"></a>Samling

Om din app genererar en stor mängd telemetri (och du använder ASP.NET SDK-version 2.0.0-beta3 eller senare), minskar den anpassningsbara samplings-modulen automatiskt volymen som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som är relaterade till samma begäran markeras eller avmarkeras som en grupp, så att du kan navigera mellan relaterade händelser.

[Lär dig mer om insamling](./sampling.md).

## <a name="create-work-item"></a>Skapa arbets objekt

Du kan skapa en bugg i GitHub-eller Azure-DevOps med information från alla telemetri objekt.

Gå till vyn transaktions information från slut punkt till slut punkt genom att klicka på ett objekt för telemetri och välj sedan **skapa arbets objekt**.

![Klicka på nytt arbets objekt, redigera fälten och klicka sedan på OK.](./media/diagnostic-search/work-item.png)

Första gången du gör detta uppmanas du att konfigurera en länk till din Azure DevOps-organisation och-projekt.

(Du kan också konfigurera länken på fliken arbets uppgifter.)

## <a name="send-more-telemetry-to-application-insights"></a>Skicka fler telemetri till Application Insights

Förutom den färdiga telemetri som skickas av Application Insights SDK kan du:

* Avbilda logg spårningar från ditt favorit loggnings ramverk i [.net](./asp-net-trace-logs.md) eller [Java](./java-trace-logs.md). Det innebär att du kan söka igenom dina logg spårningar och korrelera dem med sidvyer, undantag och andra händelser.
* [Skriv kod](./api-custom-events-metrics.md) för att skicka anpassade händelser, sid visningar och undantag.

[Lär dig hur du skickar loggar och anpassad telemetri till Application Insights](./asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>F & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Hur mycket data behålls?

Se [Sammanfattning av gränser](./pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hur kan jag se POST-data i mina server förfrågningar?

Vi loggar inte in POST-data automatiskt, men du kan använda [TrackTrace-eller log-anrop](./asp-net-trace-logs.md). Lägg till POST-data i meddelande parametern. Det går inte att filtrera på meddelandet på samma sätt som du kan filtrera efter egenskaper, men storleks gränsen är längre.

## <a name="next-steps"></a><a name="add"></a>Nästa steg

* [Skriv komplexa frågor i analys](../logs/log-analytics-tutorial.md)
* [Skicka loggar och anpassad telemetri till Application Insights](./asp-net-trace-logs.md)
* [Ställ in tillgänglighets-och svars tider](./monitor-web-app-availability.md)
* [Felsökning](../faq.md)