---
title: Mobilitets tjänster (för hands version) data strukturer i Microsoft Azure Maps
description: Förstå hur data är ordnade i Metro-områden i Azure Maps Mobility Services (för hands version). Se vilka fält som lagrar information om offentliga överförings stopp och-rader.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904728"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Data strukturer i Azure Maps mobilitets tjänster (för hands version) 

> [!IMPORTANT]
> Azure Maps mobilitets tjänster finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



I den här artikeln beskrivs begreppet tunnelbane linje i [Azure Maps mobilitets tjänster](/rest/api/maps/mobility). Vi diskuterar några vanliga fält som returneras när den här tjänsten efter frågas för offentliga överförings stopp och-rader. Vi rekommenderar att du läser den här artikeln innan du utvecklar med mobilitets tjänstens API: er.

## <a name="metro-area"></a>Tunnelbane yta

Mobilitets tjänster (för hands version) är grupperade efter Metro-områden som stöds. Metro-områden följer inte stads gränser. Ett tunnelbane område kan innehålla flera städer, tätt ifylld stad och omgivande städer. Ett land/region kan i själva verket vara ett tunnelbane område. 

`metroID`Är ett Metro-områdens ID som kan användas för att anropa API: t för att [Hämta information om Metro-ytan](/rest/api/maps/mobility/getmetroareainfopreview). Använd Azure Maps "Get Metro"-API för att begära överförings typer, transit myndigheter, aktiva aviseringar och ytterligare information för den valda tunnelbane linje. Du kan också begära de tunnelbane områden och metroIDs som stöds. Metro-områdes-ID: n kan komma att ändras.

**metroID:** 522   **Namn:** Seattle-Tacoma-Bellevue

![Seattle-tunnelbane-Area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Stopp-ID

Överförings stopp kan refereras till av två typer av ID: n, General GFTS-ID: t [(transport feed Specification)](http://gtfs.org/) och Azure Maps stopp-ID. GFTS-ID kallas för stopKey och Azure Maps stopp-ID kallas stopID. När du ofta hänvisar till överförings stopp rekommenderar vi att du använder Azure Maps stopp-ID. stopID är mer stabil och förmodligen kvar så länge det fysiska stoppet finns. Stopp-ID: t för GTFS uppdateras oftare. Till exempel kan GTFS Stop ID uppdateras per GTFS Provider-begäran eller när en ny GTFS-version släpps. Även om det fysiska stoppet inte hade någon ändring, kan stopp-ID: t för GTFS ändras.

För att starta kan du begära att närliggande överföring slutar att använda [Get närbelägen överförings-API](/rest/api/maps/mobility/getnearbytransitpreview).

## <a name="line-groups-and-lines"></a>Linje grupper och linjer

Mobilitets tjänster (för hands version) använder en parallell data modell för linjer och linje grupper. Den här modellen används för att bättre hantera ändringar som ärvts från [GTFS](http://gtfs.org/) -vägar och TRIPs-data.


### <a name="line-groups"></a>Linje grupper

En linje grupp är en entitet som grupper samman alla rader som är logiskt indelade i samma grupp. Vanligt vis innehåller en linje grupp två rader, en från punkt A till B, och den andra som returnerar från punkt B till A. Båda raderna tillhör samma offentliga transport byrå och har samma rad nummer. Det kan dock finnas fall där en linje grupp har fler än två rader eller bara en enda rad i den.


### <a name="lines"></a>Rader

Som vi nämnt ovan består varje linje grupp av en uppsättning rader. Varje linje grupp består av två rader och varje rad beskriver en riktning.  Det finns dock fall där fler rader skapar en linje grupp. Det finns till exempel en rad som ibland deklarerar genom en viss plats och ibland inte. I båda fallen fungerar den under samma rad nummer. Dessutom kan en linje grupp bestå av en enda rad. En cirkulär linje med en enda riktning är en ling grupp med en rad.

För att börja kan du begära linje grupper med hjälp av [API: t get transport line](/rest/api/maps/mobility/gettransitlineinfopreview).


## <a name="next-steps"></a>Nästa steg

Lär dig hur du begär överförings data med mobilitets tjänster (för hands version):

> [!div class="nextstepaction"]
> [Så här begär du överförings data](how-to-request-transit-data.md)

Lär dig hur du begär real tids data med mobilitets tjänster (för hands version):

> [!div class="nextstepaction"]
> [Så här begär du real tids data](how-to-request-real-time-data.md)

Utforska API-dokumentationen för Azure Maps Mobility Services (för hands version)

> [!div class="nextstepaction"]
> [API-dokumentation för mobilitets tjänster](/rest/api/maps/mobility)