---
title: Azure Stream Analytics på IoT Edge
description: Skapa Edge-jobb i Azure Stream Analytics och distribuera dem till enheter som kör Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 65f1ad93bf711f7f7efe95c38619390dde527dd0
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827248"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics på IoT Edge
 
Azure Stream Analytics på IoT Edge gör det möjligt för utvecklare att distribuera nära analys i real tid närmare IoT-enheter så att de kan låsa upp det fullständiga värdet av enhets genererade data. Azure Stream Analytics har utformats för korta svarstider, hög elasticitet, effektiv bandbreddsanvändning och bra efterlevnad. Företag kan distribuera kontroll logiken nära de industriella åtgärderna och komplettera stor data analys som gjorts i molnet.

Azure Stream Analytics på IoT Edge körs i [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) Framework. När jobbet har skapats i Stream Analytics kan du distribuera och hantera det med hjälp av IoT Hub.

## <a name="common-scenarios"></a>Vanliga scenarier

I det här avsnittet beskrivs vanliga scenarier för Stream Analytics i IoT Edge. I följande diagram visas data flödet mellan IoT-enheter och Azure-molnet.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="Diagram över IoT Edge på hög nivå":::

### <a name="low-latency-command-and-control"></a>Kommando och kontroll med låg latens

Tillverknings säkerhets system måste reagera på drift data med mycket låg latens. Med Stream Analytics på IoT Edge kan du analysera sensor data i nära real tid och utfärda kommandon när du identifierar avvikelser för att stoppa en dator eller utlösa aviseringar.

### <a name="limited-connectivity-to-the-cloud"></a>Begränsad anslutning till molnet

Verksamhets kritiska system, t. ex. fjärrutvinnings utrustning, anslutna fartyg eller offshore-borrning, behöver analysera och reagera på data även när moln anslutningen är intermittent. Med Stream Analytics körs din strömmande logik oberoende av nätverks anslutningen och du kan välja vad du skickar till molnet för vidare bearbetning eller lagring.

### <a name="limited-bandwidth"></a>Begränsad bandbredd

Mängden data som produceras av Jet-motorer eller anslutna bilar kan vara så stora att data måste filtreras eller förbehandlas innan de skickas till molnet. Med hjälp av Stream Analytics kan du filtrera eller aggregera de data som behöver skickas till molnet.

### <a name="compliance"></a>Efterlevnad

Efterlevnad av regler kan kräva att vissa data ska vara lokalt anonymiserats eller aggregerade innan de skickas till molnet.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-jobb i Azure Stream Analytics

Stream Analytics Edge-jobb körs i behållare som distribuerats till [Azure IoT Edge enheter](../iot-edge/about-iot-edge.md). Edge-jobb består av två delar:

* En moln del som är ansvarig för jobb definitionen: användarna definierar indata, utdata, fråga och andra inställningar, till exempel händelser som inte är i molnet.

* En modul som körs på dina IoT-enheter. Modulen innehåller Stream Analytics motor och tar emot jobb definitionen från molnet. 

Stream Analytics använder IoT Hub för att distribuera Edge-jobb till enhet (er). Mer information finns i [IoT Edge distribution](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Azure Stream Analytics Edge-jobb":::

## <a name="edge-job-limitations"></a>Begränsningar för Edge-jobb

Målet är att ha paritet mellan IoT Edge jobb och moln jobb. De flesta funktioner i SQL-frågespråket stöds både för Edge och molnet. Följande funktioner stöds dock inte för Edge-jobb:
* Användardefinierade funktioner (UDF) i Java Script. UDF är tillgänglig i [C# för IoT Edge jobb](./stream-analytics-edge-csharp-udf.md) (för hands version).
* Användardefinierade agg regeringar (UDA).
* Azure ML-funktioner.
* AVRO-format för indata/utdata. Just nu stöds endast CSV och JSON.
* Följande SQL-operatorer:
    * PARTITION AV
    * GetMetadataPropertyValue
* Princip för sent införsel

### <a name="runtime-and-hardware-requirements"></a>Körnings-och maskin varu krav
Om du vill köra Stream Analytics på IoT Edge behöver du enheter som kan köra [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Stream Analytics och Azure IoT Edge använda **Docker** -behållare för att tillhandahålla en portabel lösning som körs på flera värd operativ system (Windows, Linux).

Stream Analytics på IoT Edge görs tillgängligt som Windows-och Linux-avbildningar som körs på både x86-64-eller ARM-arkitekturer (Advanced RISC Machines). 


## <a name="input-and-output"></a>Indata och utdata

Stream Analytics Edge-jobb kan hämta indata och utdata från andra moduler som körs på IoT Edge enheter. Om du vill ansluta från och till vissa moduler kan du ange konfiguration för routning vid distribution. Mer information finns i [dokumentationen för IoT Edge module-sammansättning](../iot-edge/module-composition.md).

För både indata och utdata stöds CSV-och JSON-format.

För varje indata-och utdataström som du skapar i ditt Stream Analytics jobb skapas en motsvarande slut punkt i den distribuerade modulen. Dessa slut punkter kan användas i distributionens vägar.

Data ström indatatyper som stöds är:
* Edge Hub
* Händelsehubb
* IoT Hub

De utdatatyper som stöds är:
* Edge Hub
* SQL Database
* Händelsehubb
* Blob Storage/ADLS Gen2

Referens indata stöder referens fil typ. Andra utdata kan nås med hjälp av ett moln jobb. Till exempel skickar ett Stream Analytics jobb som finns i Edge utdata till Edge Hub, som sedan kan skicka utdata till IoT Hub. Du kan använda en andra moln värd Azure Stream Analytics jobb med indata från IoT Hub och utdata till Power BI eller en annan Utdatatyp.

## <a name="license-and-third-party-notices"></a>Licens och meddelanden från tredje part
* [Azure Stream Analytics på IoT Edge licens](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Meddelande från tredje part för Azure Stream Analytics på IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Avbildnings information för Azure Stream Analytics modul 

Den här versions informationen uppdaterades senast den 2020-09-21:

- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - bas avbildning: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - systemet
      - arkitektur: amd64
      - OS: Linux
 
- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - bas avbildning: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - systemet
      - arkitektur: arm
      - OS: Linux
 
- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - bas avbildning: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - systemet
      - arkitektur: arm64
      - OS: Linux
      
      
## <a name="get-help"></a>Få hjälp
Om du behöver ytterligare hjälp kan du testa [sidan Microsoft Q&en fråga för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Självstudie för Stream Analytics i IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Utveckla Stream Analytics Edge-jobb med Visual Studio-verktyg](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implementera CI/CD för Stream Analytics med API: er](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
