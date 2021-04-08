---
title: Data modell för telemetri om begär Anden – Azure Application insikter
description: Application Insights data modell för telemetri för begäran
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 7a352f4ce3528d395599a91b53031c74b0873152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320569"
---
# <a name="request-telemetry-application-insights-data-model"></a>Begär telemetri: Application Insights data modell

Ett objekt för telemetri (i [Application Insights](./app-insights-overview.md)) representerar den logiska sekvensen för körning som utlöses av en extern begäran till ditt program. Varje begär ande körning identifieras av unika `ID` och `url` innehåller alla körnings parametrar. Du kan gruppera begär Anden efter logiska `name` och definiera `source` för denna begäran. Kod körning kan resultera i `success` eller `fail` och har en viss `duration` . Både lyckade och misslyckade körningar kan grupperas ytterligare av `resultCode` . Start tid för den begärda Telemetrin som definierats på kuvert nivån.

Telemetri för begär ande stöder standard utöknings modellen med hjälp av anpassade `properties` och `measurements` .

## <a name="name"></a>Name

Namnet på begäran representerar kod Sök vägen som krävs för att bearbeta begäran. Lågt kardinalitet för att tillåta en bättre gruppering av begär Anden. För HTTP-begäranden representerar den HTTP-metod och URL-sökväg som `GET /values/{id}` saknar det faktiska `id` värdet.

Application Insights Web SDK skickar namn för begäran "i befintligt skick" med avseende på brev fall. Gruppering i UI är Skift läges känsligt, så `GET /Home/Index` räknas separat från `GET /home/INDEX` även om det ofta resulterar i samma styrenhet och åtgärds körning. Orsaken till detta är att webb adresser i allmänhet är [SKIFT läges känsliga](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Du kanske vill se om alla `404` har hänt för URL: er skrivna med versaler. Du kan läsa mer om namn samling för begäran genom ASP.NET Web SDK i [blogg inlägget](https://apmtips.com/posts/2015-02-23-request-name-and-url/).

Maxlängd: 1024 tecken

## <a name="id"></a>ID

Identifierare för en begär ande anrops instans. Används för korrelation mellan begäran och andra telemetri-objekt. ID måste vara globalt unikt. Mer information finns på sidan [korrelation](./correlation.md) .

Maxlängd: 128 tecken

## <a name="url"></a>URL

Begär URL med alla parametrar för frågesträng.

Maxlängd: 2048 tecken

## <a name="source"></a>Källa

Källa för begäran. Exempel är Instrumentation-nyckeln för anroparen eller IP-adressen för anroparen. Mer information finns på sidan [korrelation](./correlation.md) .

Maxlängd: 1024 tecken

## <a name="duration"></a>Varaktighet

Varaktighet för begäran i formatet: `DD.HH:MM:SS.MMMMMM` . Måste vara positivt och mindre än `1000` dagar. Det här fältet är obligatoriskt som telemetri för begäran representerar åtgärden med början och slutet.

## <a name="response-code"></a>Svarskod

Resultat av en begär ande körning. HTTP-statuskod för HTTP-begäranden. Det kan vara `HRESULT` ett värde eller en undantags typ för andra typer av begär Anden.

Maxlängd: 1024 tecken

## <a name="success"></a>Klart

Indikering för lyckat eller misslyckat anrop. Det här fältet är obligatoriskt. Om det inte anges explicit till `false` -en begäran anses vara lyckad. Ange det här värdet till `false` om åtgärden avbröts av undantag eller returnerade fel resultat kod.

Application Insights definiera en begäran som slutförd när svars koden är mindre än eller lika med för webb programmen `400` `401` . Det finns dock fall då den här standard mappningen inte matchar programmets semantik. Svars koden `404` kan indikera "inga poster", som kan vara en del av ett vanligt flöde. Det kan också indikera en bruten länk. För brutna länkar kan du till och med implementera mer avancerad logik. Du kan bara markera brutna länkar som ett avbrott när dessa länkar finns på samma plats genom att analysera URL-referenten. Eller markera dem som felaktiga vid åtkomst från företagets mobil program. På samma sätt `301` och `302` indikerar att det inte går att komma åt från klienten som inte stöder omdirigering.

Delvis accepterat innehåll `206` kan tyda på ett problem med en övergripande begäran. Application Insights slut punkten tar till exempel emot en batch med telemetri-objekt som en enskild begäran. Den returnerar `206` när vissa objekt i gruppen inte bearbetades korrekt. Öknings takten för `206` indikerar ett problem som behöver undersökas. Liknande logik gäller för `207` flera status, där det kan vara sämsta separata svars koder.

Du kan läsa mer om resultat kod och status kod för begäran i [blogg inlägget](https://apmtips.com/posts/2016-12-03-request-success-and-response-code/).

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- [Skriv telemetri för anpassad begäran](./api-custom-events-metrics.md#trackrequest)
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- Lär dig hur du [konfigurerar ASP.net Core](./asp-net.md) program med Application Insights.
- Kolla ut [plattformar](./platforms.md) som stöds av Application Insights.

