---
title: Felsöka live video analys på IoT Edge – Azure
description: Den här artikeln beskriver fel söknings steg för video analys i real tid för IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d766843f58bc2cdd0dcdddfad337b23fefb28768
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698747"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Felsöka live video analys på IoT Edge

Den här artikeln beskriver fel söknings steg för LVA (Live Video Analytics) på Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

### <a name="diagnostics"></a>Diagnostik

Som en del av din Live Video Analytics-distribution konfigurerar du Azure-resurser som IoT Hub och IoT Edge enheter. Som ett första steg för att diagnostisera problem bör du alltid kontrol lera att gräns enheten är korrekt konfigurerad genom att följa dessa anvisningar:

1. [Kör `check` kommandot](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Kontrol lera IoT Edge-versionen](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Kontrol lera status för IoT Edge Security Manager och dess loggar](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Visa meddelanden som går via IoT Edge hubben](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Starta om behållare](../../iot-edge/troubleshoot.md#restart-containers).
1. [Kontrol lera brand Väggs-och port konfigurations reglerna](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>För distributions problem

Om gräns infrastrukturen är fin kan du söka efter problem med distributions manifest filen. Om du vill distribuera live video analys på IoT Edge modul på IoT Edge enheten tillsammans med andra IoT-moduler, använder du ett distributions manifest som innehåller IoT Edge Hub, IoT Edge agent och andra moduler och deras egenskaper. Du kan använda följande kommando för att distribuera manifest filen:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Om JSON-koden inte är korrekt utformad kan du få följande fel meddelande:   
&nbsp;&nbsp;&nbsp;**Det gick inte att parsa JSON från filen: ' <deployment manifest.json> ' för argumentet Content ' med undantaget: "extra data: rad 101 kolumn 1 (Char 5325)"**

Om det här felet uppstår rekommenderar vi att du kontrollerar JSON för saknade hakparenteser eller andra problem med filens struktur. Om du vill verifiera fil strukturen kan du använda en-klient, till exempel [plugin-programmet för anteckningar + + med JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) eller ett online-verktyg som [JSON-formaterare & verifieraren](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Under distributionen: diagnostisera med Media Graph Direct-metoder 

När live video analys på IoT Edge modul har distribuerats korrekt på IoT Edge enheten, kan du skapa och köra medie diagrammet genom att anropa [direkta metoder](direct-methods.md).  
>[!NOTE]
>  Direkta metod anrop ska endast göras till **`lvaEdge`** modulen.

Du kan använda Azure Portal för att köra en diagnos av medie grafen med hjälp av direkta metoder:

1. I Azure Portal går du till IoT-hubben som är ansluten till din IoT Edge-enhet.

1. Leta efter **Automatisk enhets hantering** och välj sedan **IoT Edge**.  

1. I listan med gräns enheter väljer du den enhet som du vill diagnostisera.  
         
    ![Skärm bild av Azure Portal som visar en lista över gräns enheter](./media/troubleshoot-how-to/lva-sample-device.png)


1. Kontrol lera om svars koden är *200-OK*. Andra svars koder för [IoT Edge runtime](../../iot-edge/iot-edge-runtime.md) är:
    * 400-distributions konfigurationen är felaktig eller ogiltig.
    * 417 – enheten har ingen distributions konfigurations uppsättning.
    * 412-schema versionen i distributions konfigurationen är ogiltig.
    * 406 – den IoT Edge enheten är offline eller skickar inte status rapporter.
    * 500 – ett fel uppstod i IoT Edge Runtime.

    > [!TIP]
    > Om du får problem med att köra Azure IoT Edge moduler i din miljö använder du **[Azure IoT Edge standard diagnostiska steg](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** som en guide för fel sökning och diagnostik.
### <a name="post-deployment-direct-method-error-code"></a>Efter distribution: direkt metod felkod
1. Om du får en status `501 code` kontrollerar du att namnet på den direkta metoden är korrekt. Om metod namnet och nytto lasten för begäran är korrekta bör du få resultat tillsammans med lyckad kod = 200. 
1. Om nytto lasten för begäran är felaktig får du en status `400 code` och en nytto last som visar felkoden och meddelandet som ska hjälpa till att diagnostisera problemet med ditt direkta metod anrop.
    * Genom att kontrol lera rapporter och önskade egenskaper kan du förstå om modulens egenskaper har synkroniserats med distributionen. Om de inte är det kan du starta om IoT Edge-enheten. 
    * Använd den [direkta metod](direct-methods.md) guiden för att anropa några metoder, särskilt enkla som GraphTopologyList. I guiden anges även förväntade nytto laster och felkoder för begäran och svar. När de enkla direkta metoderna har genomförts kan du vara säker på att live video analys IoT Edge modulen fungerar som den ska.
        
       ![Skärm bild av rutan "direkt metod" för IoT Edge-modulen.](./media/troubleshoot-how-to/direct-method.png) 

1. Om den **angivna distributionen** och **rapporteras av enhets** kolumnerna indikerar *Ja*, kan du anropa direkt metoder i live video analys på IoT Edge modul. Välj modulen för att gå till en sida där du kan kontrol lera önskade och rapporterade egenskaper och anropa direkta metoder. Tänk på följande: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Efter distribution: diagnostisera loggar för problem under körningen 

Behållar loggarna för din IoT Edge-modul bör innehålla diagnostikinformation som hjälper dig att felsöka dina problem under modulens körning. Du kan [kontrol lera behållar loggar för problem](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) och själv diagnostisera problemet. 

Om du har kört alla föregående kontroller och fortfarande stöter på problem samlar du in loggar från IoT Edge-enheten [med `support bundle` kommandot](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) för ytterligare analys av Azure-teamet. Du kan [kontakta oss](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) för support och skicka in de insamlade loggarna.

## <a name="common-error-resolutions"></a>Lösningar för vanliga fel

Live Video Analytics distribueras som en IoT Edge modul på IoT Edge enheten och fungerar tillsammans med IoT Edge agent-och hubb-moduler. Några av de vanliga fel som uppstår när du distribuerar live video analys orsakas av problem med den underliggande IoT-infrastrukturen. Felen är:

* [IoT Edge agenten stannar efter ungefär en minut](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [IoT Edge agenten kan inte komma åt en moduls avbildning (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [IoT Edge agent-modulen rapporterar "Tom konfigurations fil" och inga moduler startar på enheten](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Det gick inte att starta IoT Edge Hub](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [IoT Edge Security daemon Miss lyckas med ett ogiltigt värdnamn](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Live Video Analytics eller någon annan anpassad IoT Edge modul kan inte skicka ett meddelande till Edge Hub med 404-fel](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [IoT Edge modulen har distribuerats och försvinner sedan från enheten](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Om du får problem med att köra Azure IoT Edge moduler i din miljö använder du **[Azure IoT Edge standard diagnostiska steg](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** som en guide för fel sökning och diagnostik.

Du kan också stöta på problem när du kör **[installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)**. Några vanliga problem är:

* Använd en prenumeration där du inte har ägar behörighet. Detta gör att skriptet Miss fungerar med ett **ForbiddenError** eller ett **AuthorizationFailed** -fel.
    * Du kommer förbi det här problemet genom att se till att du har **ägar** behörighet till den prenumeration som du planerar att använda. Om du inte kan göra det själv kan du kontakta prenumerations administratören för att ge rätt behörighet.
* **Mallen kunde inte distribueras på grund av en princip överträdelse.**
    * Undvik det här problemet genom att kontakta IT-administratören för att se till att anropen för att skapa en virtuell dator för att kringgå blockering av SSH-autentisering. Detta behövs inte eftersom vi använder ett säkert skydds-nätverk som kräver ett användar namn och lösen ord för att kommunicera med Azure-resurserna. Autentiseringsuppgifterna lagras i filen **~/clouddrive/lva-sample/vm-edge-device-credentials.txt** i Cloud Shell, när den virtuella datorn har skapats, distribuerats och anslutits till IoT Hub.
* Installations skriptet kan inte skapa tjänstens huvud namn och/eller Azure-resurser.
    * För att komma förbi det här problemet bör du kontrol lera att din prenumeration och Azure-klienten inte har nått sin maximala tjänst gräns. Lär dig mer om [Azure AD-tjänstens gränser och begränsningar](../../active-directory/enterprise-users/directory-service-limits-restrictions.md) samt [Azure-prenumerationer, tjänst begränsningar, kvoter och begränsningar.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

> [!TIP]
> Om det finns några ytterligare problem som du kan behöva hjälp med kan du **[samla in loggar och skicka in ett support ärende](#collect-logs-for-submitting-a-support-ticket)**. Du kan också kontakta oss genom att skicka oss ett e-postmeddelande till **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** .
### <a name="live-video-analytics-working-with-external-modules"></a>Live video analys fungerar med externa moduler

Live video analys via medie graphs tilläggs processorer kan utöka medie grafen för att skicka och ta emot data från andra IoT Edge moduler genom att använda HTTP-eller gRPC-protokoll. Som ett [särskilt exempel](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)kan det här medie diagrammet skicka video bild rutor som bilder till en extern härlednings modul som Yolo v3 och ta emot JSON-baserade analys resultat med http-protokollet. I en sådan topologi är målet för händelserna främst IoT-hubben. I situationer där du inte ser uthärlednings händelser i hubben, kontrol lera följande:

* Kontrol lera om navet som Media Graph publicerar till och navet du undersöker är desamma. När du skapar flera distributioner kan du få flera hubbar och kontrol lera fel hubb för händelser.
* I Azure Portal kontrollerar du om den externa modulen är distribuerad och körs. I exempel bilden här är rtspsim, yolov3, tinyyolov3 och logAnalyticsAgent IoT Edge moduler som körs utanför modulen lvaEdge.

    [![Skärm bild som visar körnings status för moduler i Azure IoT Hub. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Kontrol lera om du skickar händelser till rätt URL-slutpunkt. Den externa AI-behållaren exponerar en URL och en port som tar emot och returnerar data från POST-begäranden. Den här URL-adressen anges som en `endpoint: url` egenskap för HTTP-tilläggs processorn. Som det visas i [topologins URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json), anges slut punkten som inferencing URL-parameter. Kontrol lera att standardvärdet för parametern eller det skickade värdet är korrekt. Du kan testa för att se om det fungerar genom att använda klientens URL (sväng).  

    Här är ett exempel en Yolo v3-behållare som körs på den lokala datorn med IP-adressen 172.17.0.3.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Returnerat resultat:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Använd **[Docker Undersök kommando](https://docs.docker.com/engine/reference/commandline/inspect/)** för att hitta DATORns IP-adress.
    
* Om du kör en eller flera instanser av en graf som använder Media graphs förlängnings processor, bör du använda `samplingOptions` fältet för att hantera video flödets hastighet för bild rutor per sekund (FPS). 

   * I vissa situationer, där processorn eller minnet för Edge-datorn är mycket utnyttjad, kan du förlora vissa härlednings händelser. Du löser problemet genom att ange ett lågt värde för `maximumSamplesPerSecond` egenskapen i `samplingOptions` fältet. Du kan ställa in den på 0,5 ("maximumSamplesPerSecond": "0,5") på varje instans av grafen och sedan köra instansen igen för att kontrol lera om det finns några utgångs händelser på hubben.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Flera direkta metoder parallellt – timeout-problem 

Live video analys på IoT Edge ger en direkt metod baserad programmerings modell som gör att du kan ställa in flera topologier och flera diagram instanser. Som en del av installationen av topologin och grafen anropar du flera direkta metod anrop i IoT Edge-modulen. Om du anropar dessa flera metod anrop parallellt, särskilt de som startar och stoppar graferna, kan det uppstå ett tids gräns fel som följande: 

Initierings metoden för sammansättningen Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync utlöste ett undantag. Microsoft. Azure. devices. Common. Exceptions. IotHubException: Microsoft. Azure. devices. Common. Exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Vi rekommenderar att du *inte* anropar direkta metoder parallellt. Anropa dem i tur och ordning (det vill säga skapa ett direkt metod anrop endast efter att det föregående är klart).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Samla in loggar för att skicka in ett support ärende

När självgående fel söknings steg inte löser problemet kan du gå till Azure Portal och [öppna ett support ärende](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Loggarna kan innehålla personligt identifierbar information (PII), till exempel din IP-adress. Alla lokala kopior av loggarna tas bort så snart vi slutförde undersökningen och stänger support ärendet.  

Om du vill samla in relevanta loggar som ska läggas till i biljetten följer du anvisningarna nedan och laddar upp loggfilerna i **informations** fönstret i support förfrågan.  
1. [Konfigurera Live Video Analytics-modulen för att samla in utförliga loggar](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Aktivera fel söknings loggar](#live-video-analytics-debug-logs)
1. Återskapa problemet
1. Ansluta till den virtuella datorn från sidan **IoT Hub** i portalen
    1. Zip alla filer i mappen *debugLogs*

       > [!NOTE]
       > Dessa loggfiler är inte avsedda för själv diagnostisering. De är avsedda för Azures teknik team för att analysera dina problem.

       * I följande kommando ska du se till att ersätta **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** med platsen för fel söknings loggarna på gräns enheten som du konfigurerade tidigare i **steg 2**.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Bifoga *debugLogs.zip* -filen till support ärendet.
1. Kör [kommandot support paket](#use-the-support-bundle-command), samla in loggarna och Anslut till support ärendet.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Konfigurera Live Video Analytics-modulen för att samla in utförliga loggar
Konfigurera din live video analys-modul för att samla in utförliga loggar genom att ställa in `logLevel` och `logCategories` enligt följande:
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Du kan göra detta i något av följande:
* I **Azure Portal**, genom att uppdatera modulens identitets dubbla egenskaper för den Live Video Analytics module-modulens   [ ![ identitet ](media/troubleshoot-how-to/module-twin.png) dubbla.](media/troubleshoot-how-to/module-twin.png#lightbox)    
* Eller i **distributions manifest** filen kan du lägga till dessa poster i noden egenskaper i modulen live video analys

### <a name="use-the-support-bundle-command"></a>Använda kommandot support-bunt

När du behöver samla in loggar från en IoT Edge-enhet är det enklaste sättet att använda `support-bundle` kommandot. Det här kommandot samlar in:

- Modul loggar
- IoT Edge Security Manager och behållar motor loggar
- IoT Edge kontrol lera JSON-utdata
- Användbar felsöknings information

1. Kör `support-bundle` kommandot med flaggan *--efter* flagga för att ange hur lång tid du vill att dina loggar ska gälla. Till exempel får 2H loggar för de senaste två timmarna. Du kan ändra värdet för den här flaggan om du vill inkludera loggar för olika perioder.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Det här kommandot skapar en fil med namnet *support_bundle.zip* i katalogen där du körde kommandot. 
   
1. Bifoga *support_bundle.zip* -filen till support ärendet.

### <a name="live-video-analytics-debug-logs"></a>Real tids fel söknings loggar för video analys

Gör så här för att konfigurera live video analys på IoT Edge modul för att generera fel söknings loggar:

1. Logga in på [Azure Portal](https://portal.azure.com)och gå till din IoT-hubb.
1. I den vänstra rutan väljer du **IoT Edge**.
1. I listan med enheter väljer du ID för mål enheten.
1. Längst upp i fönstret väljer du **Ange moduler**.

   ![Skärm bild av knappen Ange moduler i Azure Portal.](media/troubleshoot-how-to/set-modules.png)

1. I avsnittet **IoT Edge moduler** söker du efter och väljer **lvaEdge**.
1. Välj **alternativ för att skapa behållare**.
1. I avsnittet **bindningar** lägger du till följande kommando:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Det här kommandot binder mapparna loggfiler mellan gräns enheten och behållaren. Om du vill samla in loggarna på en annan plats använder du följande kommando och ersätter **$LOG _LOCATION_ON_EDGE_DEVICE** med den plats som du vill använda: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Välj **Uppdatera**.
1. Välj **Granska + skapa**. Ett lyckat verifierings meddelande har publicerats under en grön banderoll.
1. Välj **Skapa**.
1. Uppdatera **modulens identitet** så att den pekar på parametern DebugLogsDirectory, som pekar på den katalog där loggarna samlas in:

    a. Under tabellen **moduler** väljer du **lvaEdge**.  
    b. Längst upp i fönstret väljer du Modulnamn, med **dubbla**. Ett redigerbart fönster öppnas.  
    c. Lägg till följande nyckel/värde-par under **önskad nyckel**:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Det här kommandot binder mapparna loggfiler mellan gräns enheten och behållaren. Om du vill samla in loggar på en annan plats på enheten:
    > 1. Skapa en bindning för fel söknings logg platsen i avsnittet **bindningar** och ersätt **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** och **$Debug _LOG_LOCATION** med den plats som du vill använda: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Använd följande kommando och Ersätt **$DEBUG _LOG_LOCATION** med den plats som användes i föregående steg:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Välj **Spara**.


1. Du kan stoppa logg insamling genom att ange värdet i **modulens identitet** , till *Null*. Gå tillbaka till sidan för **modulens identitet med dubbla** och uppdatera följande parameter som:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Metod tips kring loggning

[Övervakning och loggning](monitoring-logging.md) bör hjälpa till med att förstå taxonomin och hur du genererar loggar som kan hjälpa till med fel sökning av problem med lva. 

Eftersom gRPC Server-implementering skiljer sig mellan olika språk, finns det inget standardiserat sätt att lägga till loggning inuti på servern.  

Om du till exempel skapar en gRPC-server med .NET Core lägger gRPC till loggar i kategorin **gRPC** . Om du vill aktivera detaljerade loggar från gRPC konfigurerar du Grpc-prefixen till fel söknings nivån i appsettings.jspå filen genom att lägga till följande objekt i avsnittet LogLevel i loggning: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Du kan också konfigurera detta i Startup.cs-filen med ConfigureLogging: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[Loggning och diagnostik i gRPC i .net](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) ger viss vägledning för att samla in vissa diagnostikloggar från en gRPC-Server. 

### <a name="a-failed-grpc-connection"></a>En misslyckad gRPC-anslutning 

Om en graf är aktiv och strömmas från en kamera underhålls anslutningen av Live Video Analytics. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Övervaka och utjämna belastningen på CPU-och GPU-resurser när dessa resurser blir Flask halsar

Live Video Analytics övervakar eller tillhandahåller inte maskin varu resurs övervakning. Utvecklare måste använda övervaknings lösningarna för maskin varu tillverkare. Om du använder Kubernetes-behållare kan du dock övervaka enheten med hjälp av [Kubernetes-instrumentpanelen](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

gRPC i .NET Core-dokument delar också lite värdefull information om [prestanda metod tips](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) och [belastnings utjämning](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing).  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Felsöka en härlednings server när den inte får några ramar och du får ett "okänt" protokoll fel 

Det finns flera saker du kan göra för att få mer information om problemet.  

* Ta med "**ediaPipeline** -logg kategori i önskade egenskaper för modulen live video analys och se till att logg nivån är inställd på `Information` .  
* Om du vill testa nätverks anslutningen kan du köra följande kommando från Edge-enheten. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Om kommandot matar ut en kort sträng med jumbled text, lyckades Telnet kunna öppna en anslutning till din härlednings Server och öppna en binär gRPC-kanal. Om du inte ser det kommer Telnet att rapportera ett nätverks fel. 
* Du kan aktivera ytterligare loggning i gRPC-biblioteket i din härlednings Server. Detta kan ge ytterligare information om själva gRPC-kanalen. Detta varierar beroende på språk, här är instruktioner för [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Plocka fler bilder från bufferten för gRPC utan att skicka tillbaka resultatet för första bufferten

Som en del av gRPC data överförings avtal, ska alla meddelanden som Live Video Analytics skickar till gRPC inferencing-servern bekräftas. Det går inte att bekräfta att en bild ram tar emot data kontraktet och kan leda till oönskade situationer.  

Om du vill använda gRPC-servern med Real video analys kan du använda delat minne för bästa prestanda. Detta kräver att du använder funktioner för delade Linux-minnen som exponeras av programmerings språket/miljön. 

1. Öppna den klusterdelade minnes referensen i Linux.
1. När du får en ram får du åtkomst till adress förskjutningen i det delade minnet.
1. Bekräfta slut för ande av ram bearbetning så att dess minne kan återtas av video analys i real tid.

   > [!NOTE]
   > Om du förväntar dig att bekräfta att en bild av ramen är i real tid, kan det leda till att det delade minnet blir fullt och orsaka att data släpps.
1. Lagra varje ram i en data struktur som du väljer (lista, matris osv.) på inferencing-servern.
1. Du kan sedan köra bearbetnings logiken när du har önskat antal bild ramar.
1. Returnera inferencing-resultatet till direktsänd video analys när det är klart.

## <a name="next-steps"></a>Nästa steg

[Självstudie: Event-baserad videoinspelning till molnet och uppspelningen från molnet](event-based-video-recording-tutorial.md)