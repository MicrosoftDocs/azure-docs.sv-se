---
title: Avvikelse detektor multivarierad Java Client library snabb start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: eae4d00cd7b1a0ff90648086320135505a0d900a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316044"
---
Kom igång med multivarierad klient bibliotek för avvikelse detektor för Java. Följ de här stegen för att installera paket starten med hjälp av algoritmerna som tillhandahålls av tjänsten. De nya API: erna för avvikelse identifiering i multivarierad gör det möjligt för utvecklare att enkelt integrera avancerad AI för att identifiera avvikelser från grupper av mått, utan att det behövs några kunskaper om Machine Learning eller märkta data. Beroenden och Inter-korrelationer mellan olika signaler räknas automatiskt som viktiga faktorer. Detta hjälper dig att proaktivt skydda dina komplexa system från haverier.

Använd klient biblioteket för avvikelse detektor multivarierad för Java för att:

* Identifiera avvikelser på system nivå från en grupp tids serier.
* När en enskild tid serie inte meddelar dig mycket och du måste titta på alla signaler för att identifiera ett problem.
* Predicative-underhåll av dyra fysiska till gångar med flera till hundratals olika typer av sensorer som mäter olika aspekter av system hälsan.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse </a> i den Azure Portal för att hämta nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I den här snabb starten används Gradle-beroende hanteraren. Du hittar mer information om klient bibliotek på den [centrala maven-lagringsplatsen](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från din arbets katalog. Med det här kommandot skapas viktiga build-filer för Gradle, inklusive *build. Gradle. KTS* som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

### <a name="install-the-client-library"></a>Installera klient biblioteket

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i den här build-konfigurationen. Se till att inkludera projekt beroenden.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Skapa en Java-fil

Skapa en mapp för din exempel App. Kör följande kommando från arbets katalogen:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *MetricsAdvisorQuickstarts. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande- `import` uttryck:

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

Skapa variabler för resursens Azure-slutpunkt och nyckel. Skapa en annan variabel för exempel data filen.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 För att kunna använda multivarierad-API: erna för avvikelse detektor måste vi träna vår egen modell innan identifieringen används. Data som används för utbildning är en batch över tids serier, varje gång serien ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tids serier ska vara zippade till en zip-fil och överföras till [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md). Som standard används fil namnet för att representera variabeln för tids serien. Alternativt kan en extra meta.jsi filen inkluderas i zip-filen om du vill att namnet på variabeln ska skilja sig från. zip-filens namn. När vi genererar [URL: en BLOB SAS (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL: en till zip-filen för utbildning.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med klient biblioteket för avvikelse detektor för Node.js:

* [Autentisera klienten](#authenticate-the-client)
* [Träna en modell](#train-a-model)
* [Identifiera avvikelser](#detect-anomalies)
* [Exportera modell](#export-model)
* [Ta bort modell](#delete-model)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera ett `anomalyDetectorClient` objekt med din slut punkt och dina autentiseringsuppgifter.

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

### <a name="construct-a-model-result-and-train-model"></a>Konstruera modell resultat och träna modeller

Först måste vi konstruera en modell förfrågan. Kontrol lera att start-och slut tid stämmer med din data källa.

 För att kunna använda multivarierad-API: erna för avvikelse detektor måste vi träna vår egen modell innan identifieringen används. Data som används för utbildning är en batch över tids serier, varje gång serien ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tids serier ska vara zippade till en zip-fil och överföras till [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Som standard används fil namnet för att representera variabeln för tids serien. Alternativt kan en extra meta.jsi filen inkluderas i zip-filen om du vill att namnet på variabeln ska skilja sig från. zip-filens namn. När vi genererar [URL: en BLOB SAS (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL: en till zip-filen för utbildning.

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

Använd för att exportera din tränade modell `exportModelWithResponse` .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Ta bort modell

Använd funktionen för att ta bort en befintlig modell som är tillgänglig för den aktuella resursen `deleteMultivariateModelWithResponse` .

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Kör programmet

Du kan bygga appen med:

```console
gradle build
```
### <a name="run-the-application"></a>Kör programmet

Kör programmet med `run` målet:

```console
gradle run
```

## <a name="next-steps"></a>Nästa steg

* [Metod tips för multivarierad av avvikelse detektor](../../concepts/best-practices-multivariate.md)
