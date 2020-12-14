---
title: Analysera direktsänd video utan inspelning – Azure
description: Ett medie diagram kan användas för att bara extrahera analyser från direktuppspelad video ström, utan att behöva registrera det på gränsen eller i molnet. Den här artikeln beskriver det här konceptet.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 25a7cadc47603b726542fa391d441e1fbca78908
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97398982"
---
# <a name="analyzing-live-video-without-any-recording"></a>Analysera direktsänd video utan inspelning

## <a name="suggested-pre-reading"></a>Föreslagen för läsning 

* [Media diagram-koncept](media-graph-concept.md)
* [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)

## <a name="overview"></a>Översikt  

Du kan använda ett medie diagram för att analysera direktsänd video utan att spela in några delar av videon till en fil eller en till gång. De medie diagram som visas nedan liknar dem i artikeln om [Event-baserad videoinspelning](event-based-video-recording-concept.md), men utan en nod för till gångs mottagare eller fil mottagare.

### <a name="motion-detection"></a>Rörelse identifiering

Det medie diagram som visas nedan består av en [RTSP-källnod](media-graph-concept.md#rtsp-source) , en nod för motion- [identifiering](media-graph-concept.md#motion-detection-processor) och en IoT Hub-nod för [meddelande mottagare](media-graph-concept.md#iot-hub-message-sink) . JSON-representationen av diagram sto pol Ogin för ett sådant medie diagram finns [här](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json). I det här diagrammet kan du identifiera rörelser i inkommande video strömmar och vidarebefordra rörelse händelserna till andra appar och tjänster via noden IoT Hub meddelande mottagare. Externa appar eller tjänster kan utlösa en avisering eller skicka ett meddelande till lämplig personal.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Real video analys baserat på rörelse identifiering":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>Analysera video med hjälp av en anpassad vision-modell 

I medie diagrammet som visas nedan kan du analysera en video ström i real tid med hjälp av en anpassad vision modell som paketeras i en separat modul. JSON-representationen av diagram sto pol Ogin för ett sådant medie diagram finns [här](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json). Du kan se några exempel [här](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) när du omsluter modeller till IoT Edge moduler som körs som en härlednings tjänst.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="Direktsända video analyser baserade på en extern inferencing-modul":::

I det här medie diagrammet skickas video indata från RTSP-källan till en nod för [http-tilläggsbegäranden](media-graph-concept.md#http-extension-processor) , som skickar bild ramar (i JPEG-, BMP-eller PNG-format) till en extern tjänst för överanvändnings störningar. Resultaten från den externa härlednings tjänsten hämtas av HTTP-tillägget och vidarebefordras till IoT Edge Hub via noden IoT Hub meddelande mottagare. Den här typen av medie diagram kan användas för att skapa lösningar för en mängd olika scenarier, till exempel att förstå Time-Series-distributionen av fordon i ett snitt, förstå konsument trafik mönstret i en detalj handels butik och så vidare.
>[!TIP]
> Du kan hantera bild frekvensen i noden för HTTP-tilläggsbegäranden med hjälp av fältet innan du skickar den till en längre tid `samplingOptions` .

En förbättring i det här exemplet är att använda en rörelse detektor processor före noden HTTP-tilläggsbegäranden. Detta minskar belastningen på härlednings tjänsten eftersom den endast används när det finns rörelse aktivitet i videon.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="Direktsända video analyser baserat på rörelse identifierade ramar via extern inferencing-modul":::

## <a name="next-steps"></a>Nästa steg

[Kontinuerlig videoinspelning](continuous-video-recording-concept.md)
