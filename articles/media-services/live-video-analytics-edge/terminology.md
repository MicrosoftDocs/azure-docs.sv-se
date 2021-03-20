---
title: Live video analys på IoT EDG-terminologi – Azure
description: Den här artikeln innehåller en översikt över video analys på IoT Edge terminologi.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88690620"
---
# <a name="terminology"></a>Terminologi

Den här artikeln innehåller en översikt över terminologin som rör [live video analys på IoT Edge](overview.md).

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services är en plattform för moln mediet som gör det möjligt att bygga medie lösningar. Du kan läsa mer om det i [Azure Media Services](../latest/media-services-overview.md) -dokumentationen.

## <a name="asset"></a>Tillgång

[Till gång](../latest/assets-concept.md) är en entitet i Azure Media Services som mappar till en BLOB-behållare i Azure Storage-kontot som är kopplat till ett Media Services konto. Alla filer som är kopplade till en till gång lagras som blobbar i den behållaren medan Media Services innehåller metadata (till exempel ett namn, beskrivning, skapande tid) som är kopplat till till gången.

Live video analys på IoT Edge kan skapa till gångar och/eller lägga till data till befintliga till gångar. På så sätt kan du använda kontinuerliga och händelsebaserade videoinspelningar och uppspelningar (med videoinspelning på gräns enheten, spela in till Azure Media Services och uppspelning via befintliga Azure Media Services strömnings funktioner).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) är ett språk oberoende, högpresterande RPC-ramverk (Remote Procedure Call) med höga prestanda. Den använder sessionsbaserade strukturerade scheman via [protokoll buffer 3](https://developers.google.com/protocol-buffers/docs/proto3) som sitt underliggande meddelande utbytes format för kommunikation.

## <a name="media-graph"></a>Mediegraf

Med [medie diagram](media-graph-concept.md) kan du definiera var media ska samlas in, hur de ska bearbetas och var resultaten ska levereras. Det gör att du kan definiera en graf som består av källor, processorer och Sink-noder och därmed ge dig möjlighet att bygga Real video analys program. Medie diagram beskrivs i detalj på sidan för medie Graph-koncept.

## <a name="recording"></a>Inspelning

I samband med ett video hanterings system för säkerhets kameror refererar videoinspelningen till processen för att hämta video från kameror och lagra den i en fil (eller filer) för efterföljande visning via mobil-och webbläsarbaserade appar. Videoinspelning kan delas in i [kontinuerlig video inspelning](continuous-video-recording-concept.md) och [händelsebaserade videoinspelningar](event-based-video-recording-concept.md). Dessa beskrivs mer ingående på sidan för [video inspelnings](video-recording-concept.md) begrepp.

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) avser Real-Time strömnings protokoll. Det är ett program nivå protokoll för kontroll över leverans av data med egenskaper i real tid. RTSP tillhandahåller ett utöknings Bart ramverk för att aktivera kontrollerad leverans av real tids data på begäran, till exempel ljud och video. 

## <a name="streaming"></a>Strömning

Om du har tittat på video på en mobil enhet från tjänster som Netflix, YouTube och andra, har du erfaren strömmande video. Uppspelningen börjar strax efter att du har nått "Play" (om du har tillräckligt med bandbredd) och du kan söka fram och tillbaka längs videons tids linje. Med strömning är tanken att leverera endast den del av videon som bevakas och att låta visnings programmet spela upp videon medan data fortfarande överförs från en server till uppspelnings klienten. I samband med Azure Media Services avser [streaming](https://en.wikipedia.org/wiki/Streaming_media) processen att leverera medier från [Azure Media Services](../azure-media-player/azure-media-player-overview.md) till en strömmande klient (till exempel Azure Media Player). Du kan använda Azure Media Services för att strömma video till klienter som använder branschstandardiserade HTTP-baserade medie strömnings protokoll som [http Live Streaming (HLS)](https://developer.apple.com/streaming/) och [MPEG-streck](https://dashif.org/about/). HLS stöds av Azure Media Player och Web-spelarna som [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Googles Shaka Player](https://github.com/google/shaka-player), eller så kan du återge internt i mobilappar med Android: s [Exoplayer](https://github.com/google/ExoPlayer) och iOS [av Foundation](https://developer.apple.com/av-foundation/). MPEG-bindestreck stöds också av Azure Media Player, [hittar du en lista över klienter på den här sidan](https://dashif.org/clients/). 

Genom att använda [Media Graph](#media-graph)s för att spela in videor till en till gång i Azure Media Services kan du använda Media Services streaming-kapacitet för att leverera video STRÖMMAR i HLS och bindestreck. Du kan läsa mer om det i artikeln [video uppspelning](video-playback-concept.md) .

## <a name="vms"></a>VMS

[Virtuella datorer](https://en.wikipedia.org/wiki/Video_management_system) refererar till ett video hanterings system. Sådana system används för att konfigurera och kontrol lera CCTV-kameror, avbilda och spela in videor från dem. Dessa system tillhandahåller också klient program för att spela upp den inspelade videon

## <a name="next-steps"></a>Nästa steg

[Medie diagram](media-graph-concept.md)
