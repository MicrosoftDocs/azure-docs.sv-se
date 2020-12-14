---
title: Live video analys på IoT Edge FAQ – Azure
description: Det här avsnittet innehåller svar på video analys i real tid för IoT Edge vanliga frågor och svar.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401584"
---
# <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

Det här avsnittet innehåller svar på video analys i real tid för IoT Edge vanliga frågor och svar.

## <a name="general"></a>Allmänt

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Vilka systemvariabler kan användas i definition av diagram miljön?

|Variabel   |Beskrivning|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Representerar en omedelbar UTC-tid, vanligt vis uttryckt som datum och tid på dagen (grundläggande representation av yyyyMMddTHHmmssZ).|
|System. PreciseDateTime|Representerar en UTC datum instans i ISO8601-filkompatibelt format med millisekunder (grundläggande representation av yyyyMMddTHHmmss. fffZ).|
|System. GraphTopologyName   |Representerar en medie diagram sto pol Ogin, innehåller skissen på ett diagram.|
|System. GraphInstanceName|  Representerar en medie diagram instans, innehåller parameter värden och refererar till topologin.|

## <a name="configuration-and-deployment"></a>Konfiguration och distribution

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>Kan jag distribuera Media Edge-modulen till en Windows 10-enhet?

Ja. Se artikeln om [Linux-behållare i Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Avbilda från IP-kamera och RTSP-inställningar

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>Behöver jag använda ett särskilt SDK på enheten för att skicka in en videoström?

Nej. Video analys i real tid med IoT Edge stöder hämtning av media med RTSP-video strömnings protokoll (som stöds på de flesta IP-kameror).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>Kan jag skicka media till direktsänd video analys på IoT Edge med hjälp av RTMP eller utjämna (som ett Media Services live event)?

* Nej. Real Video Analytics stöder bara RTSP för att hämta video från IP-kameror.
* Alla kameror som stöder RTSP-strömning via TCP/HTTP bör fungera. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>Kan jag återställa eller uppdatera RTSP-källans URL på en diagraminstans?

Ja, när diagram instansen är i inaktivt läge.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>Finns det en RTSP-simulator som kan användas vid testning och utveckling?

Ja. Det finns en [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-modul som är tillgänglig för användning i snabb starter och självstudier för att stödja inlärnings processen. Den här modulen tillhandahålls då det är möjligt, men kanske inte alltid är tillgänglig. Vi rekommenderar starkt att du inte använder detta i mer än några timmar. Du bör investera i testning med din faktiska RTSP-källa innan du gör några planer på en produktions distribution.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Finns det stöd för ONVIF-identifiering av IP-kameror vid nätverksgränsen?

Nej, det finns inget stöd för ONVIF-identifiering av enheter vid gränsen.

## <a name="streaming-and-playback"></a>Strömning och uppspelning

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Kan till gångar som registrerats för AMS från kanten spelas upp med Media Services strömmande tekniker som HLS eller tank streck?

Ja. Registrerade till gångar kan strömmas på samma sätt som andra till gångar i Azure Media Services. För att strömma innehållet måste du ha en slut punkt för direkt uppspelning skapad och i körnings läge. Genom att använda standard processen för att skapa streaming-direktuppspelning får du till gång till ett HLS-eller tank strecks manifest för strömning till valfritt Player-ramverk. Mer information om hur du skapar publicerings-HLS eller streck manifest finns i [dynamisk paketering](../latest/dynamic-packaging-overview.md).

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>Kan jag använda standard innehålls skyddet och DRM-funktionerna i Media Services på en arkiverad till gång?

Ja. Alla standard funktioner för dynamisk kryptering och DRM-funktioner är tillgängliga för användning på de till gångar som registrerats från ett medie diagram.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Vilka spelare kan jag använda för att visa innehåll från de inspelade resurserna?

Alla standard spelare som stöder kompatibel Apple HTTP Live Streaming (HLS) version 3 eller version 4 stöds. Dessutom stöds även alla spelare som stöder uppspelning av MPEG-streck.

Rekommenderade spelare för testning är:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka-spelare](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple Native HTTP Live Streaming](https://developer.apple.com/streaming/)
* Edge, Chrome eller Safari inbyggd HTML5 Videos pelare
* Kommersiella spelare som stöder HLS-eller streck uppspelning

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Vilka är gränserna för strömning av en Media diagram-till gång?

Direkt uppspelning av en levande eller inspelad till gång från ett medie diagram använder samma infrastruktur för hög skalnings-och strömnings slut punkt som Media Services stöd för OTT på begäran och live-strömning för media & underhållning, och sändnings kunder. Det innebär att du snabbt och enkelt kan aktivera Azure CDN, Verizon eller Akamai för att leverera ditt innehåll till en mål grupp så litet som några visnings program, eller upp till miljon tals beroende på ditt scenario.

Innehåll kan levereras med både Apple HTTP Live Streaming (HLS) eller MPEG-tank streck.

## <a name="design-your-ai-model"></a>Utforma din AI-modell 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Jag har flera AI-modeller inkapslade i en Docker-behållare. Hur ska jag använda dem med live video analys? 

Lösningarna är olika beroende på vilket kommunikations protokoll som används av inferencing-servern för att kommunicera med live video analys. Nedan visas några sätt att göra detta.

#### <a name="http-protocol"></a>HTTP-protokoll:

* Enskild behållare (enskild lvaExtension):  

   I din inferencing-Server kan du använda en enda port men olika slut punkter för olika AI-modeller. För ett python-exempel kan du till exempel använda olika `route` s per modell som: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   I din live video analys-distribution, när du instansierar grafer, anger du URL: en för härlednings servern för varje instans som: 

   första instans: URL för härlednings Server =`http://lvaExtension:44000/score/face_detection`<br/>
   andra instans: URL för härlednings Server =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > Alternativt kan du också visa dina AI-modeller på olika portar och anropa dem när du instansierar grafer.  

* Flera behållare: 

   Varje behållare distribueras med ett annat namn. I dokumentationen för Live Video Analytics har vi för närvarande visat dig hur du distribuerar ett tillägg med namnet: **lvaExtension**. Nu kan du utveckla två olika behållare. Varje behållare har samma HTTP-gränssnitt (d.v.s. samma `/score` slut punkt). Distribuera dessa två behållare med olika namn och se till att båda lyssnar på **olika portar**. 

   En behållare med namnet lyssnar till exempel `lvaExtension1` efter porten `44000` , annan behållare med namnet `lvaExtension2` lyssnar efter porten `44001` . 

   I din Live Video Analytics-topologi instansierar du två grafer med olika härlednings-URL: er som: 

   Första instans: URL för härlednings Server = `http://lvaExtension1:44001/score`    
   Andra instans: URL för härlednings Server = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>GRPC-protokoll: 

När du använder ett gRPC-protokoll i live video analys modul 1,0 blir det enda sättet om gRPC-servern exponerade olika AI-modeller via olika portar. I [det här exemplet](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)finns det en enda port, 44000 som exponerar alla Yolo-modeller. I teorin kan Yolo gRPC-servern skrivas om för att visa vissa modeller på 44000, andra vid 45000,... 

Med Live Video Analytics module 2,0 läggs en ny egenskap till i noden gRPC-tillägg. Den här egenskapen kallas **extensionConfiguration** som är en valfri sträng som kan användas som en del av gRPC-kontraktet. Om du har flera AI-modeller som paketeras i en enda härlednings server behöver du inte exponera en nod för varje AI-modell. I stället för en diagram instans, kan förlängnings leverantören (du) definiera hur du väljer de olika AI-modellerna med egenskapen **extensionConfiguration** och under körningen kommer live video analys att skicka den här strängen till inferencing-servern som kan använda den för att anropa den önskade AI-modellen. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>Jag skapar en gRPC-Server runt en AI-modell och vill kunna använda den av flera kameror/diagram instanser. Hur ska jag bygga min server? 

 Kontrol lera först att servern kan hantera fler än en begäran i taget. Eller kontrol lera att servern fungerar i parallella trådar. 

I ett av [GRPC-exempel i Real video analys](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)finns det ett standard antal parallella kanaler. Se: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

I gRPC-servern ovan kan servern bara öppna tre kanaler per kamera (så per diagram sto pol instance) i taget. Du bör inte försöka ansluta fler än tre instanser till servern. Om du försöker öppna fler än tre kanaler, väntar förfrågningar tills en befintlig finns kvar.  

Över gRPC-serverimplementeringar används i våra python-exempel. Utvecklare kan implementera sina egna servrar eller i ovanstående standard implementering kan öka arbets numret till det antal kameror som används för att hämta video matningen från. 

Utvecklare kan skapa och använda flera kameror för att skapa en instans av en diagram sto pol Ogin där varje instans pekar på samma eller en annan härlednings Server (till exempel server som nämns i stycket ovan). 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Jag vill kunna ta emot flera ramar från överordnade innan jag fattar ett inferencing-beslut. Hur kan jag aktivera det? 

Aktuella [standard exempel](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) fungerar i läget "tillstånds lös". Dessa exempel behåller inte status för de tidigare anropen och även om de anropas (vilket innebär att flera topologier kan anropa samma lönsamhets Server, och servern kommer inte att kunna särskilja vem som anropar och tillstånd per anropare) 

#### <a name="http-protocol"></a>HTTP-protokoll

När du använder HTTP-protokollet: 

För att behålla tillstånd, anropar varje anropare (graf-instans) inferencing-servern med HTTP-Frågeparametern som är unik för anroparen. Till exempel URL-adress för härlednings Server för  

instans av första topologi = `http://lvaExtension:44000/score?id=1`<br/>
instans av andra topologin = `http://lvaExtension:44000/score?id=2`

… 

På Server sidan vet Poäng vägen vem som anropar. Om ID = 1, kan den hålla status separat för den anroparen (graf-instans). Du kan sedan behålla de mottagna video bild rutorna i en buffert (till exempel matris eller en ord lista med en DateTime-nyckel och värdet är ramen) och sedan kan du definiera servern att bearbeta (Härled) efter att x-ramar tas emot. 

#### <a name="grpc-protocol"></a>GRPC-protokoll 

När du använder gRPC-protokollet: 

I ett gRPC-tillägg är varje session för en enda Camera-feed så det finns inget behov av att ange ett ID. Nu med egenskapen extensionConfiguration kan du lagra video bild rutorna i en buffert och definiera den server som ska bearbetas (Härled) efter att x-ramar tas emot. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Kör alla ProcessMediaStreams på en viss behållare samma AI-modell? 

Nej.  

Starta/stoppa samtal från slutanvändaren på en diagram instans är en session, eller så kanske det finns en kamera från koppling/åter anslutning. Målet är att spara en session om kameran strömmar video. 

* Två kameror som skickar video för bearbetning skapar två sessioner. 
* En kamera går till en graf som har två gRPCExtension-noder som skapar två sessioner. 

Varje session är en full duplex-anslutning mellan live video analys och gRPC-servern och varje session kan ha en annan modell/pipeline. 

> [!NOTE]
> Om en kamera från koppling/återansluter (med kameran försätts i offlineläge under en period utanför tolerans gränserna) öppnas en ny session med gRPC-servern i live video analys. Det finns inget krav på att servern ska spåra tillstånd över dessa sessioner. 

Live Video Analytics har även stöd för flera gRPC-tillägg för en enskild kamera i en diagram instans. Du kommer att kunna använda dessa gRPC-tillägg för att utföra AI-bearbetning sekventiellt eller parallellt eller även ha en kombination av båda. 

> [!NOTE]
> Om flera tillägg körs parallellt påverkas dina maskin varu resurser och du måste tänka på detta när du väljer maskin vara som passar dina beräknings behov. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Vad är Max antalet samtidiga ProcessMediaStreams? 

Det finns ingen gräns för att live video analys ska tillämpas.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Hur avgör jag om min inferencing-Server ska använda CPU eller GPU eller någon annan maskin varu Accelerator? 

Detta är helt beroende av hur komplex AI-modellen utvecklas och hur utvecklaren vill använda processor-och maskin varu acceleratorer. När AI-modellen utvecklades kan utvecklarna ange vilka resurser som ska användas av modellen för att utföra åtgärder. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>Hur gör jag för att lagra bilder med markerings ramar efter bearbetning? 

Idag tillhandahåller vi gränser för avgränsnings rutor enbart som härlednings meddelanden. Utvecklare kan bygga en anpassad MJPEG-dataström som kan använda dessa meddelanden och täcka markerings rutorna över video bild rutorna.  

## <a name="grpc-compatibility"></a>gRPC-kompatibilitet 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Hur vet jag vilka obligatoriska fält för media Stream-beskrivningen är? 

Alla fält värden som inte anges får ett standardvärde [som anges av gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

I real tid av video analys används **proto3** -versionen av frågespråket. Alla protokoll data som används av Live Video Analytics-kontrakt är tillgängliga i de Protocol-buffertar som [definieras här](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Hur ska jag se till att jag använder de senaste Protocol-buffertarna? 

De senaste Protocol-filerna kan [hämtas här](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). När vi uppdaterar filerna visas de på den här platsen. Det finns ingen omedelbar plan för att uppdatera filerna, leta efter paket namnet överst i filerna för att känna till versionen. Den bör läsa: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Eventuella uppdateringar av de här filerna ökar "v-värdet" i slutet av namnet. 

> [!NOTE]
> Eftersom Real video analys använder proto3-versionen av språket är fälten valfria och det gör att det går att gå bakåt och framåt. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Vilka gRPC-funktioner är tillgängliga för mig att använda med live video analys? Vilka funktioner är obligatoriska och vilka är de valfria? 

Alla gRPC-funktioner på Server sidan kan användas, förutsatt att protobuf-kontraktet är uppfyllt. 

## <a name="monitoring-and-metrics"></a>Övervakning och mått

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Kan jag övervaka medie diagrammet på gränsen med Event Grid?

Ja. Du kan använda Prometheus-mått och publicera dem i Event Grid. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Kan jag använda Azure Monitor för att Visa hälso tillstånd, mått och prestanda för mina medie diagram i molnet eller på gränsen?

Ja. Det här stöds. Läs mer om [hur du använder Azure Monitor mått](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Finns det några verktyg för att göra det enklare att övervaka modulen Media Services IoT Edge?

Visual Studio Code stöder tillägget "Azure IoT Tools" som gör att du enkelt kan övervaka slut punkterna i LVAEdge-modulen. Du kan använda det här verktyget för att snabbt starta övervakning av den IoT Hub inbyggda slut punkten för "händelser" och se de härlednings meddelanden som dirigeras från gräns enheten till molnet. 

Dessutom kan du använda det här tillägget för att redigera modulen för LVAEdge-modulen för att ändra inställningarna för media Graph.

Mer information finns i artikeln om [övervakning och loggning](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fakturering och tillgänglighet

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Hur fungerar Real video analys på IoT Edge faktureras?

Mer information finns på [sidan med pris](https://azure.microsoft.com/pricing/details/media-services/) information.

## <a name="next-steps"></a>Nästa steg

[Snabb start: kom igång – direktsända video analyser på IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
