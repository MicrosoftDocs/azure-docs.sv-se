---
title: Testning av Azure Durable Functions-enhet
description: Lär dig mer om att enhets test Durable Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 89b6419e95b3971b0d272490e19354f300204e1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103491052"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions enhets testning

Enhets testning är en viktig del av modern program utvecklings praxis. Enhets testerna verifierar affärs logik beteendet och skyddar mot att vi introducerar ändringar som inte uppmärksammas i framtiden. Durable Functions kan lätt växa i komplexitet så att enhets testerna kan undvika att förlora ändringar. I följande avsnitt beskrivs hur du enheten testar de tre funktions typerna-Orchestration-klient, Orchestrator-och aktivitets funktioner.

> [!NOTE]
> Den här artikeln innehåller rikt linjer för enhets testning för Durable Functions appar som riktar sig Durable Functions 2. x. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

## <a name="prerequisites"></a>Förutsättningar

I exemplen i den här artikeln krävs kunskaper om följande begrepp och ramverk:

* Enhetstestning

* Bestående funktioner

* [xUnit](https://github.com/xunit/xunit) -testnings ramverk

* [MOQ](https://github.com/moq/moq4) -modellerande ramverk

## <a name="base-classes-for-mocking"></a>Bas klasser för att modellera

Det finns stöd för att modellera genom följande gränssnitt:

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationClient), [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.IDurableEntityClient) och [IDurableClient](/dotnet/api/microsoft.azure.webjobs.IDurableClient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationContext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.IDurableActivityContext)
  
* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.IDurableEntityContext)

Dessa gränssnitt kan användas med de olika utlösare och bindningar som stöds av Durable Functions. När du kör Azure Functions kör Functions-körningen funktions koden med en konkret implementering av dessa gränssnitt. För enhets testning kan du skicka in en skissad version av dessa gränssnitt för att testa affärs logiken.

## <a name="unit-testing-trigger-functions"></a>Enhets testning utlöser funktioner

I det här avsnittet kommer enhets testet att validera logiken för följande HTTP-utlösare för att starta nya dirigeringar.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Uppgiften enhets test kommer att verifiera värdet för `Retry-After` huvudet i svarets nytto Last. Därför kommer enhets testet att modellera några av `IDurableClient` metoderna för att säkerställa förutsägbara beteenden.

Först använder vi ett modell ramverk ([MOQ](https://github.com/moq/moq4) i det här fallet) för att modellera `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> Även om du kan modellera gränssnitt genom att direkt implementera gränssnittet som en klass fören klar du processen på olika sätt när du använder ett modell ramverk. Om till exempel en ny metod läggs till i gränssnittet över mindre versioner, behöver MOQ inte några kod ändringar till skillnad från konkreta implementeringar.

Sedan `StartNewAsync` är metoden fördelad för att returnera ett välkänt instans-ID.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

Nu `CreateCheckStatusResponse` är det en tom HTTP 200-svar att returnera.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

`ILogger` är också modell:

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```  

Nu `Run` anropas metoden från enhets testet:

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
 ```

 Det sista steget är att jämföra utdata med det förväntade värdet:

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Efter att ha kombinerat alla steg har enhets testet följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Enhets testning för Orchestrator-funktioner

Orchestrator Functions är ännu mer intressant för enhets testning eftersom de ofta har mycket mer affärs logik.

I det här avsnittet kommer enhets testerna att verifiera utdata från `E1_HelloSequence` Orchestrator-funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Enhets test koden börjar med att skapa en modell:

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

Sedan blir aktivitets metod anropen blå:

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Nästa enhets test anropar `HelloSequence.Run` metoden:

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Slutligen kommer utdata att val IDE ras:

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

Efter att ha kombinerat alla steg har enhets testet följande kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Enhets testning, aktivitets funktioner

Aktivitets funktioner kan vara en enhet testas på samma sätt som icke-varaktiga funktioner.

I det här avsnittet verifierar enhets testet beteendet för `E1_SayHello` funktionen activity:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Och enhets testerna kontrollerar utdataformatets format. Enhets testerna kan använda parameter typerna direkt eller en modell `IDurableActivityContext` klass:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Läs mer om MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
