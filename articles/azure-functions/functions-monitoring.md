---
title: Övervaka Azure Functions
description: Lär dig hur du använder Azure Application insikter med Azure Functions för att övervaka funktions körning.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 6dc3c582466fe0646b0350213947ff2173368bde
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936846"
---
# <a name="monitor-azure-functions"></a>Övervaka Azure Functions

[Azure Functions](functions-overview.md) erbjuder inbyggd integrering med [Azure Application insikter](../azure-monitor/app/app-insights-overview.md) för att övervaka funktioner. Den här artikeln innehåller en översikt över de övervaknings funktioner som tillhandahålls av Azure för övervakning Azure Functions.

Application Insights samlar in logg-, prestanda-och fel data. Genom att automatiskt identifiera prestanda avvikelser och med kraftfulla analys verktyg kan du enkelt diagnostisera problem och bättre förstå hur dina funktioner används. Dessa verktyg är utformade för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet för dina funktioner. Du kan även använda Application Insights under utveckling av program projekt i lokal funktion. Mer information finns i [Vad är Application Insights?](../azure-monitor/app/app-insights-overview.md).

Eftersom Application Insights instrumentering är inbyggt i Azure Functions behöver du en giltig Instrumentation-nyckel för att ansluta din app till en Application Insights resurs. Instrumentation-nyckeln läggs till i dina program inställningar när du skapar din Function app-resurs i Azure. Om din Function-app inte redan har den här nyckeln kan du [Ange den manuellt](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Application Insights priser och begränsningar

Du kan testa Application Insights integration med Azure Functions kostnads fritt med en daglig gräns för hur mycket data som bearbetas kostnads fritt.

Om du aktiverar program insikter under utvecklingen kan du trycka på den här gränsen under testningen. Azure tillhandahåller Portal-och e-postaviseringar när du närmar dig den dagliga gränsen. Om du saknar dessa aviseringar och nått gränsen visas inte nya loggar i Application Insights frågor. Var medveten om gränsen för att undvika onödig fel söknings tid. Mer information finns [i hantera priser och data volymer i Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights har en [samplings](../azure-monitor/app/sampling.md) funktion som kan skydda dig från att skapa för mycket telemetri-data vid slutförda körningar vid tider med hög belastning. Sampling är aktiverat som standard. Om du verkar saknas data kan du behöva justera samplings inställningarna så att de passar just ditt övervaknings scenario. Läs mer i [Konfigurera sampling](configure-monitoring.md#configure-sampling).

Den fullständiga listan med Application Insights funktioner som är tillgängliga för din Function-app beskrivs i [Application Insights för Azure Functions funktioner som stöds](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="application-insights-integration"></a>Application Insights-integrering

Normalt skapar du en Application Insights-instans när du skapar din Function-app. I det här fallet har Instrumentation-nyckeln som krävs för integrering redan angetts som en program inställning med namnet *APPINSIGHTS_INSTRUMENTATIONKEY*. Om det av någon anledning inte finns någon Instrumentation-nyckel angiven, måste du [aktivera Application Insights-integrering](configure-monitoring.md#enable-application-insights-integration).  

## <a name="collecting-telemetry-data"></a>Samlar in telemetridata

När Application Insights integrering har Aktiver ATS skickas telemetridata till den anslutna Application Insights-instansen. Dessa data innehåller loggar som genereras av Functions-värden, spår som skrivits från funktions koden och prestanda data. 

>[!NOTE]
>Förutom data från funktionerna och funktionerna kan du även samla in data från [skal kontrolls styrenheten för funktioner](#scale-controller-logs).   

### <a name="log-levels-and-categories"></a>Logg nivåer och kategorier

När du skriver spår från program koden bör du tilldela en logg nivå till spårningarna. Logg nivåer gör det möjligt för dig att begränsa mängden data som samlas in från dina spår.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Mer information om loggnings nivåer finns i [Konfigurera logg nivåer](configure-monitoring.md#configure-log-levels).

Genom att tilldela inloggade objekt till en kategori får du mer kontroll över telemetri som genereras från vissa källor i din Function-app. Kategorier gör det enklare att köra analyser över insamlade data. Spår som skrivs från funktions koden tilldelas enskilda kategorier baserat på funktions namnet. Mer information om kategorier finns i [Konfigurera kategorier](configure-monitoring.md#configure-categories).

### <a name="custom-telemetry-data"></a>Anpassade telemetridata

I [C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) och [Java Script](functions-reference-node.md#log-custom-telemetry)kan du använda en Application Insights SDK för att skriva anpassade telemetridata.

### <a name="dependencies"></a>Beroenden

Från och med version 2. x av funktionerna samlar körnings programmet automatiskt in data på beroenden för bindningar som använder vissa klient-SDK: er. Application Insights samlar in data i följande beroenden:

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Azure Storage tjänster (BLOB, kö och tabell)

HTTP-begäranden och databas anrop med `SqlClient` registreras också. En fullständig lista över beroenden som stöds av Application Insights finns i [automatiskt spårade beroenden](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

Application Insights genererar en _program karta_ över insamlade beroende data. Följande är ett exempel på en program karta över en HTTP-utlösare med en kö Storage utgående bindning.  

![Program karta med beroende](./media/functions-monitoring/app-map.png)

Beroenden skrivs på `Information` nivån. Om du filtrerar på `Warning` eller ovanför visas inte beroende data. Automatisk insamling av beroenden sker också i en icke-användar omfattning. Om du vill avbilda beroende data kontrollerar du att nivån är inställd på minst `Information` utanför användar omfånget ( `Function.<YOUR_FUNCTION_NAME>.User` ) i värden.

Förutom automatisk beroende data insamling kan du också använda en av de språkspecifika Application Insights SDK: erna för att skriva anpassad beroende information till loggarna. Ett exempel på hur du skriver anpassade beroenden finns i något av följande språkspecifika exempel:

+ [Logga anpassad telemetri i C#-funktioner](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Logga anpassad telemetri i JavaScript-funktioner](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Skriver till loggar 

Hur du skriver till loggar och de API: er som du använder beror på vilket språk som används i ditt program projekt för function.   
Mer information om hur du skriver loggar från dina funktioner finns i Guide för utvecklare för ditt språk.

+ [C# (.NET-klass bibliotek)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="streaming-logs"></a>Strömmande loggar

När du utvecklar ett program vill du ofta se vad som skrivs till loggarna i nära real tid när de körs i Azure.

Det finns två sätt att visa en ström av loggdata som genereras av dina funktions körningar.

* **Inbyggd logg strömning**: app Services plattformen gör att du kan visa en ström med dina program loggs-filer. Den här strömmen motsvarar de utdata som visas när du felsöker dina funktioner under [lokal utveckling](functions-develop-local.md) och när du använder fliken **test** i portalen. All logg-baserad information visas. Mer information finns i [Stream-loggar](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Den här streaming-metoden har endast stöd för en enda instans och kan inte användas med en app som körs på Linux i en förbruknings plan.

* **Live Metrics Stream**: när din Function-app är [ansluten till Application Insights](configure-monitoring.md#enable-application-insights-integration)kan du Visa loggdata och andra mått i nära real tid i Azure Portal med hjälp av [Live Metrics Stream](../azure-monitor/app/live-stream.md). Använd den här metoden när du övervakar funktioner som körs på flera instanser eller i Linux i en förbruknings plan. Den här metoden använder [exempel data](configure-monitoring.md#configure-sampling).

Logg strömmar kan visas både i portalen och i de flesta lokala utvecklings miljöer. Information om hur du aktiverar logg strömmar finns i [Aktivera strömnings körnings loggar i Azure Functions](streaming-logs.md).

## <a name="diagnostic-logs"></a>Diagnostikloggar

_Den här funktionen är i förhandsversion._ 

Med Application Insights kan du exportera telemetridata till långsiktig lagring eller andra Analysis Services.  

Eftersom Functions även integreras med Azure Monitor, kan du också använda diagnostikinställningar för att skicka telemetridata till olika destinationer, inklusive Azure Monitor loggar. Mer information finns i [övervaknings Azure Functions med Azure Monitor loggar](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>Loggar för skalnings styrenhet

_Den här funktionen är i förhandsversion._ 

Den [Azure Functions skalnings styrenheten](./event-driven-scaling.md#runtime-scaling) övervakar instanser av Azure Functions värden som appen körs på. Den här kontrollanten fattar beslut om när du ska lägga till eller ta bort instanser utifrån aktuella prestanda. Du kan låta skalnings styrenheten generera loggar till Application Insights för att bättre förstå de beslut som skalnings styrenheten gör för din Function-app. Du kan också lagra de genererade loggarna i Blob Storage för analys av en annan tjänst. 

Om du vill aktivera den här funktionen kan du lägga till en program inställning med namnet `SCALE_CONTROLLER_LOGGING_ENABLED` på dina funktioner i appens inställningar. Mer information finns i [Konfigurera loggar för skalnings styrenhet](configure-monitoring.md#configure-scale-controller-logs).

## <a name="report-issues"></a>Rapportera problem

[Skapa ett ärende i GitHub](https://github.com/Azure/Azure-Functions/issues/new)för att rapportera ett problem med Application Insights-integrering i functions eller för att göra ett förslag eller en begäran.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core loggning](/aspnet/core/fundamentals/logging/)
