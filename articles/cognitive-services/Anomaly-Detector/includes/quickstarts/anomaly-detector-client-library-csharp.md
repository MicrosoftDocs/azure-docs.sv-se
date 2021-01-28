---
title: Snabb start för .NET-klient bibliotek med avvikelse detektor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: ee4dc926269d3ac66243a3953d7e41eb3a30198c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947275"
---
Kom igång med klient biblioteket för avvikelse detektor för .NET. Följ de här stegen för att installera paket starten med hjälp av algoritmerna som tillhandahålls av tjänsten. Med tjänsten avvikelse detektor kan du hitta avvikelser i dina Time Series-data genom att automatiskt använda de bästa passnings modellerna, oavsett bransch, scenario eller data volym.

Använd klient biblioteket för avvikelse detektor för .NET för att:

* Identifiera avvikelser i din tids serie data uppsättning som en batch-begäran
* Identifiera avvikelse status för den senaste data punkten i din tids serie
* Identifiera trend ändrings punkter i din data uppsättning.

Dokumentation om biblioteks [referens](https://aka.ms/anomaly-detector-dotnet-ref)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2)  |  [Hitta koden på GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core)
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i den Azure Portal för att hämta nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>Skapa ett nytt .NET Core-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `anomaly-detector-quickstart` . Det här kommandot skapar ett enkelt "Hello World"-projekt med en enda C#-källfil: *program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```dotnetcli
dotnet build
```

Build-utdata får inte innehålla varningar eller fel.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du klient biblioteket för avvikelse detektor för .NET med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector
```

Öppna *program.cs* -filen från projekt katalogen och Lägg till följande med `directives` :

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

I programmets `main()` metod skapar du variabler för resursens Azure-plats och din nyckel som en miljö variabel. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE eller gränssnittet som kör den stängas och läsas in igen för att få åtkomst till variabeln.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objekt modell

Klienten för avvikelse detektor är ett [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) -objekt som autentiserar till Azure med hjälp av [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), som innehåller din nyckel. Klienten kan utföra avvikelse identifiering på en hel data uppsättning med hjälp av [EntireDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)eller den senaste data punkten med [LastDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). Metoden [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) identifierar punkter som markerar ändringar i en trend.

Time Series-data skickas som en serie [punkter](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) i ett [Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) -objekt. `Request`Objektet innehåller egenskaper för att beskriva data (till exempel[granularitet](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) ) och parametrar för avvikelse identifiering.

Avvikelse detektorns svar är antingen ett [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse)-, [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse)-eller [changePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) -objekt, beroende på vilken metod som används.

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du gör följande med klient biblioteket för avvikelse detektor för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Läs in en tids serie data uppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela data uppsättningen](#detect-anomalies-in-the-entire-data-set)
* [Identifiera avvikelse statusen för den senaste data punkten](#detect-the-anomaly-status-of-the-latest-data-point)
* [Identifiera ändrings punkterna i data uppsättningen](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du en klient med din slut punkt och nyckel. Skapa ett [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) -objekt.

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>Läsa in tids serie data från en fil

Hämta exempel data för den här snabb starten från [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Högerklicka på **RAW** i webbläsaren.
2. Klicka på **Spara länk som**.
3. Spara filen i program katalogen som en CSV-fil.

Denna tids serie data formateras som en. csv-fil och skickas till API: t för avvikelse identifiering.

Skapa en ny metod för att läsa tids serie data och Lägg till dem i ett [Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) -objekt. Anropa `File.ReadAllLines()` med sökvägen till filen och skapa en lista med [punkt](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point) objekt och ta bort alla nya rad tecken. Extrahera värdena och avgränsa datestamp från det numeriska värdet och Lägg till dem i ett nytt `Point` objekt.

Skapa ett `Request` objekt med en serie punkter och `Granularity.Daily` för data punkternas [granularitet](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity) (eller periodicitet).

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela data uppsättningen

Skapa en metod för att anropa klientens [EntireDetectAsync ()-](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) Metod med `Request` objektet och väntar svaret som ett [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) -objekt. Om tids serien innehåller avvikelser itererar du genom svarets [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly) -värden och skriver ut alla `true` . Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

Skapa en metod för att anropa klientens [LastDetectAsync ()-](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) Metod med `Request` objektet och väntar svaret som ett [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) -objekt. Kontrol lera svarets [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly) -attribut för att avgöra om den senaste data punkten som skickats var en avvikelse eller inte.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>Identifiera ändrings punkter i data uppsättningen

Skapa en metod för att anropa klientens [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref) -Metod med `Request` objektet och väntar svaret som ett [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) -objekt. Kontrol lera svarets IsChangePoint-värden och skriv ut alla `true` . Dessa värden motsvarar trend ändrings punkter, om de påträffades.

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `dotnet run` kommandot från program katalogen.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
