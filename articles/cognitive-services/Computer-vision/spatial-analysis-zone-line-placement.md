---
title: Zon och linje placering för rums analys
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar zoner och rader med Spatial analys
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90941677"
---
# <a name="zone-and-line-placement-guide"></a>Guide för zon-och linje placering

Den här artikeln innehåller rikt linjer för hur du definierar zoner och rader för avstånds analys åtgärder för att uppnå en korrekt analys av folk rörelser i ett utrymme. Detta gäller för alla åtgärder. 

Zoner och rader definieras med hjälp av JSON SPACEANALYSIS_CONFIG-parametern. Mer information finns i artikeln om [spatial analys åtgärder](spatial-analysis-operations.md) .

## <a name="guidelines-for-drawing-zones"></a>Rikt linjer för rit zoner

Kom ihåg att alla blank steg är olika. Du måste uppdatera position eller storlek beroende på dina behov.

Om du vill se ett särskilt avsnitt i din kameravy, skapar du den största zonen som du kan använda för det specificerade golv området som du är intresse rad av, men inte inklusive andra områden som du inte är intresse rad av. Detta ökar noggrannheten hos de data som samlas in och förhindrar falska positiva identifieringar från områden som du inte vill spåra. Var försiktig när du placerar hörnen i polygonen och se till att de inte är utanför det området som du vill spåra.  

### <a name="example-of-a-well-shaped-zone"></a>Exempel på en väl formad zon

Zonen bör vara tillräckligt stor för att kunna hantera tre personer i varje gräns och fokuseras på intresse området. Rums analys identifierar personer vars fötter placeras i zonen, så när du ritar zoner på 2D-bilden ska du föreställa zonen som en golv läggning på golvet.

![Välformad zon](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Exempel på zoner som inte är väl formade

I följande exempel visas dåligt formade zoner. I de här exemplen är det område av intresse som är det sista steget i *spel tids* visningen.

**Zonen finns inte på golvet.**

![Dåligt formad zon](./media/spatial-analysis/zone-not-on-floor.png) 

**Zonen är för liten.**

![zonen är för liten](./media/spatial-analysis/zone-too-small.png)

**Zonen fångar inte hela området runt visningen.**

![zonen fångar inte helt området runt slut punkten](./media/spatial-analysis/zone-bad-capture.png)

**Zonen är för nära kanten på kamera bilden och avbildar inte rätt visning.**

![zonen är för nära kanten på kamera bilden och avbildar inte rätt visning](./media/spatial-analysis/zone-edge.png)

**Zonen är delvis blockerad av hyllan, så att människor och golv inte syns helt.**

![zonen är delvis blockerad, så att människor inte är helt synliga](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Exempel på en välformad linje

Raden bör vara tillräckligt lång för att rymma hela ingången. Rums analys identifierar personer vars fötter korsar linjen, så när du ritar linjer på 2D-bilden är det dags att du ritar dem som om de ligger på golvet. 

Om möjligt utökar du linjen som är större än den faktiska ingången. Om detta inte kommer att resultera i extra korsningar (som i bilden nedan när linjen är mot en vägg) utökar du det.

![Välformad linje](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Exempel på rader som inte är väl formade

I följande exempel visas dåligt definierade rader.

**Linjen täcker inte hela post sättet på golvet.**

![Linjen täcker inte hela post sättet på golvet](./media/spatial-analysis/zone-line-bad-coverage.png)

**Linjen är för hög och tar inte upp hela dörren.**

![Linjen är för hög och tar inte upp hela dörren](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Nästa steg

* [Distribuera en webb program inventering](spatial-analysis-web-app.md)
* [Konfigurera åtgärder för rums analys](./spatial-analysis-operations.md)
* [Loggning och felsökning](spatial-analysis-logging.md)
* [Guide för kamera placering](spatial-analysis-camera-placement.md)
