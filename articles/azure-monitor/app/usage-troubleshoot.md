---
title: Felsöka användar analys verktyg – Azure Application insikter
description: Fel söknings guide – analysera webbplats-och program användning med Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a1d77016fdf94de4fdd574b0d4cbd22d6b0b8490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024752"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Felsöka verktyg för användar beteende analys i Application Insights
Har du frågor om [verktyg för användar beteende analys i Application Insights](usage-overview.md): [användare, sessioner, händelser](usage-segmentation.md), [trattar](usage-funnels.md), [användarflöden](usage-flows.md), [kvarhållning](usage-retention.md)eller kohorter? Här är några svar.

## <a name="counting-users"></a>Inventering av användare
**Analys verktygen för användar beteende visar att min app har en användare/session, men jag vet att min app har många användare/sessioner. Hur kan jag åtgärda dessa felaktiga antal?**

Alla telemetri-händelser i Application Insights ha ett [anonymt användar-ID](./data-model-context.md) och ett [sessions-ID](./data-model-context.md) som två av deras standard egenskaper. Som standard räknar alla användnings analys verktyg användare och sessioner baserat på dessa ID: n. Om dessa standard egenskaper inte fylls med unika ID: n för varje användare och session av appen, ser du ett felaktigt antal användare och sessioner i verktygen för användnings analys.

Om du övervakar en webbapp är den enklaste lösningen att lägga till [Application Insights JavaScript SDK](./javascript.md) till din app, och se till att skript kodfragmentet läses in på varje sida som du vill övervaka. Java Script SDK genererar automatiskt anonyma användar-och sessions-ID: n och fyller sedan i telemetri-händelser med dessa ID: n när de skickas från din app.

Om du övervakar en webb tjänst (inget användar gränssnitt) skapar du [en telemetri-initierare som fyller i egenskaperna anonym användar-ID och sessions-ID](./usage-overview.md) enligt din tjänsts begrepp för unika användare och sessioner.

Om din app skickar [autentiserade användar-ID: n](./api-custom-events-metrics.md#authenticated-users)kan du räkna utifrån autentiserade användar-ID: n i användar verktyget. Välj "autentiserade användare" i list rutan "Visa".

Analys verktygen för användar beteende stöder för närvarande inte räkning av användare eller sessioner baserat på andra egenskaper än anonymt användar-ID, autentiserat användar-ID eller sessions-ID.

## <a name="naming-events"></a>Namnge händelser
**Min app har tusentals olika sid visning och anpassade händelse namn. Det är svårt att skilja mellan dem och analys verktygen för användar beteende svarar ofta inte. Hur kan jag åtgärda dessa namngivnings problem?**

Sid visning och anpassade händelse namn används i analys verktygen för användar beteende. Att namnge händelser är mycket viktigt för att få värde från dessa verktyg. Målet är ett balans mellan att ha för få, alltför generiska namn ("knapp klickningar") och att ha för många, överliggande namn ("Redigera knapp klickade på http: \/ /www.contoso.com/index").

Om du vill göra ändringar i sid visningen och de anpassade händelse namnen som appen skickas, måste du ändra appens källkod och omdistribuera. **Alla telemetridata i Application Insights lagras i 90 dagar och kan inte tas bort**, så ändringar som du gör i händelse namn tar 90 dagar till fullständigt manifest. För 90 dagar efter att namnet ändras visas både de gamla och nya händelse namnen i din telemetri, så du kan justera frågor och kommunicera i dina team.

Om din app skickar för många sid visnings namn kontrollerar du om de här sid visnings namnen har angetts manuellt i koden eller om de skickas automatiskt av Application Insights Java Script SDK:

* Om sid visnings namnen anges manuellt i kod med hjälp av [ `trackPageView` API: et](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), ändra namnet till mindre specifik. Undvik vanliga misstag som att placera URL: en i namnet på sid visningen. Använd i stället URL-parametern för `trackPageView` API: et. Flytta övrig information från sid visnings namnet till anpassade egenskaper.

* Om Application Insights JavaScript SDK skickar automatiskt sid visnings namn, kan du antingen ändra sidornas titlar eller växla till att skicka sid visnings namn manuellt. SDK: n skickar [rubriken](https://developer.mozilla.org/docs/Web/HTML/Element/title) för varje sida som ett sid visnings namn som standard. Du kan ändra att rubrikerna är mer generella, men vara mindfula för SEO och andra effekter som den här ändringen kan ha. Om du anger namn på sid visningar manuellt med `trackPageView` API: t åsidosätter de automatiskt insamlade namnen, så du kan skicka fler allmänna namn i telemetri utan att ändra sid rubrikerna.   

Om din app skickar för många anpassade händelse namn ändrar du namnet i koden till mindre. Undvik att lägga till URL: er och annan per sida eller dynamisk information i anpassade händelse namn direkt. Flytta i stället informationen till anpassade egenskaper för den anpassade händelsen med API: `trackEvent` et. I stället för `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` föreslår vi exempelvis något som `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` .

## <a name="next-steps"></a>Nästa steg

* [Översikt över användar beteende analys verktyg](usage-overview.md)

## <a name="get-help"></a>Få hjälp
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)