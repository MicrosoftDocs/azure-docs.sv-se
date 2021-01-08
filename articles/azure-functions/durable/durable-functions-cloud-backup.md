---
title: Fläkt-ut-och fläkt scenarier i Durable Functions – Azure
description: Lär dig hur du implementerar ett antidrivet fläkt scenario i Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 91128033696af6a56488db7991987f1e384b719e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027661"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Exempel på anti-out/fläkt i scenario i Durable Functions-Cloud backup

*Fläkt-ut-och-fläkt* syftar på mönstret för att köra flera funktioner samtidigt och sedan utföra någon agg regering på resultaten. I den här artikeln beskrivs ett exempel som använder [Durable Functions](durable-functions-overview.md) för att implementera ett scenario med fläkt-in-och fläkt. Exemplet är en varaktig funktion som säkerhetskopierar hela eller delar av en Apps webbplats innehåll till Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Översikt över scenario

I det här exemplet laddar funktionerna upp alla filer i en angiven katalog rekursivt till Blob Storage. De räknar också det totala antalet byte som har överförts.

Det är möjligt att skriva en enda funktion som tar hand om allt. Det huvudsakliga problemet som du skulle köra i är **skalbarhet**. En enskild funktions körning kan bara köras på en enskild virtuell dator, så data flödet begränsas av genomflödet för den virtuella datorn. Ett annat problem är **tillförlitlighet**. Om det uppstår ett problem halvvägs eller om hela processen tar fler än 5 minuter kan säkerhets kopieringen Miss lyckas i ett delvis slutfört tillstånd. Den måste sedan startas om.

En mer robust metod är att skriva två vanliga funktioner: en skulle räkna upp filerna och lägga till fil namnen i en kö, och en annan skulle läsa från kön och överföra filerna till Blob Storage. Den här metoden är bättre när det gäller data flöde och tillförlitlighet, men kräver att du etablerar och hanterar en kö. Mer viktig information **finns i användnings villkoren och** **samordningen** om du vill göra allt mer, till exempel rapportera det totala antalet byte som överförs.

En Durable Functions metod ger dig alla angivna förmåner med mycket låg belastning.

## <a name="the-functions"></a>Funktionerna

I den här artikeln beskrivs följande funktioner i exempel appen:

* `E2_BackupSiteContent`: En [Orchestrator-funktion](durable-functions-bindings.md#orchestration-trigger) som anropar `E2_GetFileList` för att hämta en lista över filer som ska säkerhets kopie ras och sedan anropas `E2_CopyFileToBlob` för att säkerhetskopiera varje fil.
* `E2_GetFileList`: En [aktivitets funktion](durable-functions-bindings.md#activity-trigger) som returnerar en lista med filer i en katalog.
* `E2_CopyFileToBlob`: En aktivitets funktion som säkerhetskopierar en enskild fil till Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent Orchestrator-funktion

Den här Orchestrator-funktionen gör i princip följande:

1. Använder ett `rootDirectory` värde som indataparameter.
2. Anropar en funktion för att hämta en rekursiv lista över filer under `rootDirectory` .
3. Gör flera parallella funktions anrop för att ladda upp varje fil till Azure Blob Storage.
4. Väntar på att alla uppladdningar ska slutföras.
5. Returnerar summan av totalt antal byte som överförts till Azure Blob Storage.

# <a name="c"></a>[C#](#tab/csharp)

Här är den kod som implementerar Orchestrator-funktionen:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Lägg märke till `await Task.WhenAll(tasks);` raden. Alla enskilda anrop till `E2_CopyFileToBlob` funktionen förväntades *inte* , vilket gör att de kan köras parallellt. När vi skickar den här uppgifts uppsättningen till `Task.WhenAll` , får vi tillbaka en aktivitet som inte slutförs *förrän alla kopierings åtgärder har slutförts*. Om du är bekant med aktivitets Parallel Library (TPL) i .NET är detta inte nytt för dig. Skillnaden är att dessa aktiviteter kan köras på flera virtuella datorer samtidigt, och Durable Functions-tillägget säkerställer att körningen från slut punkt till slut punkt är elastisk för att bearbeta återvinning.

Efter att ha väntat `Task.WhenAll` oss vet vi att alla funktions anrop har slutförts och har returnerat värdena tillbaka till oss. Varje anrop för att `E2_CopyFileToBlob` returnera antalet byte som har överförts, så att summan av total antalet byte är en del av att lägga till alla dessa retur värden tillsammans.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Funktionen använder standard *function.jspå* för Orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Här är den kod som implementerar Orchestrator-funktionen:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Lägg märke till `yield context.df.Task.all(tasks);` raden. Alla enskilda anrop till `E2_CopyFileToBlob` funktionen gavs *inte* ut, vilket gör att de kan köras parallellt. När vi skickar den här uppgifts uppsättningen till `context.df.Task.all` , får vi tillbaka en aktivitet som inte slutförs *förrän alla kopierings åtgärder har slutförts*. Om du är bekant med [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) i Java Script är detta inte nytt för dig. Skillnaden är att dessa aktiviteter kan köras på flera virtuella datorer samtidigt, och Durable Functions-tillägget säkerställer att körningen från slut punkt till slut punkt är elastisk för att bearbeta återvinning.

> [!NOTE]
> Även om aktiviteterna är konceptuellt likartade med JavaScript-löfte, ska Orchestrator-funktioner använda `context.df.Task.all` och `context.df.Task.any` i stället för `Promise.all` och `Promise.race` för att hantera aktivitets parallellisering.

Efter `context.df.Task.all` att ha fått från, vet vi att alla funktions anrop har slutförts och att de returnerade värdena tillbaka till oss. Varje anrop för att `E2_CopyFileToBlob` returnera antalet byte som har överförts, så att summan av total antalet byte är en del av att lägga till alla dessa retur värden tillsammans.

# <a name="python"></a>[Python](#tab/python)

Funktionen använder standard *function.jspå* för Orchestrator-funktioner.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/function.json)]

Här är den kod som implementerar Orchestrator-funktionen:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/\_\_init\_\_.py)]

Lägg märke till `yield context.task_all(tasks);` raden. Alla enskilda anrop till `E2_CopyFileToBlob` funktionen gavs *inte* ut, vilket gör att de kan köras parallellt. När vi skickar den här uppgifts uppsättningen till `context.task_all` , får vi tillbaka en aktivitet som inte slutförs *förrän alla kopierings åtgärder har slutförts*. Om du är bekant med [`asyncio.gather`](https://docs.python.org/3/library/asyncio-task.html#asyncio.gather) i python är detta inte nytt för dig. Skillnaden är att dessa aktiviteter kan köras på flera virtuella datorer samtidigt, och Durable Functions-tillägget säkerställer att körningen från slut punkt till slut punkt är elastisk för att bearbeta återvinning.

> [!NOTE]
> Även om uppgifter är konceptuellt likartade med python-awaitables, bör Orchestrator-funktioner använda `yield` såväl som `context.task_all` och för `context.task_any` API: erna för att hantera parallellisering för aktiviteter.

Efter `context.task_all` att ha fått från, vet vi att alla funktions anrop har slutförts och att de returnerade värdena tillbaka till oss. Varje anrop för att `E2_CopyFileToBlob` returnera antalet överförda byte, så vi kan beräkna summan av totala antalet byte genom att lägga till alla retur värden tillsammans.

---

### <a name="helper-activity-functions"></a>Hjälp aktivitetens funktioner

Funktionerna i hjälp aktiviteten, precis som med andra exempel, är bara vanliga funktioner som använder sig av `activityTrigger` trigger-bindningen.

#### <a name="e2_getfilelist-activity-function"></a>Funktionen E2_GetFileList aktivitet

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.js* filen för att `E2_GetFileList` se ut så här:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

Här är implementeringen:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Funktionen använder `readdirp` modulen (version 2. x) för att rekursivt läsa katalog strukturen.

# <a name="python"></a>[Python](#tab/python)

*function.js* filen för att `E2_GetFileList` se ut så här:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/function.json)]

Här är implementeringen:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/\_\_init\_\_.py)]

---

> [!NOTE]
> Du kanske undrar varför du inte ville använda den här koden direkt i Orchestrator-funktionen. Du kan, men detta skulle bryta en av de grundläggande reglerna för Orchestrator-funktioner, vilket är att de aldrig bör göra I/O, inklusive lokal fil system åtkomst. Mer information finns i [begränsningar för Orchestrator-funktions kod](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>Funktionen E2_CopyFileToBlob aktivitet

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Du måste installera `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet-paketet för att köra exempel koden.

Funktionen använder vissa avancerade funktioner i Azure Functions bindningar (det vill säga användningen av [ `Binder` parametern](../functions-dotnet-class-library.md#binding-at-runtime)), men du behöver inte bekymra dig om dessa Detaljer för den här genom gången.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.jspå* fil för `E2_CopyFileToBlob` är på samma sätt enkla:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

JavaScript-implementeringen använder [Azure Storage SDK för-noden](https://github.com/Azure/azure-storage-node) för att överföra filerna till Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

# <a name="python"></a>[Python](#tab/python)

*function.jspå* fil för `E2_CopyFileToBlob` är på samma sätt enkla:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/function.json)]

Python-implementeringen använder [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python) för att överföra filerna till Azure Blob Storage.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/\_\_init\_\_.py)]

---

Implementeringen läser in filen från disken och strömmar innehållet till en blob med samma namn i behållaren "säkerhets kopiering". Returvärdet är antalet byte som kopieras till Storage, som sedan används av Orchestrator-funktionen för att beräkna mängd summan.

> [!NOTE]
> Detta är ett perfekt exempel på hur du flyttar I/O-åtgärder till en `activityTrigger` funktion. Det går inte bara att distribuera arbetet på flera olika datorer, men du får också fördelarna med att göra en kontroll punkt för förloppet. Om värd processen avslutas av någon anledning vet du vilka uppladdningar som redan har slutförts.

## <a name="run-the-sample"></a>Kör exemplet

Du kan starta dirigeringen, i Windows, genom att skicka följande HTTP POST-begäran.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

Alternativt, på en Linux-Funktionsapp (python körs för närvarande bara på Linux för App Service), kan du starta dirigeringen så här:

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"/home/site/wwwroot"
```

> [!NOTE]
> `HttpStart`Funktionen som du anropar fungerar bara med JSON-formaterat innehåll. Därför `Content-Type: application/json` krävs rubriken och katalog Sök vägen kodas som en JSON-sträng. HTTP-kodfragmentet förutsätter att det finns en post i `host.json` filen som tar bort `api/` standardprefixet från alla URL: er för http-utlösare. Du hittar koden för den här konfigurationen i `host.json` filen i exemplen.

Denna HTTP-begäran utlöser `E2_BackupSiteContent` Orchestrator och skickar strängen `D:\home\LogFiles` som en parameter. Svaret innehåller en länk för att hämta status för säkerhets kopieringen:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Det kan ta flera minuter att slutföra åtgärden beroende på hur många loggfiler du har i din Function-app. Du kan hämta den senaste statusen genom att fråga URL: en i `Location` rubriken för föregående HTTP 202-svar.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

I det här fallet körs fortfarande funktionen. Du kan se de inloggade ingångarna som sparats i Orchestrator-läge och senast uppdaterad tid. Du kan fortsätta att använda `Location` rubrik värden för att avsöka efter slutförandet. När statusen är "slutförd" visas ett HTTP-svar som liknar följande:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Nu kan du se att dirigeringen är slutförd och ungefär hur lång tid det tog att slutföra. Du kan också se ett värde för `output` fältet, vilket indikerar att cirka 450 KB av loggarna har laddats upp.

## <a name="next-steps"></a>Nästa steg

Det här exemplet har visat hur du implementerar mönstret för fläkt-out/fläkt i. I nästa exempel visas hur du implementerar övervaknings mönstret med hjälp av [varaktiga timers](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Kör övervaknings exemplet](durable-functions-monitor.md)