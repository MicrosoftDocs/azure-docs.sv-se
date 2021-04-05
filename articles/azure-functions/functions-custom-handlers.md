---
title: Azure Functions anpassade hanterare
description: Lär dig hur du använder Azure Functions med valfri språk-eller körnings version.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: dd112c74ea9f013a0e14bddd735060ddbf73c14e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578533"
---
# <a name="azure-functions-custom-handlers"></a>Azure Functions anpassade hanterare

Varje Functions-App körs av en språkspecifik hanterare. Även om Azure Functions stöder många [språk hanterare](./supported-languages.md) som standard, finns det fall där du kanske vill använda andra språk eller körningar.

Anpassade hanterare är lätta webb servrar som tar emot händelser från Functions-värden. Alla språk som stöder HTTP-primitiver kan implementera en anpassad hanterare.

Anpassade hanterare passar bäst för situationer där du vill:

- Implementera en Function-app på ett språk som inte stöds för närvarande, till exempel Go eller Rust.
- Implementera en Function-app i en körning som inte stöds för närvarande, till exempel DENO.

Med anpassade hanterare kan du använda [utlösare och indata och utdata-bindningar](./functions-triggers-bindings.md) via [tilläggs paket](./functions-bindings-register.md).

Kom igång med Azure Functions anpassade hanterare med [snabb starter i go och Rust](create-first-function-vs-code-other.md).

## <a name="overview"></a>Översikt

I följande diagram visas relationen mellan Functions-värden och en webb server som implementerats som en anpassad hanterare.

![Översikt över Azure Functions anpassad hanterare](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Varje händelse utlöser en begäran som skickas till Functions-värden. En händelse är en utlösare som stöds av Azure Functions.
1. Funktions värden skickar sedan en [begäran om nytto Last](#request-payload) till webb servern. Nytto lasten innehåller utlösare och data bindnings data och andra metadata för funktionen.
1. Webb servern kör den enskilda funktionen och returnerar en [svars nytto Last](#response-payload) till Functions-värden.
1. Funktions värden skickar data från svaret till funktionens utgående bindningar för bearbetning.

En Azure Functions-app som implementeras som en anpassad hanterare måste konfigurera *host.jspå*, *local.settings.jspå* och *function.jspå* filer enligt några få konventioner.

## <a name="application-structure"></a>Program struktur

Om du vill implementera en anpassad hanterare behöver du följande aspekter av ditt program:

- En *host.jspå* filen i roten för din app
- En *local.settings.jspå* filen i roten för din app
- En *function.jspå* fil för varje funktion (inuti en mapp som matchar funktions namnet)
- Ett kommando, ett skript eller en körbar fil som kör en webb server

Följande diagram visar hur dessa filer ser ut i fil systemet för en funktion med namnet "MyQueueFunction" och en anpassad hanterare med namnet *handler.exe*.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Konfiguration

Programmet konfigureras via *host.jspå* och *local.settings.jspå* filer.

#### <a name="hostjson"></a>host.jspå

*host.jspå* visar de funktioner som är värdar för att skicka begär Anden genom att peka på en webb server som kan bearbeta http-händelser.

En anpassad hanterare definieras genom att konfigurera *host.jspå* filen med information om hur du kör webb servern via `customHandler` avsnittet.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

`customHandler`Avsnittet pekar på ett mål som definieras av `defaultExecutablePath` . Körnings målet kan antingen vara ett kommando, en körbar fil eller en fil där webb servern är implementerad.

Använd `arguments` matrisen för att skicka argument till den körbara filen. Argument stöder expandering av miljövariabler (program inställningar) med `%%` notation.

Du kan också ändra arbets katalogen som används av den körbara filen med `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Stöd för bindningar

Standard utlösare tillsammans med indata-och utgående bindningar är tillgängliga genom att referera till [tilläggs paket](./functions-bindings-register.md) i *host.jsi* filen.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.jspå* definierar program inställningar som används när du kör Function-appen lokalt. Eftersom det kan innehålla hemligheter bör *local.settings.js* vara exkluderat från käll kontroll. Använd program inställningarna i Azure i stället.

För anpassade hanterare, ange `FUNCTIONS_WORKER_RUNTIME` till `Custom` i *local.settings.jspå*.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

### <a name="function-metadata"></a>Function-metadata

När den används med en anpassad hanterare är *function.jsi* innehållet inte annorlunda än hur du definierar en funktion under någon annan kontext. Det enda kravet är att *function.jspå* filer måste finnas i en mapp med namnet som matchar funktions namnet.

Följandefunction.jskonfigurerar en funktion som har en utlösare för kön och en utgående bindning *för* kö. Eftersom den finns i en mapp med namnet *MyQueueFunction* definierar den en funktion med namnet *MyQueueFunction*.

**MyQueueFunction/function.jspå**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>Begär nytto Last

När ett Queue-meddelande tas emot skickar Functions-värden en HTTP POST-begäran till den anpassade hanteraren med en nytto Last i bröd texten.

Följande kod representerar en nytto last för begäran. Nytto lasten innehåller en JSON-struktur med två medlemmar: `Data` och `Metadata` .

`Data`Medlemmen innehåller nycklar som matchar indata och utlösnings namn som definieras i matrisen för bindningar i *function.jsi* filen.

`Metadata`Medlemmen inkluderar [metadata som genereras från händelse källan](./functions-bindings-expressions-patterns.md#trigger-metadata).

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Svars nytto Last

Efter konvention formateras funktions svaren som nyckel/värde-par. Nycklar som stöds är:

| <nobr>Nytto Last nyckel</nobr>   | Datatyp | Kommentarer                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | objekt    | Innehåller svars värden som definieras av `bindings` matrisen i *function.jspå*.<br /><br />Om en funktion till exempel har kon figurer ATS med en utgående bindning för kö med namnet "myQueueOutput", `Outputs` innehåller en nyckel med namnet `myQueueOutput` , som anges av den anpassade hanteraren till de meddelanden som skickas till kön. |
| `Logs`        | matris     | Meddelanden visas i loggarna Functions-anrop.<br /><br />När du kör i Azure visas meddelanden i Application Insights. |
| `ReturnValue` | sträng    | Används för att tillhandahålla ett svar när utdata har kon figurer ATS som `$return` i *function.jsi* filen. |

Detta är ett exempel på en nytto last för svar.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Exempel

Anpassade hanterare kan implementeras på alla språk som har stöd för att ta emot HTTP-händelser. I följande exempel visas hur du implementerar en anpassad hanterare med hjälp av programmeringsspråket go.

### <a name="function-with-bindings"></a>Funktion med bindningar

Scenariot som implementeras i det här exemplet innehåller en funktion med namnet `order` som godkänner en `POST` med en nytto last som representerar en produkt order. När en order skickas till funktionen skapas ett Queue Storage meddelande och ett HTTP-svar returneras.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementering

I en mapp med namnet *order* konfigurerar *function.js* filen för http-utlöst funktion.

**order/function.jspå**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Den här funktionen definieras som en [http-utlöst funktion](./functions-bindings-http-webhook-trigger.md) som returnerar ett [http-svar](./functions-bindings-http-webhook-output.md) och matar ut ett [kö lagrings](./functions-bindings-storage-queue-output.md) meddelande.

I appens rot är *host.jspå* fil konfigurerad att köra en körbar fil med namnet `handler.exe` ( `handler` i Linux eller MacOS).

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Detta är HTTP-begäran som skickas till Functions-körningen.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

Functions-körningen skickar sedan följande HTTP-begäran till den anpassade hanteraren:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Vissa delar av nytto lasten togs bort för det kortfattat.

*handler.exe* är det program som kompileras med anpassad hanterare som kör en webb server och svarar på att fungera som anrops begär Anden från funktions värden.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

I det här exemplet kör den anpassade hanteraren en webb server för att hantera HTTP-händelser och är inställd på att lyssna efter begär Anden via `FUNCTIONS_CUSTOMHANDLER_PORT` .

Även om functions-värden tog emot ursprunglig HTTP-begäran på `/api/order` , anropar den den anpassade hanteraren med hjälp av funktions namnet (dess mappnamn). I det här exemplet definieras funktionen i sökvägen till `/order` . Värden skickar den anpassade hanteraren en HTTP-begäran på sökvägen till `/order` .

När `POST` begär Anden skickas till den här funktionen är utlösarens data och funktions-metadata tillgängliga via texten HTTP-begäran. Den ursprungliga HTTP-begäran om brödtext kan nås i nytto lasten `Data.req.Body` .

Funktionens svar är formaterat i nyckel/värde-par där `Outputs` medlemmen innehåller ett JSON-värde där nycklarna matchar utdata som definieras i *function.jsi* filen.

Detta är ett exempel på en nytto last som denna hanterare returnerar till Functions-värden.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Genom att ange `message` utdata som motsvarar de order data som kom från begäran, kommer funktionen att mata ut data till den konfigurerade kön. Funktions värden returnerar också det HTTP-svar som kon figurer ATS i `res` anroparen.

### <a name="http-only-function"></a>Funktionen endast HTTP

För HTTP-utlösta funktioner utan ytterligare bindningar eller utdata kanske du vill att din hanterare ska arbeta direkt med HTTP-begäran och-svar i stället för [begäran](#request-payload) och [svars](#response-payload) nytto laster för anpassad hanterare. Det här beteendet kan konfigureras i *host.jspå* med `enableForwardingHttpRequest` inställningen.

> [!IMPORTANT]
> Det primära syftet med funktionen anpassad hanterare är att aktivera språk och körningar som för närvarande inte har stöd för första klassen på Azure Functions. Det kan vara möjligt att köra webb program med anpassade hanterare, Azure Functions inte är en omvänd standardproxy. Vissa funktioner som svars strömning, HTTP/2 och WebSockets är inte tillgängliga. Vissa komponenter i HTTP-begäran, till exempel vissa huvuden och vägar kan vara begränsade. Ditt program kan också få överdriven [kall start](event-driven-scaling.md#cold-start).
>
> Överväg att köra dina webbappar på [Azure App Service](../app-service/overview.md)för att åtgärda dessa omständigheter.

Följande exempel visar hur du konfigurerar en HTTP-utlöst funktion utan ytterligare bindningar eller utdata. Scenariot som implementeras i det här exemplet innehåller en funktion `hello` som heter som accepterar en `GET` eller `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementering

I en mapp med namnet *Hej* konfigurerar *function.jsfilen för* http-utlöst funktion.

**Hej/function.jspå**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Funktionen har kon figurer ATS för att acceptera både- `GET` och- `POST` begär Anden och resultatvärdet anges via ett argument med namnet `res` .

I roten av appen är *host.jspå* filen konfigurerad för att köras `handler.exe` och `enableForwardingHttpRequest` har angetts till `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

`enableForwardingHttpRequest` `true` I så fall skiljer sig beteendet för http-funktioner från standard beteendet för anpassade hanterare på följande sätt:

* HTTP-begäran innehåller inte de anpassade hanterings [begär Anden som begär](#request-payload) nytto Last. I stället anropar Functions-hanteraren hanteraren med en kopia av den ursprungliga HTTP-begäran.
* Funktions värden anropar hanteraren med samma sökväg som den ursprungliga begäran, inklusive parametrar för frågesträng.
* Funktions värden returnerar en kopia av hanterarens HTTP-svar som svar på den ursprungliga begäran.

Följande är en POST-begäran till Functions-värden. Funktions värden skickar sedan en kopia av begäran till den anpassade hanteraren på samma sökväg.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

Filen fil *hanterare. go* implementerar en webb server och en http-funktion.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

I det här exemplet skapar den anpassade hanteraren en webb server som hanterar HTTP-händelser och är inställd på att lyssna efter begär Anden via `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` begär Anden hanteras genom att returnera en sträng och `POST` begär Anden har åtkomst till begär ande texten.

Vägen för funktionen order här är `/api/hello` samma som den ursprungliga begäran.

>[!NOTE]
>`FUNCTIONS_CUSTOMHANDLER_PORT`Är inte den offentliga porten som används för att anropa funktionen. Den här porten används av Functions-värden för att anropa den anpassade hanteraren.

## <a name="deploying"></a>Deploy

En anpassad hanterare kan distribueras till alla Azure Functions-värd alternativ. Om hanteraren kräver operativ system-eller plattforms beroenden (till exempel språk körning) kan du behöva använda en [anpassad behållare](./functions-create-function-linux-custom-image.md).

När du skapar en Function-app i Azure för anpassade hanterare rekommenderar vi att du väljer .NET Core som stack. En anpassad stack för anpassade hanterare kommer att läggas till i framtiden.

Om du vill distribuera en app för anpassad hanterare med Azure Functions Core Tools kör du följande kommando.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Se till att alla filer som krävs för att köra den anpassade hanteraren finns i mappen och ingår i distributionen. Om din anpassade hanterare är en binär körbar fil eller har plattformsspecifika beroenden ser du till att dessa filer matchar mål distributions plattformen.

## <a name="restrictions"></a>Begränsningar

- Webb servern för anpassad hanterare måste starta inom 60 sekunder.

## <a name="samples"></a>Exempel

Exempel på hur du implementerar funktioner på flera olika språk hittar du i [exemplen för anpassade hanterare GitHub lagrings platsen](https://github.com/Azure-Samples/functions-custom-handlers) .

## <a name="troubleshooting-and-support"></a>Fel sökning och support

### <a name="trace-logging"></a>Spårnings loggning

Om processen för anpassad hanterare inte kan starta eller om det uppstår problem med att kommunicera med Functions-värden kan du öka funktions programmets loggnings nivå för `Trace` att se fler diagnostiska meddelanden från värden.

Om du vill ändra funktions programmets standard loggnings nivå konfigurerar du `logLevel` inställningen i `logging` avsnittet i *host.jspå*.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

Funktions värden utvärderar extra logg meddelanden inklusive information som rör den anpassade hanterings processen. Använd loggarna för att undersöka problem med att starta en anpassad hanterare-process eller anropa funktioner i din anpassade hanterare.

Lokalt skrivs loggar ut till-konsolen.

I Azure, [fråga Application Insights spårningar](analyze-telemetry-data.md#query-telemetry-data) för att Visa logg meddelanden. Om din app genererar en hög mängd loggar, skickas bara en del av logg meddelanden till Application Insights. [Inaktivera sampling](configure-monitoring.md#configure-sampling) för att se till att alla meddelanden loggas.

### <a name="test-custom-handler-in-isolation"></a>Testa anpassad hanterare i isolering

Appar för anpassad hantering är en webb Server process, så det kan vara bra att starta den på ett eget och testa funktions anrop genom att skicka modeller av [http-begäranden](#request-payload) med ett verktyg som t. ex. [vändning](https://curl.haxx.se/) eller [Postman](https://www.postman.com/).

Du kan också använda den här strategin i dina CI/CD-pipelines för att köra automatiserade tester på din anpassade hanterare.

### <a name="execution-environment"></a>Körnings miljö

Anpassade hanterare körs i samma miljö som en typisk Azure Functions app. Testa din hanterare för att säkerställa att miljön innehåller alla beroenden som krävs för att köra. För appar som kräver ytterligare beroenden kan du behöva köra dem med hjälp av en [anpassad behållar avbildning](functions-create-function-linux-custom-image.md) som finns på Azure Functions [Premium-plan](functions-premium-plan.md).

### <a name="get-support"></a>Få support

Om du behöver hjälp med anpassade hanterare i en Function-app kan du skicka en begäran via vanliga Support kanaler. På grund av de många möjliga språken som används för att bygga anpassade hanterings appar är stödet dock inte obegränsat.

Support är tillgängligt om functions-värden har problem med att starta eller kommunicera med den anpassade hanterings processen. För problem som är specifika för den egna bearbetningen av din anpassade hanterings process, till exempel problem med det valda språket eller ramverket, kan support teamet inte tillhandahålla hjälp i det här sammanhanget.

## <a name="next-steps"></a>Nästa steg

Kom igång med att skapa en Azure Functions app i Go eller Rust med den [anpassade hanterings](create-first-function-vs-code-other.md)guiden.
