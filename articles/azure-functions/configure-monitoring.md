---
title: Konfigurera övervakning för Azure Functions
description: Lär dig hur du ansluter din Function-app för att Application Insights övervakning och hur du konfigurerar data insamling.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 77cc87e44990f471ef38871c0225835660779a3c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825749"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Så här konfigurerar du övervakning för Azure Functions

Azure Functions integreras med Application Insights för att bättre kunna övervaka dina funktions appar. Application Insights är en funktion i Azure Monitor, är en utöknings bar APM-tjänst (Application Performance Management) som samlar in data som genererats av din Function-app, inklusive information som appen skriver till loggar. Application Insights integration aktive ras vanligt vis när din Function-app skapas. Om din app inte har kon figurer ATS, måste du först [aktivera Application Insights-integrering](#enable-application-insights-integration). 

Du kan använda Application Insights utan någon anpassad konfiguration. Standard konfigurationen kan resultera i stora data mängder. Om du använder en Azure-prenumeration från Visual Studio kan du träffa din data Kap för Application Insights. Mer information om Application Insights kostnader finns i [Hantera användning och kostnader för Application Insights](../azure-monitor/app/pricing.md).

Senare i den här artikeln får du lära dig hur du konfigurerar och anpassar de data som dina funktioner skickar till Application Insights. För en Function-app konfigureras loggning i [host.js] i filen. 

> [!NOTE]
> Du kan använda särskilt konfigurerade program inställningar för att representera specifika inställningar i en host.jspå fil för en viss miljö. Detta gör att du effektivt kan ändra host.jspå inställningar utan att behöva publicera om host.jspå filen i projektet. Läs mer i [åsidosätt host.jspå värden](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Konfigurera kategorier

Azure Functions loggen innehåller en *kategori* för varje logg. Kategorin visar vilken del av körnings koden eller din funktions kod som skrev loggen. Kategorier skiljer sig mellan version 1. x och senare versioner. Följande diagram beskriver de huvudsakliga kategorierna för loggar som körs av körnings miljön. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

| Kategori | Tabell | Beskrivning |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **relation**| Beroende data samlas in automatiskt för vissa tjänster. För lyckade körningar finns dessa loggar på `Information` nivån. Läs mer i [beroenden](functions-monitoring.md#dependencies). Undantagen loggas på `Error` nivån. Körningen skapar också `Warning` nivå loggar, till exempel när Kömeddelanden skickas till en Poison- [kö](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | Med C#-och JavaScript-SDK: er kan du samla in anpassade mått och logga anpassade händelser. Mer information finns i [anpassade telemetridata](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **Anden**| Inkluderar funktionen startade och slutförda loggar för en speciell funktion körs. För lyckade körningar finns dessa loggar på `Information` nivån. Undantagen loggas på `Error` nivån. Körningen skapar också `Warning` nivå loggar, till exempel när Kömeddelanden skickas till en Poison- [kö](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **Anden**| Loggar som skapats av användare, vilket kan vara vilken logg nivå som helst. Mer information om hur du skriver till loggar från funktionerna finns i [skriva till loggar](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Dessa körnings genererade loggar tillhandahåller antal och medelvärden för funktions anrop under en [konfigurerbar](#configure-the-aggregator) tids period. Standard perioden är 30 sekunder eller 1 000 resultat, beroende på vilket som kommer först. Exempel är antalet körningar, lyckade kostnader och varaktighet. Alla dessa loggar skrivs på `Information` nivå. Om du filtrerar på `Warning` eller ovanför visas inte någon av dessa data. |
| **`Host.Results`** | **begäran** | Dessa loggar som genereras av körning indikerar att en funktion lyckades eller misslyckades. Alla dessa loggar skrivs på `Information` nivå. Om du filtrerar på `Warning` eller ovanför visas inte någon av dessa data. |
| **`Microsoft`** | **Anden** | Fullständigt kvalificerad logg kategori som motsvarar en .NET Runtime-komponent som anropas av värden.  |
| **`Worker`** | **Anden** | Loggar som genereras av språk arbets processen för non-.NET språk. Språk arbets loggar kan också loggas i en `Microsoft.*` kategori, till exempel `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` . De här loggarna skrivs på `Information` nivå.|

> [!NOTE]
> För funktioner i .NET-klass bibliotek förutsätter dessa kategorier att du använder `ILogger` och inte `ILogger<T>` . Mer information finns i dokumentationen för [Functions ILogger](functions-dotnet-class-library.md#ilogger). 

# <a name="v1x"></a>[v1. x](#tab/v1)

| Kategori | Tabell | Beskrivning |
| ----- | ----- | ----- |
| **`Function`** | **Anden**| Loggar som skapats av användare, vilket kan vara vilken logg nivå som helst. Mer information om hur du skriver till loggar från funktionerna finns i [skriva till loggar](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Dessa körnings genererade loggar tillhandahåller antal och medelvärden för funktions anrop under en [konfigurerbar](#configure-the-aggregator) tids period. Standard perioden är 30 sekunder eller 1 000 resultat, beroende på vilket som kommer först. Exempel är antalet körningar, lyckade kostnader och varaktighet. Alla dessa loggar skrivs på `Information` nivå. Om du filtrerar på `Warning` eller ovanför visas inte någon av dessa data. |
| **`Host.Executor`** | **Anden** | Inkluderar **funktionen startad** och **funktionen slutförda** loggar för en speciell funktion körs. För lyckade körningar är dessa loggar `Information` nivå. Undantagen har loggats på `Error` nivå. Körningen skapar också `Warning` nivå loggar, till exempel när Kömeddelanden skickas till en Poison- [kö](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **begäran** | Dessa loggar som genereras av körning indikerar att en funktion lyckades eller misslyckades. Alla dessa loggar skrivs på `Information` nivå. Om du filtrerar på `Warning` eller ovanför visas inte någon av dessa data. |

---

**Tabell** kolumnen visar vilken tabell i Application Insights loggen skrivs. 

## <a name="configure-log-levels"></a>Konfigurera logg nivåer

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

För varje kategori anger du den lägsta logg nivå som ska skickas. Inställningarna för host.jsvarierar beroende på [funktionernas körnings version](functions-versions.md). 

I exemplet nedan definieras loggning baserat på följande regler:

+ För loggar av `Host.Results` eller `Function` , bara logg händelser på `Error` eller en högre nivå. 
+ `Host.Aggregator`Logga alla genererade mått () för loggar av `Trace` .
+ För alla andra loggar, inklusive användar loggar, logga endast `Information` nivå och högre händelser.

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Om [host.jspå] innehåller flera loggar som börjar med samma sträng matchas de mer definierade loggarna först. Tänk på följande exempel som loggar allt i körningen, förutom `Host.Aggregator` på `Error` nivån:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1. x](#tab/v1) 

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

---

Du kan använda en loggnings nivå inställning för `None` att förhindra att några loggar skrivs för en kategori. 

## <a name="configure-the-aggregator"></a>Konfigurera Aggregator

Enligt vad som anges i föregående avsnitt sammanställer körnings miljön data om funktions körningar under en viss tids period. Standard perioden är 30 sekunder eller 1 000 körningar, beroende på vilket som kommer först. Du kan konfigurera den här inställningen i [host.jspå] filen.  Här är ett exempel:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurera sampling

Application Insights har en [samplings](../azure-monitor/app/sampling.md) funktion som kan skydda dig från att skapa för mycket telemetri-data vid slutförda körningar vid tider med hög belastning. När antalet inkommande körningar överskrider ett angivet tröskelvärde börjar Application Insights att ignorera några av de inkommande körningarna slumpmässigt. Standardvärdet för maximalt antal körningar per sekund är 20 (fem i version 1. x). Du kan konfigurera sampling i [host.jspå](./functions-host-json.md#applicationinsights).  Här är ett exempel:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Du kan undanta vissa typer av telemetri från sampling. I det här exemplet är data av typen `Request` och `Exception` exkluderas från sampling. Detta säkerställer att *alla* funktions körningar (begär Anden) och undantag loggas medan andra typer av telemetri är underkastade sampling. 

# <a name="v1x"></a>[v1. x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Mer information finns [i sampling i Application Insights](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>Konfigurera loggar för skalnings styrenhet

_Den här funktionen är i förhandsversion._ 

Du kan låta [Azure Functions Scale Controller](./functions-scale.md#runtime-scaling) generera loggar till antingen Application Insights eller till Blob Storage för att bättre förstå de beslut som skalnings styrenheten gör för din Function-app.

Om du vill aktivera den här funktionen kan du lägga till en program inställning med namnet `SCALE_CONTROLLER_LOGGING_ENABLED` på dina funktioner i appens inställningar. Värdet för den här inställningen måste ha formatet `<DESTINATION>:<VERBOSITY>` , baserat på följande:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Följande Azure CLI-kommando aktiverar till exempel utförlig loggning från skalnings styrenheten till Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

I det här exemplet ersätter `<FUNCTION_APP_NAME>` och `<RESOURCE_GROUP_NAME>` med namnet på din Function-app och resurs gruppens namn. 

Följande Azure CLI-kommando inaktiverar loggning genom att ange utförlighet till `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Du kan också inaktivera loggning genom att ta bort `SCALE_CONTROLLER_LOGGING_ENABLED` inställningen med följande Azure CLI-kommando:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="enable-application-insights-integration"></a>Aktivera Application Insights-integrering

För att en Function-app ska skicka data till Application Insights måste den känna till Instrumentation-nyckeln för en Application Insights resurs. Nyckeln måste vara i en app-inställning med namnet **APPINSIGHTS_INSTRUMENTATIONKEY**.

När du skapar en Function-app [i Azure Portal](functions-create-first-azure-function.md), från kommando raden med hjälp av [Azure Functions Core tools](./create-first-function-cli-csharp.md)eller genom att använda [Visual Studio Code](./create-first-function-vs-code-csharp.md), aktive ras Application Insights integration som standard. Application Insights resursen har samma namn som din Function-app, och den skapas antingen i samma region eller i den närmaste regionen.

### <a name="new-function-app-in-the-portal"></a>Ny function-app i portalen

Om du vill granska Application Insights resurs som skapas väljer du den för att expandera fönstret **Application Insights** . Du kan ändra det **nya resurs namnet** eller välja en annan **plats** i ett [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data.

![Aktivera Application Insights när du skapar en Function-app](media/functions-monitoring/enable-ai-new-function-app.png)

När du väljer **skapa** skapas en Application Insights resurs med din Function-app, som har `APPINSIGHTS_INSTRUMENTATIONKEY` inställningen ange i program inställningar. Allt är klart att sätta igång.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Lägg till i en befintlig Function-app 

Om det inte gick att skapa en Application Insights-resurs med din Function-app, kan du använda följande steg för att skapa resursen. Du kan sedan lägga till Instrumentation-nyckeln från resursen som en [program inställning](functions-how-to-use-azure-function-app-settings.md#settings) i din Function-app.

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **Function-appen** och väljer sedan din Function-app. 

1. Välj **Application Insights inte har kon figurer ATS** överst i fönstret. Om du inte ser den här banderollen kanske din app redan har Application Insights aktive rad.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Aktivera Application Insights från portalen":::

1. Expandera **ändra din resurs** och skapa en Application Insights resurs genom att använda de inställningar som anges i följande tabell.  

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nytt resursnamn** | Unikt namn på App | Det är enklast att använda samma namn som din Function-app, vilket måste vara unikt i din prenumeration. | 
    | **Plats** | Europa, västra | Använd om möjligt samma [region](https://azure.microsoft.com/regions/) som din Function-app eller en som är nära den regionen. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Skapa en Application Insights resurs":::

1. Välj **Använd**. 

   Application Insights resursen skapas i samma resurs grupp och prenumeration som din Function-app. När resursen har skapats stänger du Application Insightss fönstret.

1. I din Function-app väljer du **konfiguration** under **Inställningar** och väljer sedan **program inställningar**. Om du ser en inställning med namnet `APPINSIGHTS_INSTRUMENTATIONKEY` är Application Insights integration aktive rad för din Function-app som körs i Azure. Om den här inställningen inte finns kan du lägga till den med hjälp av din Application Insights Instrumentation-nyckel som värde.

> [!NOTE]
> Tidiga versioner av Functions använde inbyggd övervakning, som inte längre rekommenderas. När du aktiverar Application Insights-integrering för en sådan Function-app måste du också [inaktivera inbyggd loggning](#disable-built-in-logging).  

## <a name="disable-built-in-logging"></a>Inaktivera inbyggd loggning

När du aktiverar Application Insights inaktiverar du den inbyggda loggning som använder Azure Storage. Den inbyggda loggningen är användbar för testning med lätta arbets belastningar, men är inte avsedd för användning med hög belastnings produktion. För produktions övervakning rekommenderar vi Application Insights. Om den inbyggda loggningen används i produktionen kan loggnings posten vara ofullständig på grund av begränsning på Azure Storage.

Om du vill inaktivera den inbyggda loggningen tar du bort `AzureWebJobsDashboard` appens inställning. Information om hur du tar bort inställningar för appar i Azure Portal finns i avsnittet **program inställningar** i [hantera en Function-app](functions-how-to-use-azure-function-app-settings.md#settings). Innan du tar bort appens inställning ska du se till att inga befintliga funktioner i samma Function-app använder inställningen för Azure Storage utlösare eller bindningar.

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning finns i:

+ [Övervaka Azure Functions](functions-monitoring.md)
+ [Analysera Azure Functions telemetridata i Application Insights](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.jspå]: functions-host-json.md
