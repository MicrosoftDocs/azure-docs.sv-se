---
title: Live händelse tillstånd och fakturering i Azure Media Services
description: Det här avsnittet ger en översikt över Azure Media Services Live händelse tillstånd och fakturering.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: c9fa12e1ee3778d0865c75662064bd4067e56d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897823"
---
# <a name="live-event-states-and-billing"></a>Live händelse tillstånd och fakturering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

I Azure Media Services börjar en Live-händelse att fakturera så fort dess tillstånd övergår till **körning** eller **vänte läge**. Du debiteras även om det inte finns någon video som går igenom tjänsten. Om du vill stoppa direkt sändningen från fakturering måste du stoppa Live-händelsen. Direkt avskrift debiteras på samma sätt som direkt sändningen.

När **LiveEventEncodingType** på din [Live-händelse](/rest/api/media/liveevents) är inställt på Standard eller Premium1080p stänger Media Services automatiskt av alla aktiva händelser som fortfarande är i **körnings** tillstånd 12 timmar efter att inmatnings flödet förloras och det inte finns någon **Live-utdata** som körs. Men du kommer fortfarande att faktureras för den tidpunkt då Live-händelsen var i **körnings** tillstånd.

> [!NOTE]
> Direkt sändnings händelser stängs inte av automatiskt och måste stoppas explicit genom API: et för att undvika överdriven fakturering.

## <a name="states"></a>Tillstånd

Live-händelsen kan vara i något av följande tillstånd.

|Stat|Beskrivning|
|---|---|
|**Stoppad**| Det här är start läget för Live-händelsen när den har skapats (om inte Autostart har angetts till true.) Ingen fakturering sker i det här läget. Det går inte att ta emot några inmatade händelser. |
|**Startar**| Direkt sändningen börjar och resurserna får ett allokerat värde. Ingen fakturering sker i det här läget.  Om ett fel inträffar återgår Live-händelsen till stoppat tillstånd.|
| **Allokera** | Tilldelnings åtgärden anropades för Live-händelsen och resurserna tillhandahålls för den här Live-händelsen. När den här åtgärden har slutförts övergår den aktiva händelsen över till vänte läge.
|**Läget**| Live Event-resurser har etablerats och är redo att startas. Fakturering sker i det här läget.  De flesta egenskaper kan fortfarande uppdateras, men inmatning eller strömning tillåts inte under det här läget.
|**Körs**| Live Event-resurserna har allokerats, inmatnings-och för hands-URL: er har genererats och kan ta emot Live-strömmar. Faktureringen är nu aktiv. Du måste uttryckligen anropa Stop Event-resursen för att stoppa ytterligare fakturering.|
|**Stoppas**| Direkt sändningen stoppas och resurserna etableras. Ingen fakturering sker i det här tillfälliga läget. |
|**Tas bort**| Live-händelsen tas bort. Ingen fakturering sker i det här tillfälliga läget. |

Du kan välja att aktivera Live-avskrifter när du skapar en Live-händelse. Om du gör det debiteras du för direkt avskrifter när Live-händelsen är i **körnings** tillstånd. Observera att du kommer att faktureras även om det inte finns något ljud som passerar direkt evenemanget.

## <a name="next-steps"></a>Nästa steg

- [Översikt över direkt uppspelning](live-streaming-overview.md)
- [Själv studie kurs om Live-direktuppspelning](stream-live-tutorial-with-api.md)
