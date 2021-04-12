---
title: Video analys i real tid för IoT Edge viktig information – Azure
description: Det här avsnittet innehåller viktig information om real tids analys av IoT Edge-versioner, förbättringar, fel korrigeringar och kända problem.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: f130b93b8d799c371a640f2b29c22c0d77834cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954403"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Viktig information om live video analys i IoT Edge

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` i din RSS-feed läsare.

Den här artikeln innehåller information om:

* De senaste versionerna
* Kända problem
* Felkorrigeringar
* Föråldrade funktioner

<hr width=100%>

## <a name="january-12-2021"></a>12 januari 2021

Den här versions tag gen är för uppdateringen januari 2021 för modulen:

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> I snabb starterna och självstudierna använder distributions manifestet en tagg på 2 (Real-Video-Analytics: 2). Det innebär att bara att omdistribuera sådana manifest bör uppdatera modulen på din gräns > enheter.
### <a name="bug-fixes"></a>Felkorrigeringar 

* Fälten `ActivationSignalOffset` , `MinimumActivationTime` och `MaximumActivationTime` i signal grind processorer angavs felaktigt som obligatoriska egenskaper. De är nu **valfria** egenskaper.
* Åtgärdat ett användnings fel som gör att live video analys på IoT Edge modul kraschar när den distribueras i vissa regioner.

<hr width=100%>

## <a name="december-14-2020"></a>14 december 2020
Den här versionen är den offentliga för hands versionen av video analys på IoT Edge. Versions tag gen är

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>Uppdatera modul
* Stöd har lagts till för användning av fler än en HTTP-tilläggs processor och gRPC förlängnings processor per diagram-topologi.
* Stöd har lagts till för [disk utrymmes hantering för Sink-noder](upgrading-lva-module.md#disk-space-management-with-sink-nodes).
* `MediaGraphGrpcExtension` Node stöder nu [extensionConfiguration](grpc-extension-protocol.md) -egenskapen för att använda flera AI-modeller på en enda gRPC-Server.
* Stöd för insamling av mått för Live Video Analytics-modul i [Prometheus-format](https://prometheus.io/docs/practices/naming/)har lagts till. Läs mer om hur du [samlar in mått och vyer i Azure Monitor.](monitoring-logging.md#azure-monitor-collection-via-telegraf) 
* Möjligheten att filtrera utdata har lagts till. Du kan bara skicka **ljud-** eller **video-** eller **ljud-och video filmer** med hjälp av `outputSelectors` till en graf-nod. 
* Filter processor för RAM hastighet är **inaktuell**.  
    * Ram frekvens hantering är nu tillgängligt i själva diagram tillägget processor.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-tillägg
* Lanserade [video analyser på IoT Edge](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) – ett Visual Studio Code-tillägg som hjälper dig att hantera lva-medie diagram. Det här tillägget fungerar med **LVA 2,0-modulen** och erbjuder redigering och hantering av medie diagram med ett slimmat och lättanvänt grafiskt gränssnitt.
## <a name="september-22-2020"></a>Den 22 september 2020

Den här versions tag gen är för uppdateringen september 2020 för modulen:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> I snabb starterna och självstudierna använder distributions manifestet en tagg på 1 (Real-Video-Analytics: 1). Det innebär att bara att omdistribuera sådana manifest bör uppdatera modulen på din gräns > enheter.

### <a name="module-updates"></a>Uppdatera modul

* En ny [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) -nod, som är tillgänglig för integrering med modulen för [spatial analys](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(för hands version) från Cognitive Services.
* Stöd har lagts till för Linux ARM64-enheter – Använd [manuella steg](deploy-iot-edge-device.md) för att distribuera till sådana enheter.

### <a name="documentation-updates"></a>Dokumentationsuppdateringar

* Det finns [instruktioner](deploy-azure-stack-edge-how-to.md) för att använda real tids analys på IoT Edge på Azure Stack gräns enheter.
* Ny självstudie om att utveckla scenario-speciella modeller för visuellt innehåll med hjälp av [Custom vision tjänsten](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) och att använda den för att [analysera direktsänd video](custom-vision-tutorial.md) i real tid.

<hr width=100%>

## <a name="august-19-2020"></a>19 augusti 2020

Den här versions tag gen är för uppdateringen augusti 2020 för modulen:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> I snabb starterna och självstudierna använder distributions manifestet en tagg på 1 (Real-Video-Analytics: 1). Det innebär att bara att omdistribuera sådana manifest bör uppdatera modulen på din gräns > enheter.

### <a name="module-updates"></a>Uppdatera modul

* Du kan nu få hög data överförings prestanda mellan direktsända video analyser på IoT Edge och ditt anpassade tillägg med gRPC Framework. Kom igång genom att läsa [snabb](analyze-live-video-use-your-grpc-model-quickstart.md) starten.
* En bredare regional distribution av Real video analys och endast moln tjänsten har uppdaterats.  
* Live Video Analytics är nu tillgängligt i 25 fler regioner över hela världen. Här är [listan](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) över alla tillgängliga regioner.  
* [Inställningarna](https://aka.ms/lva-edge/setup-resources-for-samples) för snabb start har också uppdaterats med stöd för nya regioner.
    * Det finns inget anrop till åtgärden för alla som redan har konfigurerat resurser

### <a name="bug-fixes"></a>Felkorrigeringar 

* Ta bort användningen av ett föråldrat Azure-tillägg i installations skriptet

<hr width=100%>

## <a name="july-13-2020"></a>13 juli 2020

Den här versions tag gen är för uppdatering juli 2020 för modulen:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> I snabb starterna och självstudierna använder distributions manifestet en tagg på 1 (Real-Video-Analytics: 1). Det innebär att bara att omdistribuera sådana manifest bör uppdatera modulen på din gräns > enheter.

### <a name="module-updates"></a>Uppdatera modul

* Nu kan du skapa diagram-topologier som har en noden till gångs mottagare och en filsink-nod för en signal grind processor. Se [topologin](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) för ett exempel.

### <a name="bug-fixes"></a>Felkorrigeringar

* Förbättringar av validering av önskade egenskaper

<hr width=100%>

## <a name="june-1-2020"></a>Den 1 juni 2020

Den här versionen är den första offentliga för hands versionen av video analys på IoT Edge. Versions tag gen är

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funktioner som stöds

* Analysera direktuppspelade video strömmar med AI-moduler som du väljer och spela in video på gräns enheten eller i molnet
* Använd på Linux AMD64-operativ system som [stöds](../../iot-edge/support.md) av IoT Edge
* Distribuera och konfigurera modulen via IoT Hub med Azure Portal eller Visual Studio Code
* Hantera [diagram-topologier och diagram instanser](media-graph-concept.md#media-graph-topologies-and-instances) via fjärr anslutning eller lokalt genom följande direkta metod anrop

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Nästa steg

[Översikt](overview.md)
