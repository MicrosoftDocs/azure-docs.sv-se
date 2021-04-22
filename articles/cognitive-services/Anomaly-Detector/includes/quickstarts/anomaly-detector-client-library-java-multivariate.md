---
title: Avvikelseidentifiering snabbstart för Java-klientbibliotek med fleravarierade program
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: e85f54beb9a3d4203e527ed9c8ce5582b6a2f5b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880278"
---
Kom igång med Avvikelseidentifiering flervarierat klientbibliotek för Java. Följ de här stegen för att installera paketet och börja använda de algoritmer som tillhandahålls av tjänsten. De nya API:erna för avvikelseidentifiering med flera variabler gör det möjligt för utvecklare att enkelt integrera avancerad AI för att identifiera avvikelser från grupper av mått, utan att behöva maskininlärningskunskaper eller märkta data. Beroenden och interkorrelationer mellan olika signaler räknas automatiskt som viktiga faktorer. På så sätt kan du proaktivt skydda komplexa system mot fel.

Använd Avvikelseidentifiering multivariate-klientbibliotek för Java för att:

* Identifiera avvikelser på systemnivå från en grupp med tidsserier.
* När en enskild tidsserie inte berättar så mycket och du måste titta på alla signaler för att identifiera ett problem.
* Predikatiskt underhåll av dyra fysiska tillgångar med tiotals till hundratals olika typer av sensorer som mäter olika aspekter av systemets hälsa.

[Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Paket (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/)  |  [Exempelkod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-byggverktyget](https://gradle.org/install/), eller någon annan beroendehanterare.
* När du har din Azure-prenumeration skapar Avvikelseidentifiering en Avvikelseidentifiering resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Avvikelseidentifiering-API:et. Du klistrar in din nyckel och slutpunkt i koden nedan senare i snabbstarten.
    Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I den här snabbstarten används Gradle-beroendehanteraren. Du hittar mer information om klientbibliotek på [Maven Central Repository.](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från arbetskatalogen. Det här kommandot skapar viktiga byggfiler för Gradle, inklusive *build.gradle.kts* som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Leta *upp build.gradle.kts* och öppna det med önskad IDE eller textredigerare. Kopiera sedan den här byggkonfigurationen. Se till att inkludera projektberoendena.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Skapa en Java-fil

Skapa en mapp för exempelappen. Kör följande kommando från arbetskatalogen:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *MetricsAdvisorQuickstarts.java*. Öppna den i önskat redigeringsprogram eller IDE och lägg till följande `import` -instruktioner:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Skapa variabler för resursens Azure-slutpunkt och nyckel. Skapa en annan variabel för exempeldatafilen.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Om du vill Avvikelseidentifiering flera api:er måste vi träna vår egen modell innan du använder identifiering. Data som används för träning är en batch med tidsserier. Varje tidsserie ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tidsserier ska vara komprimerade i en zip-fil och laddas upp till [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md) Som standard används filnamnet för att representera variabeln för tidsserien. Alternativt kan en extra meta.jspå filen inkluderas i zip-filen om du vill att namnet på variabeln ska vara ett annat än ZIP-filnamnet. När vi har [genererat blob-SAS-URL :en (signaturer för delad åtkomst)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL:en till ZIP-filen för träning.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med Avvikelseidentifiering klientbibliotek för Node.js:

* [Autentisera klienten](#authenticate-the-client)
* [Träna en modell](#train-a-model)
* [Identifiera avvikelser](#detect-anomalies)
* [Exportera modell](#export-model)
* [Ta bort modell](#delete-model)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en instans av `anomalyDetectorClient` ett objekt med din slutpunkt och dina autentiseringsuppgifter.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Träna en modell

### <a name="construct-a-model-result-and-train-model"></a>Skapa ett modellresultat och träna en modell

Först måste vi skapa en modellbegäran. Se till att start- och sluttiden överensstämmer med datakällan.

 Om du vill Avvikelseidentifiering flera api:er måste vi träna vår egen modell innan du använder identifiering. Data som används för träning är en batch med tidsserier. Varje tidsserie ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tidsserier ska zippas upp i en zip-fil och laddas upp till [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Som standard används filnamnet för att representera variabeln för tidsserien. Alternativt kan en extra meta.jspå filen inkluderas i zip-filen om du vill att namnet på variabeln ska vara ett annat än ZIP-filnamnet. När vi har [genererat blob-SAS-URL :en (signaturer för delad åtkomst)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL:en till ZIP-filen för träning.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Identifiera avvikelser

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Exportera modell

Om du vill exportera den tränade modellen använder du `exportModelWithResponse` .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Ta bort modell

Om du vill ta bort en befintlig modell som är tillgänglig för den aktuella resursen använder du `deleteMultivariateModelWithResponse` funktionen .

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Kör programmet

Du kan skapa appen med:

```console
gradle build
```
### <a name="run-the-application"></a>Kör programmet

Innan du kör kan det vara bra att kontrollera koden mot den [fullständiga exempelkoden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java).

Kör programmet med `run` målet:

```console
gradle run
```

## <a name="next-steps"></a>Nästa steg

* [Avvikelseidentifiering metodtips med flera](../../concepts/best-practices-multivariate.md)
