---
title: Media Graph-koncept – Azure
description: Med ett medie diagram kan du definiera var mediet ska samlas in, hur det ska bearbetas och var resultatet ska levereras. Den här artikeln innehåller en detaljerad beskrivning av media Graph-konceptet.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 5efb62440b52d6219373d15ba3d19ddac1a2a834
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007848"
---
# <a name="media-graph"></a>Mediegraf

## <a name="suggested-pre-reading"></a>Föreslagen för läsning

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)

## <a name="overview"></a>Översikt

Med ett medie diagram kan du definiera var mediet ska samlas in, hur det ska bearbetas och var resultatet ska levereras. Du kan göra detta genom att ansluta komponenter eller noder på önskat sätt. Diagrammet nedan innehåller en grafisk representation av ett medie diagram.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="Mediegraf":::

Live video analys på IoT Edge stöder olika typer av källor, processorer och mottagare.

* **Källnoden** möjliggör inhämtning av media i medie diagrammet. Media i den här kontexten kan konceptuellt vara en ljud ström, en video ström, en data ström eller en ström som innehåller ljud, video och/eller data som kombineras tillsammans i en enda ström.
* **Processor noder** möjliggör bearbetning av media i medie diagrammet.
* Med **Sink-noder** kan du leverera bearbetnings resultatet till tjänster och appar utanför medie diagrammet.

## <a name="media-graph-topologies-and-instances"></a>Media Graph-topologier och instanser 

Med live video analys på IoT Edge kan du hantera medie diagram via två koncept – "Graph Topology" och "Graph instance". Med en Graph-topologi kan du definiera skissen för en graf, med parametrar som plats hållare för värden. Topologin definierar vilka noder som används i medie diagrammet och hur de är anslutna i medie diagrammet. Om du till exempel vill spela in feeden från en kamera behöver du en graf med en källnod som tar emot video och en Sink-nod som skriver videon.

Värdena för parametrarna i topologin anges när du skapar diagram instanser som refererar till topologin. På så sätt kan du skapa flera instanser som refererar till samma topologi, men med olika värden för de parametrar som anges i topologin. I exemplet ovan kan du ha använt parametrar för att representera kamerans IP-adress och namnet på den inspelade videon. Du kan skapa många graf-instanser med topologin – en instans för varje kamera i en byggnad, kanske var och en med den angivna IP-adressen och det angivna namnet.

## <a name="media-graph-states"></a>Medie diagram status  

Diagrammets livs cykel och diagram instanser visas i följande tillstånds diagram.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="Graf-topologi och diagram instansen livs cykel":::

Du börjar med att [skapa en diagram sto pol Ogin](direct-methods.md#graphtopologyset). För varje direktsända video flöde som du vill bearbeta med den här topologin [skapar du en diagram instans](direct-methods.md#graphinstanceset). 

Graf-instansen är i `Inactive` tillståndet (inaktiv).

När du är redo att skicka Live-videofeeden till graf-instansen [aktiverar](direct-methods.md#graphinstanceactivate) du det. Graf-instansen går kort genom ett över gångs `Activating` tillstånd. om det lyckas går du till ett `Active` tillstånd. I `Active` läget kommer mediet att bearbetas (om graf-instansen tar emot indata).

> [!NOTE]
>  En diagram instans kan vara aktiv utan att data flödar genom den (till exempel kameran är offline).
> Din Azure-prenumeration kommer att faktureras när graf-instansen är i aktivt läge.

Du kan upprepa processen för att skapa och aktivera andra diagram instanser för samma topologi, om du har andra direktsända video flöden att bearbeta.

När du är färdig med bearbetningen av direktsänd video-inmatningen kan du [inaktivera](direct-methods.md#graphinstancedeactivate) graf-instansen. Graf-instansen går kort genom ett över gångs `Deactivating` tillstånd, tömmer alla data som de har och återgår sedan till `Inactive` statusen.

Du kan bara [ta bort](direct-methods.md#graphinstancedelete) en diagram instans när den är i ett `Inactive` tillstånd.

När alla graf-instanser som refererar till en speciell diagram sto pol Ogin har tagits bort, kan du [ta bort graf-topologin](direct-methods.md#graphtopologydelete).


## <a name="sources-processors-and-sinks"></a>Källor, processorer och mottagare  

Live video analys på IoT Edge stöder följande typer av noder i ett medie diagram:

### <a name="sources"></a>Källor 

#### <a name="rtsp-source"></a>RTSP-källa 

Med en RTSP-källmapp kan du mata in media från en [RTSP](https://tools.ietf.org/html/rfc2326) -Server. Övervaknings-och IP-baserade kameror överför sina data i ett protokoll som kallas RTSP (Real-Time-Streaming-Protocol) som skiljer sig från andra typer av enheter som telefoner och video kameror. Det här protokollet används för att upprätta och kontrol lera medie sessioner mellan en server (kameran) och en klient. Noden RTSP-källa i ett medie diagram fungerar som en klient och kan upprätta en session med en RTSP-server. Många enheter, till exempel de flesta [IP-kameror](https://en.wikipedia.org/wiki/IP_camera) har en inbyggd RTSP-server. [ONVIF](https://www.onvif.org/) bestämmer att RTSP ska stödjas i sin definition av [profiler G, S & T-](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) kompatibla enheter. RTSP-Källnoden kräver att du anger en RTSP-URL, tillsammans med autentiseringsuppgifter för att aktivera en autentiserad anslutning.

#### <a name="iot-hub-message-source"></a>IoT Hub meddelande källa 

Precis som andra [IoT Edge moduler](../../iot-edge/iot-edge-glossary.md#iot-edge-module)kan live video analys i IoT Edge-modulen ta emot meddelanden via [IoT Edge hubben](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Dessa meddelanden kan skickas från andra moduler eller appar som körs på gräns enheten eller från molnet. Sådana meddelanden levereras (dirigeras) till en [namngiven inmatning](../../iot-edge/module-composition.md#sink) i modulen. En nod för IoT Hub-meddelande källa tillåter att sådana meddelanden når ett medie diagram. Dessa meddelanden eller signaler kan sedan användas internt i medie diagrammet, vanligt vis för att aktivera signal portar (se [signal grindar](#signal-gate-processor) nedan). 

Du kan till exempel ha en IoT Edge-modul som genererar ett meddelande när en dörr öppnas. Meddelandet från modulen kan dirigeras till IoT Edge Hub, där det kan dirigeras till IoT Hub-meddelande källan för ett medie diagram. I medie grafen kan IoT Hub-meddelande källan skicka händelsen till en signal grind processor, som sedan kan aktivera inspelning av videon från en RTSP-källa till en fil. 

### <a name="processors"></a>Processorer  

#### <a name="motion-detection-processor"></a>Processor för rörelse identifiering 

Med noden motion-identifiering kan du identifiera rörelse i live video. Den undersöker inkommande video bild rutor och avgör om det finns rörelse i videon. Om rörelsen upptäcks, skickas den på video bild rutorna till den efterföljande komponenten och en händelse skapas. Noden för identifiering av rörelser (tillsammans med andra noder) kan användas för att utlösa inspelning av den inkommande videon när en rörelse upptäcks.

#### <a name="frame-rate-filter-processor"></a>Filter processor för RAM hastighet  

Med filtret för RAM hastighet filtrera processor kan du sampla ramar från den inkommande video strömmen med en angiven hastighet. På så sätt kan du minska antalet ramar som skickas till nedströms komponenter (till exempel noden HTTP-tilläggsbegäranden) för vidare bearbetning.

#### <a name="http-extension-processor"></a>Processor för HTTP-tillägg

Med noden HTTP-tillägg kan du ansluta din egen IoT Edge-modul till ett medie diagram. Den här noden tar avkodade video ramar som inaktuella inmatade och vidarebefordrar sådana ramar till en HTTP REST-slutpunkt som exponeras av din modul. Den här noden har möjlighet att autentisera med REST-slutpunkten om det behövs. Dessutom har noden en inbyggd avbildnings-Formatter för skalning och kodning av video bild rutor innan de vidarebefordras till REST-slutpunkten. Skalaren har alternativ för bild-höjd-förhållandet som ska bevaras, utfylls eller sträckas ut. Bild kodaren stöder JPEG-, PNG-eller BMP-format. Läs mer om processorn [här](media-graph-extension-concept.md#http-extension-processor).

#### <a name="grpc-extension-processor"></a>gRPC förlängnings processor

GRPC Extension processor-noden använder avkodade video ramar som inaktuella inmatade filer och vidarebefordrar sådana ramar till en [gRPC](terminology.md#grpc) -slutpunkt som exponeras av din modul. Noden stöder överföring av data med hjälp av [delat minne](https://en.wikipedia.org/wiki/Shared_memory) eller bädda in innehållet direkt i bröd texten i gRPC-meddelanden. Dessutom har noden en inbyggd avbildnings-Formatter för skalning och kodning av video bild rutor innan de vidarebefordras till gRPC-slutpunkten. Skalaren har alternativ för bild-höjd-förhållandet som ska bevaras, utfylls eller sträckas ut. Bild kodaren stöder JPEG-, PNG-eller BMP-format. Läs mer om processorn [här](media-graph-extension-concept.md#grpc-extension-processor).

#### <a name="signal-gate-processor"></a>Signal grind processor  

Med noden signal grind processor kan du skicka ett villkor till media från en nod till en annan. Den fungerar även som en buffert som tillåter synkronisering av medier och händelser. Ett vanligt användnings fall är att infoga en signal grind processor mellan noden RTSP-källa och noden till gångs mottagare och att använda utdata från en nod för motion-detektor för att utlösa porten. I ett sådant medie diagram skulle du bara spela in video när du har identifierat rörelse.

### <a name="sinks"></a>Mottagare  

#### <a name="asset-sink"></a>Till gångs mottagare  

Med noden till gångs mottagare kan du skriva medie data (video och/eller ljud) till en Azure Media Services till gång. Det kan bara finnas en nod för till gångs mottagare i ett medie diagram. Se avsnittet [till gång](terminology.md#asset) för mer information om till gångar och deras roll vid inspelning och uppspelning av media. Du kan också se artikeln [kontinuerlig video inspelning](continuous-video-recording-concept.md) för information om hur nodens egenskaper används.

#### <a name="file-sink"></a>Fil mottagare  

Med noden fil mottagare kan du skriva medie data (video och/eller ljud) till en plats i det lokala fil systemet på den IoT Edge enheten. Det får bara finnas en filsink-nod i ett medie diagram och den måste ligga bakom en signal grind processor. Detta begränsar varaktigheten för utdatafilerna till värden som anges i egenskaperna för signal porten processor.

#### <a name="iot-hub-message-sink"></a>IoT Hub meddelande mottagare  

Med noden IoT Hub meddelande mottagare kan du publicera händelser till IoT Edge Hub. IoT Edge Hub kan sedan dirigera data till andra moduler eller appar på gräns enheten eller till IoT Hub i molnet (per vägar som anges i distributions manifestet). Noden IoT Hub meddelande mottagare kan acceptera händelser från överordnade processorer, till exempel en nod för identifiering av rörelser, eller från en extern härlednings tjänst via en HTTP-tilläggsprovider.

## <a name="rules-on-the-use-of-nodes"></a>Regler för användning av noder

Se [begränsningar i Graph-topologier](quotas-limitations.md#limitations-on-graph-topologies-at-preview) för ytterligare regler för hur olika noder kan användas i ett medie diagram.

## <a name="scenarios"></a>Scenarier

Med en kombination av de källor, processorer och handfat som definierats ovan kan du bygga medie diagram för en rad olika scenarier som omfattar analys av direktsänd video. Exempel scenarier är:

* [Kontinuerlig videoinspelning](continuous-video-recording-concept.md)
* [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)
* [Videoanalys i realtid utan videoinspelning](analyze-live-video-concept.md)

## <a name="next-steps"></a>Nästa steg

Information om hur du kan köra rörelse identifiering på en Live-videofeed finns i [snabb start: kör Live Video Analytics med din egen modell](use-your-model-quickstart.md).
