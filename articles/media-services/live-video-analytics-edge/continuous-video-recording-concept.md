---
title: Kontinuerlig video inspelning – Azure
description: Kontinuerlig videoinspelning (CVR) avser processen att kontinuerligt spela in videon från en video källa. I det här avsnittet beskrivs vad CVR är.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453758"
---
# <a name="continuous-video-recording"></a>Kontinuerlig videoinspelning  

## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

* [Media diagram-koncept](media-graph-concept.md)
* [Video inspelnings koncept](video-recording-concept.md)

## <a name="overview"></a>Översikt

Kontinuerlig videoinspelning (CVR) avser processen att kontinuerligt spela in videon från en video källa. Video analys på IoT Edge har stöd för att spela in video kontinuerligt, dygnet runt, från en CCTV-kamera via ett [medie diagram](media-graph-concept.md) som består av en RTSP-källmapp och en nod för till gångs mottagare. Diagrammet nedan visar en grafisk representation av ett sådant medie diagram. JSON-representationen av [diagram sto pol Ogin](media-graph-concept.md#media-graph-topologies-and-instances) för ett sådant medie diagram finns [här](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Kontinuerlig videoinspelning":::

Medie diagrammet som illustreras ovan kan köras på en Edge-enhet, med till gångs mottagaren som registrerar videon till en Azure Media Services [till gång](terminology.md#asset). Videon spelas in så länge medie diagrammet är i aktiverat läge. Eftersom video spelas in som en till gång, kan den spelas upp med befintliga strömmande funktioner i Media Services. Se [uppspelning av inspelat innehåll](video-playback-concept.md) för mer information.

## <a name="resilient-recording"></a>Elastisk inspelning

Real tids analys på IoT Edge stöder drift under mindre än perfekt nätverks förhållanden, där gräns enheten ibland kan förlora anslutningen till molnet eller uppleva en minskning av den tillgängliga bandbredden. För att kunna redovisa detta registreras videon från källan lokalt i en cache och synkroniseras automatiskt med till gången med regelbunden bas. Om du undersöker [Graph-topologins JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)visas följande egenskaper:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

De två sistnämnda egenskaperna är relevanta för elastisk registrering (båda är också nödvändiga egenskaper för en nod för till gångs mottagare). Egenskapen localMediaCachePath anger att till gångs mottagare ska använda den mappsökvägen för att cachelagra medie data innan de överförs till till gången. Du kan se [den här](../../iot-edge/how-to-access-host-storage-from-module.md) artikeln för att förstå hur Edge-modulen kan använda enhetens lokala lagring. Egenskapen localMediaCacheMaximumSizeMiB definierar hur mycket disk utrymme som till gångs mottagaren kan använda som cache (1 MiB = 1024 * 1024 byte). 

Om din Edge-modul förlorar anslutningen under en mycket lång tid och innehållet som lagras i cache-mappen når localMediaCacheMaximumSizeMiB-värdet, kommer till gångs mottagaren att börja ta bort data från cachen, med början från de äldsta data. Om till exempel enheten tappade anslutningen på 10 och cachen träffar Max gränsen på. 18:00 börjar till gångs mottagaren ta bort data som registrerats på 10. 

När nätverks anslutningen har återställts kommer till gångs mottagaren att börja överföra från cachen och sedan starta från de äldsta data. I exemplet ovan skulle det vara fem minuter för videon som måste tas bort från cachen vid tidpunkten då anslutningen återställdes (t. ex. 6:02PM) kommer till gångs mottagaren att börja laddas upp från 10:05AM-märket.

Om du senare granskar till gången med [dessa](playback-recordings-how-to.md) API: er ser du att det finns ett lucka i till gången från cirka 10 till 10:05AM.

## <a name="segmented-recording"></a>Segmenterad inspelning  

Som vi nämnt ovan registrerar noden till gångs mottagare video till en lokal cache och laddar regelbundet upp videon till molnet. Egenskapen segmentLength (visas i avsnittet ovan) hjälper dig att kontrol lera kostnaden för Skriv transaktioner som är kopplad till att skriva data till ditt lagrings konto där till gången registreras. Om du till exempel ökar överförings perioden från 30 sekunder till 5 minuter, kommer antalet lagrings transaktioner att släppas med en faktor på 10 (5 * 60/30).

Egenskapen segmentLength säkerställer att Edge-modulen laddar upp video högst en gång per segmentLength sekunder. Den här egenskapen har ett minsta värde på 30 sekunder (även standard) och kan ökas med 30 sekunders steg till högst 5 minuter.

> [!NOTE]
> I artikeln [uppspelnings inspelningar](playback-recordings-how-to.md) får du en funktion som segmentLength har på uppspelning.

## <a name="see-also"></a>Se även

* [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)
* [Uppspelning av inspelat innehåll](video-playback-concept.md)

## <a name="next-steps"></a>Nästa steg

[Självstudie: kontinuerlig videoinspelning](continuous-video-recording-tutorial.md)
