---
title: Händelse baserad video inspelning – Azure
description: Event-based Video Recording (EVR) syftar på att spela in videon som utlöses av en händelse. Händelsen i fråga kunde ha sitt ursprung på grund av bearbetning av själva video signalen (till exempel identifiering av rörelse) eller kan komma från en oberoende källa (till exempel öppning av en dörr).  Några användnings fall som rör händelsebaserade videoinspelningar beskrivs i den här artikeln.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 6a5f4873b2cfef8d9a6594916d82cd30a3bc1cc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97401616"
---
# <a name="event-based-video-recording"></a>Händelsebaserad videoinspelning  
 
## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

* [Kontinuerlig videoinspelning](continuous-video-recording-concept.md)
* [Uppspelning av inspelat innehåll](video-playback-concept.md)
* [Media diagram-koncept](media-graph-concept.md)

## <a name="overview"></a>Översikt 

Event-based Video Recording (EVR) syftar på att spela in videon som utlöses av en händelse. Händelsen i fråga kunde ha sitt ursprung på grund av bearbetning av själva video signalen (till exempel identifiering av rörelse) eller kan komma från en oberoende källa (till exempel öppning av en dörr). 

Du kan spela in video (utlöst av en händelse) från en CCTV-kamera till en Media Services till gång med ett medie diagram som består av en [RTSP-källmapp](media-graph-concept.md#rtsp-source) , noden [till gångs mottagare](media-graph-concept.md#asset-sink) och andra noder som beskrivs i användnings fallen nedan. Du kan konfigurera noden [till gångs mottagare](media-graph-concept.md#asset-sink) för att generera nya till gångar varje gång en händelse inträffar, så att videon som motsvarar varje händelse kommer att finnas i en egen till gång. Du kan också välja att använda en till gång för att lagra videon för alla händelser. 

Som ett alternativ till noden till gångs mottagare kan du använda en [filsink](media-graph-concept.md#file-sink) -nod för att skapa korta kodfragment av video (relaterat till en händelse av intresse) till en angiven plats i det lokala fil systemet på gräns enheten. 

Några användnings fall som rör händelsebaserade videoinspelningar beskrivs i den här artikeln.

### <a name="video-recording-based-on-motion-detection"></a>Video inspelning baserad på rörelse identifiering  

I det här användnings fallet kan du bara spela in videoklipp när det uppstår en rörelse i videon och bli aviserad när ett sådant videoklipp skapas. Detta kan vara relevant i kommersiella säkerhets scenarier som omfattar skydd av kritisk infrastruktur. Genom att generera aviseringar och spela in video när oväntad rörelse identifieras kan du förbättra den mänskliga operatörens effektivitet eftersom åtgärden bara behövs när aviseringen genereras.

Diagrammet nedan visar en grafisk representation av ett medie diagram som hanterar det här användnings fallet. JSON-representationen av diagram sto pol Ogin för ett sådant medie diagram finns [här](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Video inspelning baserad på rörelse identifiering":::

I diagrammet fångar RTSP-Källnoden Live-videofeeden från kameran och levererar den till en nod för [rörelse identifiering](media-graph-concept.md#motion-detection-processor) . Vid identifiering av rörelse i Live-videon genererar noden för motion-identifiering en händelse som går till noden [signal grind processor](media-graph-concept.md#signal-gate-processor) , samt till noden IoT Hub meddelande mottagare. Den senare noden skickar händelserna till IoT Edge Hub, där de kan dirigeras till andra destinationer för att utlösa aviseringar. 

En händelse från rörelse detektor-noden utlöser noden signal grind processor och tillåter att Live-videofeeden från noden RTSP-källa flödar till till gångs mottagar noden. I avsaknad av sådana rörelse identifierings händelser kommer porten att stängas efter en konfigurerad tid. Detta anger varaktigheten för den inspelade videon.

### <a name="video-recording-based-on-events-from-other-sources"></a>Videoinspelning baserat på händelser från andra källor  

I det här användnings fallet kan signaler från en annan IoT-sensor användas för att utlösa inspelning av video. Diagrammet nedan visar en grafisk representation av ett medie diagram som hanterar det här användnings fallet. JSON-representationen av diagram sto pol Ogin för ett sådant medie diagram finns [här](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Videoinspelning baserat på händelser från andra källor":::

I diagrammet skickar den externa sensorn händelser till IoT Edge Hub. Händelserna dirigeras sedan till noden signal grind processor via noden [IoT Hub-meddelande källa](media-graph-concept.md#iot-hub-message-source) . Beteendet för signal porten processor är detsamma som i föregående användnings fall – den öppnas och gör att Live-videofeeden flödar från noden RTSP-källa till filen Sink-noden (eller till gångs mottagarens nod) när den utlöses av den externa händelsen. 

Om du använder en fil mottagar nod, registreras videon i det lokala fil systemet på gräns enheten. Om du använder en nod för till gångs mottagare registreras videon till en till gång.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Video inspelning baserad på en extern inferencing-modul 

I detta fall kan du spela in videoklipp baserat på en signal från ett externt logik system. Ett exempel på ett sådant användnings fall kan bara spela in ett videoklipp när en Last bil upptäcks i video flödet för trafik på en väg. Diagrammet nedan visar en grafisk representation av ett medie diagram som hanterar det här användnings fallet. JSON-representationen av diagram sto pol Ogin för ett sådant medie diagram finns [här](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Video inspelning baserad på en extern inferencing-modul":::

I diagrammet fångar RTSP-Källnoden Live-videofeeden från kameran och levererar den till två grenar: en har en [signal grind processor](media-graph-concept.md#signal-gate-processor) , och den andra använder en [http-tilläggsprovider](media-graph-concept.md) för att skicka data till en extern Logic-modul. Med noden HTTP-tillägg kan Media grafen skicka bild ramar (i JPEG-, BMP-eller PNG-format) till en extern tjänst Överlagrings tjänst över REST. Den här signal Sök vägen kan vanligt vis bara stödja låga bild hastigheter (<5fps). Du kan använda noden för HTTP-tilläggsbegäranden för att sänka bild Rute frekvensen för videon som går till den externa inferencing-modulen.

Resultaten från den externa härlednings tjänsten hämtas av HTTP-tillägget och vidarebefordras till IoT Edge Hub via noden IoT Hub meddelande mottagare där de kan bearbetas ytterligare av modulen extern logik. Om härlednings tjänsten kan identifiera fordon, kan Logic-modulen söka efter ett speciellt fordon, till exempel en buss eller en Last bil. När Logic-modulen identifierar ett objekt av intresse, kan den utlösa noden signal grind processor genom att skicka en händelse via IoT Edge hubben till noden IoT Hub meddelande källa i grafen. Utdata från signal porten visas för att gå till antingen en fil mottagare-nod eller en nod för till gångs mottagare. I det förra fallet kommer videon att registreras i det lokala fil systemet på gräns enheten. I det senare fallet kommer videon att spelas in till en till gång.

En förbättring i det här exemplet är att använda en rörelse detektor processor före noden HTTP-tilläggsbegäranden. Detta minskar belastningen på härlednings tjänsten, till exempel Nighttime om det kan finnas långa tids perioder när det inte finns några fordon på motorväg. 

## <a name="next-steps"></a>Nästa steg

[Självstudie: händelse-baserad videoinspelning](event-based-video-recording-tutorial.md)
