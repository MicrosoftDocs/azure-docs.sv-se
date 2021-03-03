---
title: Live video analys på IoT Edge vanliga frågor och svar – Azure
description: Den här artikeln besvarar vanliga frågor om real tids analys av IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 72a07a1a509aebcd7ba4048d0c84e913481c978e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702257"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Vanliga video analyser på IoT Edge vanliga frågor och svar

Den här artikeln besvarar vanliga frågor om real tids analys av Azure IoT Edge.

## <a name="general"></a>Allmänt

**Vilka systemvariabler kan jag använda i definitionen för grafens topologi?**

| Variabel   |  Beskrivning  | 
| --- | --- | 
| [System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Representerar en snabb UTC-tid, vanligt vis uttryckt som datum och tid på dagen i följande format:<br>*yyyyMMddTHHmmssZ* | 
| System. PreciseDateTime | Representerar en datum/tid-instans med UTC-tid (Coordinated Universal Time) i ett ISO8601-kompatibelt format med millisekunder i följande format:<br>*yyyyMMddTHHmmss. fffZ* | 
| System. GraphTopologyName   | Representerar en Media Graph-topologi och innehåller skissen på ett diagram. | 
| System. GraphInstanceName |    Representerar en medie diagram instans, innehåller parameter värden och refererar till topologin. | 

## <a name="configuration-and-deployment"></a>Konfiguration och distribution

**Kan jag distribuera Media Edge-modulen till en Windows 10-enhet?**

Ja. Mer information finns i [Linux-behållare i Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Avbilda från IP-kamera och RTSP-inställningar

**Behöver jag använda ett särskilt SDK på enheten för att skicka in en videoström?**

Nej, live video analys på IoT Edge stöder hämtning av media med hjälp av RTSP (real tids strömnings protokoll) för video strömning, som stöds på de flesta IP-kameror.

**Kan jag skicka media till direktsänd video analys på IoT Edge med hjälp av RTMP (Real-Time Messaging Protocol) eller Smooth Streaming protokoll (till exempel en Media Services Live-händelse)?**

Nej, Real video analys stöder endast RTSP för att hämta video från IP-kameror. Alla kameror som stöder RTSP-strömning via TCP/HTTP bör fungera. 

**Kan jag återställa eller uppdatera RTSP-källans URL i en diagram instans?**

Ja, när diagram instansen är i *inaktivt* läge.  

**Finns det en RTSP-simulator som kan användas vid testning och utveckling?**

Ja, en [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-modul är tillgänglig för användning i snabb starter och självstudier för att stödja inlärnings processen. Den här modulen tillhandahålls som bästa ansträngning och är kanske inte alltid tillgänglig. Vi rekommenderar starkt att du *inte* använder simulatorn i mer än några timmar. Du bör investera i testning med din faktiska RTSP-källa innan du planerar en produktions distribution.

**Finns det stöd för ONVIF-identifiering av IP-kameror vid nätverksgränsen?**

Nej, vi har inte stöd för att öppna ONVIF (Network Video Interface forum) för enheter på gränsen.

## <a name="streaming-and-playback"></a>Strömning och uppspelning

**Kan jag spela upp till gångar som registrerats för Azure Media Services från gränsen med hjälp av strömmande tekniker som HLS eller tank streck?**

Ja. Du kan strömma registrerade till gångar som vilken annan till gång som helst i Azure Media Services. För att strömma innehållet måste du ha en slut punkt för direkt uppspelning skapad och i körnings läge. Genom att använda standard processen för att skapa streaming-direktuppspelning får du åtkomst till en Apple-HTTP Live Streaming (HLS) eller dynamisk anpassningsbar strömning via HTTP (bindestreck, även kallat MPEG-streck) för strömning till alla kompatibla Player-ramverk. Mer information om hur du skapar och publicerar HLS-eller streck manifest finns i [dynamisk paketering](../latest/dynamic-packaging-overview.md).

**Kan jag använda standard innehålls skyddet och DRM-funktionerna i Media Services på en arkiverad till gång?**

Ja. Alla funktioner för dynamisk dynamisk kryptering och Digital Rights Management (DRM) är tillgängliga för användning på till gångar som har registrerats från ett medie diagram.

**Vilka spelare kan jag använda för att visa innehåll från de inspelade resurserna?**

Alla standard spelare som stöder kompatibel HLS version 3 eller version 4 stöds. Dessutom stöds även alla spelare som stöder uppspelning av MPEG-streck.

Rekommenderade spelare för testning är:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka-spelare](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple Native HTTP Live Streaming](https://developer.apple.com/streaming/)
* Edge-, Chrome-eller Safari inbyggd HTML5-videospelare
* Kommersiella spelare som stöder HLS-eller streck uppspelning

**Vilka är gränserna för strömning av en Media diagram-till gång?**

Direkt uppspelning av en levande eller inspelad till gång från ett medie diagram använder samma storskaliga infrastruktur-och strömnings slut punkter som Media Services stöder för strömning på begäran och live-strömning för media & underhållning, över de främsta (OTT) och sändnings kunderna. Det innebär att du snabbt och enkelt kan aktivera Azure Content Delivery Network, Verizon eller Akamai för att leverera ditt innehåll till en mål grupp så litet som några visnings program eller upp till miljon tals beroende på ditt scenario.

Du kan leverera innehåll genom att använda antingen Apple HLS eller MPEG-tank streck.

## <a name="design-your-ai-model"></a>Utforma din AI-modell 

**Jag har flera AI-modeller inkapslade i en Docker-behållare. Hur ska jag använda dem med live video analys?** 

Lösningarna varierar beroende på vilket kommunikations protokoll som används av inferencing-servern för att kommunicera med live video analys. I följande avsnitt beskrivs hur varje protokoll fungerar.

*Använd HTTP-protokollet*:

* Enskild behållare (enskild lvaExtension):  

   I din inferencing-Server kan du använda en enda port men olika slut punkter för olika AI-modeller. För ett python-exempel kan du till exempel använda olika `route` s per modell, som du ser här: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   I din live video analys-distribution, när du instansierar grafer, anger du URL: en för härlednings servern för varje instans, som du ser här: 

   första instans: URL för härlednings Server =`http://lvaExtension:44000/score/face_detection`<br/>
   andra instans: URL för härlednings Server =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Du kan också exponera dina AI-modeller på olika portar och anropa dem när du instansierar grafer.  

* Flera behållare: 

   Varje behållare distribueras med ett annat namn. Tidigare visade vi i dokumentationen för Live Video Analytics hur du distribuerar ett tillägg som heter *lvaExtension*. Nu kan du utveckla två olika behållare, var och en med samma HTTP-gränssnitt, vilket innebär att de har samma `/score` slut punkt. Distribuera dessa två behållare med olika namn och se till att båda lyssnar på *olika portar*. 

   Till exempel lyssnar en behållare med namnet `lvaExtension1` efter porten `44000` och en andra behållare som heter `lvaExtension2` lyssnar efter porten `44001` . 

   I din Live Video Analytics-topologi instansierar du två grafer med olika härlednings-URL: er, som du ser här: 

   Första instans: URL för härlednings Server = `http://lvaExtension1:44001/score`    
   Andra instans: URL för härlednings Server = `http://lvaExtension2:44001/score`
   
*Använd gRPC-protokollet*: 

* När du använder en gRPC-protokoll (Real-Purpose Remote Procedure Call) i live video analys-1,0 modulen är det enda sättet att göra detta är om gRPC-servern exponerar olika AI-modeller via olika portar. I [det här kod exemplet](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)visar en enskild port, 44000, alla Yolo-modeller. I teorin kunde Yolo gRPC-servern skrivas om för att visa vissa modeller på port 44000 och andra på port 45000. 

* Med Live Video Analytics module 2,0 läggs en ny egenskap till i noden gRPC-tillägg. Den här egenskapen, **extensionConfiguration**, är en valfri sträng som kan användas som en del av gRPC-kontraktet. Om du har flera AI-modeller som paketeras i en enda härlednings server behöver du inte exponera en nod för varje AI-modell. I stället kan du, för en diagram förekomst, som tilläggs leverantör definiera hur du vill välja de olika AI-modellerna med hjälp av egenskapen **extensionConfiguration** . Under körningen skickar Live Video Analytics den här strängen till inferencing-servern, som kan använda den för att anropa den önskade AI-modellen. 

**Jag skapar en gRPC-Server runt en AI-modell, och jag vill kunna stödja användningen av flera kameror eller diagram instanser. Hur ska jag bygga min server?** 

 Se först till att servern kan hantera mer än en begäran i taget eller arbeta i parallella trådar. 

Till exempel har ett standard antal parallella kanaler angetts i följande [exempel på live video analys gRPC](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py): 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

I föregående instans av gRPC-servern kan servern bara öppna tre kanaler åt gången per kamera, eller per diagram sto pol instans. Försök inte att ansluta fler än tre instanser till servern. Om du försöker öppna fler än tre kanaler väntar förfrågningar tills en befintlig kanal sjunker.  

Föregående implementering av gRPC-servern används i våra python-exempel. Som utvecklare kan du implementera en egen server eller använda den föregående standard implementeringen för att öka arbets numret, som du anger till det antal kameror som ska användas för video flöden. 

Om du vill konfigurera och använda flera kameror kan du skapa en instans av flera diagram sto pol Ogin, var och en som pekar på samma eller en annan härlednings Server (till exempel den server som nämns i föregående stycke). 

**Jag vill kunna ta emot flera ramar från överordnade innan jag fattar ett inferencing-beslut. Hur kan jag aktivera det?** 

Våra aktuella [standard exempel](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) fungerar i *tillstånds löst* läge. De behåller inte statusen för föregående anrop eller till och med vem som anropade. Det innebär att flera instanser av topologier kan anropa samma härlednings Server, men servern kan inte särskilja vem som anropar eller tillstånd per anropare. 

*Använd HTTP-protokollet*:

Om du vill behålla tillstånd, varje anropare eller graf, anropar du inferencing-servern med hjälp av HTTP-Frågeparametern som är unik för anroparen. Till exempel visas URL-adresserna för härlednings servern för varje instans här:  

instans av första topologi = `http://lvaExtension:44000/score?id=1`<br/>
instans av andra topologin = `http://lvaExtension:44000/score?id=2`

… 

På Server sidan vet Poäng vägen vem som anropar. Om ID = 1, kan den vara i enskilt tillstånd för den anroparen eller diagram sto pol instansen. Du kan sedan behålla de mottagna video bild rutorna i en buffert. Du kan till exempel använda en matris eller en ord lista med en DateTime-nyckel och värdet är ramen. Du kan sedan definiera den server som ska bearbetas (Härled) efter *x* -antalet ramar tas emot. 

*Använd gRPC-protokollet*: 

I ett gRPC-tillägg är varje session för en enda Camera-feed, så det finns inget behov av att ange ett ID. Med egenskapen extensionConfiguration kan du nu lagra video bild rutorna i en buffert och definiera den server som ska bearbetas (Härled) efter *x* -antalet ramar tas emot. 

**Kör alla ProcessMediaStreams på en viss behållare samma AI-modell?** 

Nej. Starta eller stoppa anrop från slutanvändaren i en diagram instans, eller så kanske det finns en kamera som kopplar från eller återansluter. Målet är att spara en session om kameran strömmar video. 

* Två kameror som skickar video för bearbetning skapar två sessioner. 
* En kamera som går till en graf som har två gRPC-tillägg skapar två sessioner. 

Varje session är en full duplex-anslutning mellan live video analys och gRPC-servern och varje session kan ha en annan modell eller pipeline. 

> [!NOTE]
> Om en kamera kopplar från eller återansluter, och kameran försätts i offlineläge under en period utanför tolerans gränserna, öppnas en ny session med gRPC-servern i live video analys. Det finns inget krav på att servern ska spåra status mellan dessa sessioner. 

Live Video Analytics ger också stöd för flera gRPC-tillägg för en enskild kamera i en diagram instans. Du kan använda dessa gRPC-tillägg för att utföra AI-bearbetningen sekventiellt, parallellt eller som en kombination av båda. 

> [!NOTE]
> Att flera tillägg körs parallellt påverkar dina maskin varu resurser. Tänk på detta när du väljer maskin vara som passar dina beräknings behov. 

**Vilket är det maximala antalet samtidiga ProcessMediaStreams?** 

Live Video Analytics använder inga begränsningar för det här talet.  

**Hur kan jag avgöra om min inferencing-Server ska använda CPU eller GPU eller någon annan maskin varu Accelerator?** 

Ditt beslut är beroende av den utvecklade AI-modellens komplexitet och hur du vill använda processor-och maskin varu acceleratorer. När du utvecklar AI-modellen kan du ange vilka resurser som modellen ska använda och vilka åtgärder som ska utföras. 

**Hur gör jag för att lagra bilder med markerings ramar efter bearbetning?** 

Idag tillhandahåller vi gränser för avgränsnings rutor enbart som härlednings meddelanden. Du kan bygga en anpassad MJPEG-dataström som kan använda dessa meddelanden och täcka markerings rutorna i video bild rutorna.  

## <a name="grpc-compatibility"></a>gRPC-kompatibilitet 

**Hur vet jag vilka obligatoriska fält för media Stream-beskrivningen är?** 

Alla fält som du inte anger något värde till får ett [standardvärde som anges av gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

I real tids analys används *proto3* -versionen av protokollets buffert språk. Alla protokollets buffertar som används av Live Video Analytics-kontrakt är tillgängliga i [protokollets buffer-filer](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

**Hur kan jag se till att jag använder de senaste Protocol-buffertarna?** 

Du kan hämta de senaste protokollets buffertar på [webbplatsen för Contract-filer](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). När vi uppdaterar filerna finns de på den här platsen. Det finns ingen omedelbar plan för att uppdatera protokoll filen, så leta efter paket namnet högst upp i filerna för att känna till versionen. Den bör läsa: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Eventuella uppdateringar av de här filerna ökar "v-värdet" i slutet av namnet. 

> [!NOTE]
> Eftersom Real video analys använder proto3-versionen av språket är fälten valfria och-versionen är bakåtkompatibel och vidarebefordras. 

**Vilka gRPC-funktioner är tillgängliga för mig att använda med live video analys? Vilka funktioner är obligatoriska och vilka är de valfria?** 

Du kan använda alla gRPC-funktioner på Server sidan, förutsatt att protobuf-kontraktet (Protocol buffer) är uppfyllt. 

## <a name="monitoring-and-metrics"></a>Övervakning och mått

**Kan jag övervaka medie diagrammet på gränsen genom att använda Azure Event Grid?**

Ja. Du kan använda Prometheus mått och publicera dem i Event-rutnätet. 

**Kan jag använda Azure Monitor för att Visa hälso tillstånd, mått och prestanda för mina medie diagram i molnet eller på gränsen?**

Ja, vi har stöd för den här metoden. Mer information finns i [Översikt över Azure Monitor mått](../../azure-monitor/essentials/data-platform-metrics.md).

**Finns det några verktyg för att göra det enklare att övervaka modulen Media Services IoT Edge?**

Visual Studio Code stöder tillägget Azure IoT tools, där du enkelt kan övervaka slut punkterna i LVAEdge-modulen. Du kan använda det här verktyget för att snabbt komma igång med att övervaka den inbyggda slut punkten för IoT Hub för "händelser" och Visa de härlednings meddelanden som dirigeras från gräns enheten till molnet. 

Dessutom kan du använda det här tillägget för att redigera modulen för LVAEdge-modulen för att ändra inställningarna för media Graph.

Mer information finns i artikeln om [övervakning och loggning](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fakturering och tillgänglighet

**Hur fungerar Real video analys på IoT Edge faktureras?**

Information om fakturering finns i [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Nästa steg

[Snabb start: kom igång med live video analys på IoT Edge](get-started-detect-motion-emit-events-quickstart.md)