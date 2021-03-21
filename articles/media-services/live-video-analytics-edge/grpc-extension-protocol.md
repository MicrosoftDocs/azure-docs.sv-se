---
title: gRPC Extension Protocol – Azure
description: I den här artikeln får du lära dig mer om att använda gRPC-tilläggsprogram för att skicka meddelanden mellan Live Video Analytics-modulen och ditt AI eller ka anpassade tillägg.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f7b5be859702199b07dfa0d6a43a09ca8ff0c42f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455864"
---
# <a name="grpc-extension-protocol"></a>Protokoll för gRPC-tillägg

Med live video analys på IoT Edge kan du utöka medie diagrammets bearbetnings funktioner via en [nod i diagram tillägget](/azure/media-services/live-video-analytics-edge/media-graph-extension-concept). Om du använder gRPC Extension-processorn som tilläggsprovider är kommunikationen mellan Live Video Analytics-modulen och din AI-eller ka-modul över gRPC-baserade, högpresterande strukturerat protokoll.

I den här artikeln får du lära dig mer om att använda gRPC-tilläggsprogram för att skicka meddelanden mellan Live Video Analytics-modulen och ditt AI eller ka anpassade tillägg.

gRPC är ett modernt, högpresterande RPC-ramverk med öppen källkod som körs i valfri miljö och stöder plattforms oberoende och kommunikation mellan olika språk. GRPC transport service använder HTTP/2 dubbelriktad strömning mellan:

* gRPC-klienten (direktsänd video analys i IoT Edge modul) och 
* gRPC-servern (ditt anpassade tillägg).

En gRPC-session är en enskild anslutning från gRPC-klienten till gRPC-servern via TCP/TLS-porten. 

I en enda session: klienten skickar en Media Stream-Beskrivning följt av video bild rutor till servern som ett [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -meddelande över gRPC Stream-sessionen. Servern verifierar Stream-beskrivningen, analyserar video ramen och returnerar utöknings resultat som ett protobuf-meddelande. 

Vi rekommenderar starkt att svaren returneras med giltiga JSON-dokument efter det företablerade schemat som definierats enligt [schema objekt modellen för härledning av metadata](/azure/media-services/live-video-analytics-edge/inference-metadata-schema). Detta säkerställer bättre samverkan med andra komponenter och möjliga framtida funktioner som läggs till i modulen för video analys i real tid.

![kontrakt för gRPC-tillägg](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>Implementerar gRPC-protokoll

### <a name="creating-a-grpc-connection"></a>Skapa en gRPC-anslutning

Det anpassade tillägget måste implementera följande gRPC-tjänst:

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

När detta anropas öppnas en dubbelriktad ström för meddelanden som flödar mellan gRPC-tillägget och video analys diagrammet i real tid. Det första meddelandet som skickas i den här strömmen av varje part innehåller en MediaStreamDescriptor som definierar vilken information som ska skickas i följande MediaSamples.

Till exempel kan Graph-tillägget skicka meddelandet (anges här i JSON) för att indikera att det skickar 416x416 RGB24-kodade bild rutor som är inbäddade i gRPC-meddelanden till det anpassade tillägget.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

Det anpassade tillägget skulle, i svar, skicka följande meddelande för att indikera att det endast returnerar inferences.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

Nu när båda sidorna har Exchange Media-beskrivningar börjar Real video analys att skicka ramar till tillägget.

> [!NOTE]
> Implementeringen av gRPC på Server sidan kan göras i valfritt programmeringsspråk.
### <a name="sequence-numbers"></a>Sekvensnummer

Både gRPC-tillägget och det anpassade tillägget upprätthåller en separat uppsättning sekvensnummer som är tilldelade till deras meddelanden. Dessa sekvensnummer ska monotont från 1. `ack_sequence_number` kan ignoreras om inget meddelande bekräftas, vilket kan inträffa när det första meddelandet skickas.

En begäran måste bekräftas när motsvarande meddelande har bearbetats fullständigt. Om det finns en delad minnes överföring anger den här bekräftelsen att avsändaren kan frigöra det delade minnet och att mottagaren inte längre kommer åt den. Avsändaren måste innehålla alla delade minnen tills den har bekräftats.

### <a name="reading-embedded-content"></a>Läser inbäddat innehåll

Inbäddat innehåll kan läsas direkt från `MediaSample` meddelandet via `content_byte` fältet. Data kommer att kodas enligt `MediaDescriptor` .

### <a name="reading-content-from-shared-memory"></a>Läser innehåll från delat minne

När du överför innehåll via delat minne, kommer avsändaren att inkludera `SharedMemoryBufferTransferProperties` `MediaStreamDescriptor` när de först upprättar en session. Detta kan se ut så här (uttryckt i JSON):

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

Mottagaren öppnar sedan filen `/dev/shm/inference_client_share_memory_2146989006636459346` . Avsändaren skickar ett `MediaSample` meddelande som innehåller en `ContentReference` hänvisning till en speciell plats i den här filen.

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

Mottagaren läser sedan data från den här platsen i filen.

För att Live Video Analytics-behållaren ska kommunicera över delat minne måste IPC-läget för behållaren vara korrekt konfigurerat. Detta kan göras på många sätt, men här är några rekommenderade konfigurationer.

* När du kommunicerar med en gRPC inferencing-motor som körs på värd enheten ska IPC-läget vara värd.
* När du kommunicerar med en gRPC-server som körs i en annan IoT Edge modul ska IPC-läget vara inställt på delnings bara för modulen live video analys och `container:liveVideoAnalytics` för det anpassade tillägget, där `liveVideoAnalytics` är namnet på modulen för video analys i real tid.

Det här kan se ut som i enheten, med det första alternativet ovan.

```
"liveVideoAnalytics": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "host"
      }
  }
}
```

Mer information om IPC-lägen finns i https://docs.docker.com/engine/reference/run/#ipc-settings---ipc .

## <a name="mediagraph-grpc-extension-contract-definitions"></a>MediaGraph gRPC tillägg kontrakts definitioner

Det här avsnittet definierar gRPC-kontraktet som definierar data flödet.

### <a name="protocol-messages"></a>Protokoll meddelanden

![Protokoll meddelanden](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>Klientautentisering

Implementerare av anpassade tillägg kan verifiera äktheten på inkommande gRPC-anslutningar för att vara säker på att de kommer från noden gRPC-tillägg. Noden tillhandahåller en post i begärandehuvuden för att validera mot.

Autentiseringsuppgifter för användar namn/lösen ord kan användas för att åstadkomma detta. När du skapar en gRPC-utöknings-nod, anges autentiseringsuppgifterna som nedan:

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

När gRPC-begäran skickas kommer följande huvud att ingå i metadata för begäran, mimicking HTTP Basic-autentisering.

`x-ms-authentication: Basic (Base64 Encoded username:password)`


## <a name="configuring-inference-server-for-each-mediagraph-over-grpc-extension"></a>Konfigurera en härlednings Server för varje MediaGraph över gRPC-tillägg
När du konfigurerar en härlednings server behöver du inte exponera exponera en nod för varje AI-modell som är paketerad i en härlednings Server. I stället kan du använda `extensionConfiguration` `MediaGraphGrpcExtension` nodens egenskap och definiera hur du väljer AI-modell (er) för en diagram instans. Under körningen skickar LVA strängen till inferencing-servern som kan använda den för att anropa den önskade AI-modellen. Den här `extensionConfiguration` egenskapen är en valfri egenskap och är Server Specific. Egenskapen kan användas som nedan:
```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "5"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>Använda gRPC över TLS

En gRPC-anslutning som används för inferencing kan skyddas via TLS. Detta är användbart i situationer där säkerheten i nätverket mellan live video analys och inferencing-motorn inte kan garanteras. TLS krypterar allt innehåll som är inbäddat i gRPC-meddelanden, vilket ger ytterligare processor belastning när du överför ramar till en hög hastighet.

IgnoreHostname-och IgnoreSignature-verifierings alternativen stöds inte av gRPC, så Server certifikatet, som inferencing-motorn presenterar måste innehålla ett CN som matchar exakt med IP-adressen/hostname i URL: en för den gRPC-tilläggs nodens slut punkt.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om schemat för härledning av metadata](inference-metadata-schema.md)
