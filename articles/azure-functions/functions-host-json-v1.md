---
title: host.jssom referens för Azure Functions 1. x
description: Referens dokumentation för Azure Functions host.jspå filen med v1-körningsmiljön.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 48dba50b384731befdc7fba7c418e542994cedd9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608962"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host.jssom referens för Azure Functions 1. x

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

Filen *host.jspå* metadatafilen innehåller globala konfigurations alternativ som påverkar alla funktioner för en Function-app. I den här artikeln visas de inställningar som är tillgängliga för v1-körningen. JSON-schemat är på http://json.schemastore.org/host .

> [!NOTE]
> Den här artikeln är för Azure Functions 1. x.  En referens för host.jsi functions 2. x och senare finns i [host.jsreferens för Azure Functions 2. x](functions-host-json.md).

Andra konfigurations alternativ för Function-appar hanteras i dina [app-inställningar](functions-app-settings.md).

Vissa host.jsi inställningar används endast när de körs lokalt i [local.settings.js](functions-run-local.md#local-settings-file) filen.

## <a name="sample-hostjson-file"></a>Exempel host.jspå fil

Följande exempel *host.jspå* filer har alla möjliga alternativ angivna.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

I följande avsnitt i den här artikeln beskrivs varje toppnivå egenskap. Alla är valfria om inget annat anges.

## <a name="aggregator"></a>aggregera

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Konfigurations inställningar för [Azure Cosmos DB utlösare och bindningar](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------|
|GatewayMode|Gateway|Anslutnings läget som används av funktionen vid anslutning till Azure Cosmos DBs tjänsten. Alternativen är `Direct` och `Gateway`|
|Protokoll|Https|Anslutnings protokollet som används av funktionen vid anslutning till Azure Cosmos DBs tjänsten.  Läs [här om du vill ha en förklaring av båda lägena](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|saknas|Lease-prefix som ska användas för alla funktioner i en app.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Konfigurations inställningar för [Event Hub-utlösare och bindningar](functions-bindings-event-hubs.md#functions-1x).

## <a name="functions"></a>funktionen

En lista med funktioner som jobb värden kör. En tom matris innebär att köra alla-funktioner. Endast avsedd att användas när du [Kör lokalt](functions-run-local.md). I Function-appar i Azure bör du i stället följa stegen i [så här inaktiverar du funktioner i Azure Functions](disable-function.md) för att inaktivera vissa funktioner i stället för att använda den här inställningen.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Anger varaktigheten för alla funktioner. I en server lös förbruknings plan är det giltiga intervallet från 1 sekund till 10 minuter och standardvärdet är 5 minuter. I en App Service plan finns det ingen övergripande gräns och standardvärdet är _Null_, vilket innebär att ingen tids gräns anges.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Konfigurations inställningar för [övervakaren av värd hälsa](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|enabled|true|Anger om funktionen är aktive rad. | 
|healthCheckInterval|10 sekunder|Tidsintervallet mellan de regelbundna hälso kontrollerna i bakgrunden. | 
|healthCheckWindow|2 minuter|Ett glidande tids fönster som används tillsammans med `healthCheckThreshold` inställningen.| 
|healthCheckThreshold|6|Maximalt antal gånger som hälso kontrollen kan återställas innan en återkallning av en värd initieras.| 
|counterThreshold|0,80|Tröskelvärdet som en prestanda räknare kommer att anses vara ohälsosam.| 

## <a name="http"></a>http

Konfigurations inställningar för [http-utlösare och bindningar](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|dynamicThrottlesEnabled|falskt|När den här inställningen är aktive rad kommer pipelinen för bearbetning av begär Anden att regelbundet kontrol lera system prestanda räknare som anslutningar/trådar/processer/minne/processor/osv. om någon av dessa räknare är över en inbyggd hög tröskel (80%), avvisas begär Anden med en 429 "upptagen"-svar tills räknarna återgår till normala nivåer.|
|maxConcurrentRequests|obunden ( `-1` )|Maximalt antal HTTP-funktioner som ska köras parallellt. På så sätt kan du kontrol lera samtidighet, vilket kan hjälpa dig att hantera resursutnyttjande. Du kan till exempel ha en HTTP-funktion som använder många system resurser (minne/processor/Sockets) så att den orsakar problem när samtidigheten är för hög. Eller så kanske du har en funktion som gör utgående begär anden till en tjänst från tredje part, och dessa anrop måste vara begränsade. I dessa fall kan det hjälpa att tillämpa en begränsning.|
|maxOutstandingRequests|obunden ( `-1` )|Det maximala antalet väntande begär Anden som innehas vid en specifik tidpunkt. Den här gränsen omfattar begär Anden som har placerats i kö, men som inte har börjat köras, samt alla pågående körningar. Inkommande begär anden över den här gränsen avvisas med ett svar på 429 "för upptagen". Det gör det möjligt för anropare att använda tidsbaserade återförsöks strategier och hjälper dig också att kontrol lera maximal fördröjning för begäran. Detta styr endast köer som inträffar inom skript värdens körnings Sök väg. Andra köer, t. ex. ASP.NET, kommer fortfarande att gälla och påverkas inte av den här inställningen.|
|routePrefix|api|Det väg-prefix som gäller för alla vägar. Använd en tom sträng för att ta bort standardprefixet. |

## <a name="id"></a>id

Unikt ID för en jobb värd. Kan vara ett GUID för gemener/versaler med streck borttagna. Krävs när du kör lokalt. När du kör i Azure rekommenderar vi att du inte anger något ID-värde. Ett ID genereras automatiskt i Azure när `id` har utelämnats. 

Om du delar ett lagrings konto över flera Function-appar, se till att varje Function-app har en annan `id` . Du kan utelämna `id` egenskapen eller manuellt ange varje funktions program `id` till ett annat värde. Timer-utlösaren använder ett lagrings lås för att säkerställa att det bara kommer att finnas en timer-instans när en Function-app skalar ut till flera instanser. Om två Functions-appar delar samma `id` och var och en använder en timer-utlösare, kommer bara en timer att köras.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>loggar

Styr filtrering av loggar som skrivits av ett [ILogger](functions-dotnet-class-library.md#ilogger) -objekt eller av [context. log](functions-reference-node.md#contextlog-method).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|categoryFilter|saknas|Anger filtrering efter kategori| 
|defaultLevel|Information|För alla kategorier som inte anges i `categoryLevels` matrisen skickar du loggar på denna nivå och över till Application Insights.| 
|categoryLevels|saknas|En matris med kategorier som anger den minsta logg nivå som ska skickas till Application Insights för varje kategori. Den kategori som anges här styr alla kategorier som börjar med samma värde och värden som är längre prioriterade. I föregående exempel *host.jspå* fil, alla kategorier som börjar med "Host. aggregator"-logg på `Information` nivå. Alla andra kategorier som börjar med "Host", till exempel "Host.Executor", log på `Error` nivå.| 

## <a name="queues"></a>kön

Konfigurations inställningar för [utlösare och bindningar i lagrings kön](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|maxPollingInterval|60000|Det maximala intervallet i millisekunder mellan Queue-avsökningar.| 
|visibilityTimeout|0|Tidsintervall mellan återförsök vid bearbetning av ett meddelande Miss lyckas.| 
|batchSize|16|Antalet köa meddelanden som funktions körningen hämtar samtidigt och processer parallellt. När antalet som bearbetas går ned till `newBatchThreshold` Kör körningen en annan batch och börjar bearbeta dessa meddelanden. Det maximala antalet samtidiga meddelanden som bearbetas per funktion är `batchSize` plus `newBatchThreshold` . Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning av meddelanden som tas emot i en kö kan du ange `batchSize` 1. Den här inställningen eliminerar dock ingen samtidighet så länge som din funktions App körs på en enda virtuell dator (VM). Om Function-appen skalar ut till flera virtuella datorer kan varje virtuell dator köra en instans av varje funktion som utlöses av kön.<br><br>Det maximala värdet `batchSize` är 32. | 
|maxDequeueCount|5|Antal försök att bearbeta ett meddelande innan det flyttas till en Poison-kö.| 
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetas samtidigt går till det här talet, hämtar körningen en annan batch.| 

## <a name="sendgrid"></a>SendGrid

Konfigurations inställning för [SendGrind utgående bindning](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
}    
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|Från|saknas|Avsändarens e-postadress för alla funktioner.| 

## <a name="servicebus"></a>Service Bus

Konfigurations inställning för [Service Bus utlösare och bindningar](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till motringning som meddelande pumpen ska initiera. Som standard bearbetar Functions-körningen flera meddelanden samtidigt. Om du vill dirigera körningen så att den bara behandlar en enskild kö eller ett ämnes meddelande i taget, anger `maxConcurrentCalls` du 1. | 
|prefetchCount|saknas|Standard-PrefetchCount som ska användas av den underliggande MessageReceiver.| 
|autoRenewTimeout|00:05:00|Den längsta tid som meddelande låset ska förnyas automatiskt.|
|Automatisk|true|Om värdet är true Slutför utlösaren meddelande bearbetningen automatiskt vid lyckad körning av åtgärden. Om det är falskt är det ansvaret för funktionen att slutföra meddelandet innan det returneras.|

## <a name="singleton"></a>Singleton

Konfigurations inställningar för beteendet singleton lock. Mer information finns i [GitHub problem med singleton-stöd](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|lockPeriod|00:00:15|Den period som funktions nivå lås utförs för. Lås automatisk förnyelse.| 
|listenerLockPeriod|00:01:00|Den period som lyssnarens lås tas för.| 
|listenerLockRecoveryPollingInterval|00:01:00|Det tidsintervall som används för återställning av lyssnar lås om det inte gick att hämta ett lyssnar lås vid start.| 
|lockAcquisitionTimeout|00:01:00|Den maximala tid som körningen kommer att försöka hämta ett lås.| 
|lockAcquisitionPollingInterval|saknas|Intervallet mellan lås försök.| 

## <a name="tracing"></a>spårning

*Version 1. x*

Konfigurations inställningar för loggar som du skapar med hjälp av ett `TraceWriter` objekt. Mer information finns i [C#-loggning].

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|consoleLevel|information|Spårnings nivån för konsol loggning. Alternativen är: `off` , `error` , `warning` , `info` och `verbose` .|
|fileLoggingMode|debugOnly|Spårnings nivån för fil loggning. Alternativen är `never` , `always` , `debugOnly` .| 

## <a name="watchdirectories"></a>watchDirectories

En uppsättning [delade kod kataloger](functions-reference-csharp.md#watched-directories) som ska övervakas för ändringar.  Säkerställer att när kod i dessa kataloger ändras, hämtas ändringarna av funktionerna.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du uppdaterar host.jspå filen](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Se globala inställningar i miljövariabler](functions-app-settings.md)
