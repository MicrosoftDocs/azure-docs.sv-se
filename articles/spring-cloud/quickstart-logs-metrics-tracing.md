---
title: Snabbstart – Övervaka Azure Spring Cloud appar med loggar, mått och spårning
description: Använd loggströmning, logganalys, mått och spårning för att övervaka Samplingymetrics-exempelappar på Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 907bf06323d13b2d26dec5003e4739f2ae9faf74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378524"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Snabbstart: Övervaka Azure Spring Cloud appar med loggar, mått och spårning

::: zone pivot="programming-language-csharp"
Med den inbyggda övervakningsförmågan i Azure Spring Cloud kan du felsöka och övervaka komplexa problem. Azure Spring Cloud integrerar Distribuerad tracing [för](https://steeltoe.io/docs/3/tracing/distributed-tracing) Steeltoe med Azures [Application Insights](../azure-monitor/app/app-insights-overview.md). Den här integreringen ger kraftfulla loggar, mått och funktioner för distribuerad spårning från Azure Portal.

I följande procedurer förklaras hur du använder Log Streaming, Log Analytics, Metrics och Distributed Tracing med exempelappen som du distribuerade i föregående snabbstarter.

## <a name="prerequisites"></a>Förutsättningar

* Slutför de föregående snabbstarterna i den här serien:

  * [Etablera Azure Spring Cloud tjänsten](spring-cloud-quickstart-provision-service-instance.md).
  * [Konfigurera Azure Spring Cloud konfigurationsservern](spring-cloud-quickstart-setup-config-server.md).
  * [Skapa och distribuera appar](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Loggar

Det finns två sätt att se loggar på Azure Spring Cloud: **Loggströmning** av realtidsloggar per appinstans eller **Log Analytics** för aggregerade loggar med avancerad frågefunktion.

### <a name="log-streaming"></a>Loggströmning

Du kan använda loggströmning i Azure CLI med följande kommando.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Du ser utdata som liknar följande exempel:

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> Använd `az spring-cloud app logs -h` för att utforska fler parametrar och funktioner för loggströmmar.

### <a name="log-analytics"></a>Log Analytics

1. I Azure Portal går du till **| Översiktssidan** och **välj Loggar** i **avsnittet** Övervakning. Välj **Kör** på någon av exempelfrågorna för Azure Spring Cloud.

   [![Logs Analytics-post ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Redigera frågan för att ta bort where-satserna som begränsar visningen till varnings- och felloggar.

1. Välj sedan `Run` så visas loggarna. Mer [information om hur du skriver frågor](../azure-monitor/logs/get-started-queries.md) finns i Azure Log Analytics-dokument.

   [![Logs Analytics-fråga –Toe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

1. Mer information om frågespråket som används i Log Analytics finns i [Azure Monitor och loggfrågor.](/azure/data-explorer/kusto/query/) Om du vill fråga efter alla Log Analytics-loggar från en centraliserad klient kan du [läsa Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="metrics"></a>Mått

1. I Azure Portal går du till **| Översiktssidan** och **välj Mått** i **avsnittet** Övervakning. Lägg till ditt första mått genom att välja ett av .NET-måtten under  **Prestanda (.NET)** eller Begäran **(.NET)** i listrutan Mått och för Sammansättning för att visa tidslinjen för `Avg` måttet. 

   [![Posten Mått –Toe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Klicka **på Lägg** till filter i verktygsfältet och välj om du vill visa endast `App=solar-system-weather` **CPU-användning för appen solar-system-weather.**

   [![Använda filter i mått – Kanttoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Stäng filtret som skapades i föregående steg, välj Tillämpa **uppdelning** och välj värden för `App` **att** se CPU-användning av olika appar.

   [![Använda uppdelning i mått – Metalltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Distribuerad spårning

1. I Azure Portal går du till **| Översiktssidan** och **välj Distribuerad spårning** i **avsnittet** Övervakning. Välj sedan **fliken Visa programkarta** till höger.

   [![Post för distribuerad spårning – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Nu kan du se status för anrop mellan appar. 

   [![Översikt över distribuerad spårning – Kanttoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Välj länken mellan **solar-system-weather och** **planet-weather-provider** för att se mer information som långsammaste anrop med HTTP-metoder.

   [![Distribuerad spårning – Metalltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Välj slutligen Undersök **prestanda för** att utforska mer kraftfulla inbyggda prestandaanalyser.

   [![Prestanda för distribuerad spårning – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Med den inbyggda övervakningsförmågan i Azure Spring Cloud kan du felsöka och övervaka komplexa problem. Azure Spring Cloud [integrerar Spring Cloud Suvth](https://spring.io/projects/spring-cloud-sleuth) med Azures [Application Insights](../azure-monitor/app/app-insights-overview.md). Den här integreringen ger kraftfulla loggar, mått och funktioner för distribuerad spårning från Azure Portal. I följande procedurer förklaras hur du använder Log Streaming, Log Analytics, Metrics och distribuerad spårning med distribuerade SymmetryMetrics-appar.

## <a name="prerequisites"></a>Förutsättningar

Slutför föregående steg: 

* [Etablera en instans av Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Konfigurera konfigurationsservern](spring-cloud-quickstart-setup-config-server.md)
* [Skapa och distribuera appar](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Loggar

Det finns två sätt att se loggar på Azure Spring Cloud: **Loggströmning** av realtidsloggar per appinstans eller **Log Analytics** för aggregerade loggar med avancerad frågefunktion.

### <a name="log-streaming"></a>Loggströmning

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Du kan använda loggströmning i Azure CLI med följande kommando.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Du ser loggar som dessa:

[![Loggströmning från Azure CLI ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Använd `az spring-cloud app logs -h` för att utforska fler parametrar och funktioner för loggströmmar.

#### <a name="intellij"></a>[Intellij](#tab/IntelliJ)

Hämta loggarna med hjälp av Azure Toolkit for IntelliJ:

1. Välj **Azure Explorer** och **Spring Cloud**.

1. Högerklicka på appen som körs.

1. Välj **Strömningsloggar** i listrutan.

   ![Välj strömningsloggar](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Välj **Instans**.

   ![Välj instans](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Strömningsloggen visas i utdatafönstret.

   ![Strömmande loggutdata](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Gå till **| Översiktssidan** och **välj Loggar** i **avsnittet** Övervakning. Klicka **på** Kör på någon av exempelfrågorna för Azure Spring Cloud. 

   [![Logs Analytics-post ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Sedan visas filtrerade loggar. Mer [information om hur du skriver frågor](../azure-monitor/logs/get-started-queries.md) finns i Azure Log Analytics-dokument.

   [![Logs Analytics-fråga ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

1. Mer information om frågespråket som används i Log Analytics finns i [Azure Monitor och loggfrågor.](/azure/data-explorer/kusto/query/) Om du vill fråga efter alla Log Analytics-loggar från en centraliserad klient kan du [läsa Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="metrics"></a>Mått

1. Gå till **| Översiktssidan** och **välj Mått** i **avsnittet** Övervakning. Lägg till ditt första mått genom att `system.cpu.usage` välja **Mått och** för `Avg` Sammansättning **för** att se tidslinjen för total CPU-användning.

   [![Posten Mått ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Klicka **på Lägg** till filter i verktygsfältet ovan och välj för att visa endast `App=Gateway` CPU-användning för **gatewayappen.**

   [![Använda filter i mått ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Stäng filtret som skapades ovan, klicka på **Tillämpa delning** och välj Värden `App` för **att** se CPU-användning av olika appar.

   [![Använda delning i mått ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Distribuerad spårning

1. Gå till **tjänst-| Översiktssidan** och välj **Distribuerad spårning** i **avsnittet** Övervakning. Klicka sedan på **fliken Visa programkarta** till höger.

   [![Post för distribuerad spårning ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Nu kan du se status för anrop mellan Programymetrics-appar. 

   [![Översikt över distribuerad spårning ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Klicka på länken mellan **gateway och** account-service om du vill se mer information om långsammaste anrop med **HTTP-metoder.**

   [![Distribuerad spårning ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Klicka slutligen på **Undersök prestanda** för att utforska mer kraftfulla inbyggda prestandaanalyser.

   [![Prestanda för distribuerad spårning ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

I de här snabbstarterna skapade du Azure-resurser som fortsätter att ackumulera avgifter om de finns kvar i din prenumeration. Om du inte förväntar dig att behöva dessa resurser i framtiden tar du bort resursgruppen med hjälp av portalen eller genom att köra följande kommando i Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

I en tidigare snabbstart anger du även standardresursgruppens namn. Om du inte planerar att fortsätta till nästa snabbstart rensar du ut standardvärdet genom att köra följande CLI-kommando:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Nästa steg

Mer information om övervakningsfunktionerna i Azure Spring Cloud finns i:

> [!div class="nextstepaction"]
> [Diagnostiktjänster](diagnostic-services.md)
>
> [Distribuerad spårning](spring-cloud-howto-distributed-tracing.md)
>
> [Strömma loggar i realtid](spring-cloud-howto-log-streaming.md)
