---
title: Snabb start – övervakning av Azure våren-molnappar med loggar, mått och spårning
description: Använd logg strömning, logg analys, mått och spårning för att övervaka Piggymetrics-testappar i Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 75c50e6342a00ef37215d9e961503dd7ffb0d205
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879337"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Snabb start: övervaka Azure våren Cloud-appar med loggar, mått och spårning

::: zone pivot="programming-language-csharp"
Med den inbyggda övervaknings funktionen i Azure våren-molnet kan du felsöka och övervaka komplexa problem. Azure våren Cloud integrerar Steeltoe- [distribuerad spårning](https://steeltoe.io/docs/3/tracing/distributed-tracing) med azures [Application Insights](../azure-monitor/app/app-insights-overview.md). Den här integrationen tillhandahåller kraftfulla loggar, mått och distribuerad spårnings funktion från Azure Portal.

Följande procedurer beskriver hur du använder logg strömning, Log Analytics, mått och distribuerad spårning med den exempel-app som du distribuerade i föregående snabb starter.

## <a name="prerequisites"></a>Förutsättningar

* Slutför föregående snabb starter i den här serien:

  * [Etablera Azure våren Cloud Service](spring-cloud-quickstart-provision-service-instance.md).
  * [Konfigurera konfigurations servern för Azure våren Cloud](spring-cloud-quickstart-setup-config-server.md).
  * [Bygg och distribuera appar](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Loggar

Det finns två sätt att se loggar i Azure våren Cloud: **logg strömning** av real tids loggar per app-instans eller **Log Analytics** för sammanställda loggar med avancerad fråge funktion.

### <a name="log-streaming"></a>Logg strömning

Du kan använda logg strömning i Azure CLI med följande kommando.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Du kommer att se utdata som liknar följande exempel:

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
> Använd `az spring-cloud app logs -h` för att utforska fler parametrar och logg Ströms funktioner.

### <a name="log-analytics"></a>Log Analytics

1. I Azure Portal går du till **tjänsten | Sidan översikt** och välj **loggar** i avsnittet **övervakning** . Välj **Kör** på en av exempel frågorna för Azure våren Cloud.

   [![Loggar Analytics-post ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Redigera frågan för att ta bort WHERE-satserna som begränsar visningen till varnings-och fel loggar.

1. Välj sedan så visas `Run` loggar. I [Azure Log Analytics-dokument](../azure-monitor/logs/get-started-queries.md) finns mer information om hur du skriver frågor.

   [![Loggar Analytics-fråga – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>Mått

1. I Azure Portal går du till **tjänsten | Sidan översikt** och välj **mått** i avsnittet **övervakning** . Lägg till ditt första mått genom att välja ett av .NET-måtten under **prestanda (.net)** eller **begär (.net)** i list rutan **mått** och `Avg` för **agg regering** att Visa tids linjen för det måttet.

   [![Mått poster – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Klicka på **Lägg till filter** i verktygsfältet, Välj `App=solar-system-weather` för att se CPU-användning enbart för **solsystem – väder-** appen.

   [![Använd filter i mått – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Ignorera filtret som du skapade i föregående steg, Välj **tillämpa delning** och välj `App` för **värden** för att se CPU-användning av olika appar.

   [![Använd delning i mått – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Distribuerad spårning

1. I Azure Portal går du till **tjänsten | Översikts** sida och välj **distribuerad spårning** i avsnittet **övervakning** . Välj sedan fliken **Visa program karta** till höger.

   [![Distributed tracing-post – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Nu kan du se status för anrop mellan appar. 

   [![Översikt över distribuerad spårning – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Välj länken mellan **sol systemet – väder** och **planet – väder-Provider** för att se mer information som LÅNGSAMMASTE anrop via http-metoder.

   [![Distribuerad spårning – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Slutligen väljer du **Undersök prestanda** för att utforska mer kraftfulla inbyggda prestanda analyser.

   [![Prestanda för distribuerad spårning – Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Med den inbyggda övervaknings funktionen i Azure våren-molnet kan du felsöka och övervaka komplexa problem. Azure våren Cloud integrerar [våren Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) med azures [Application Insights](../azure-monitor/app/app-insights-overview.md). Den här integrationen tillhandahåller kraftfulla loggar, mått och distribuerad spårnings funktion från Azure Portal. Följande procedurer beskriver hur du använder logg strömning, Log Analytics, mått och distribuerad spårning med distribuerade PiggyMetrics-appar.

## <a name="prerequisites"></a>Förutsättningar

Slutför föregående steg: 

* [Etablera en instans av Azure våren Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Konfigurera konfigurations servern](spring-cloud-quickstart-setup-config-server.md)
* [Bygg och distribuera appar](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Loggar

Det finns två sätt att se loggar i Azure våren Cloud: **logg strömning** av real tids loggar per app-instans eller **Log Analytics** för sammanställda loggar med avancerad fråge funktion.

### <a name="log-streaming"></a>Logg strömning

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Du kan använda logg strömning i Azure CLI med följande kommando.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Loggarna ser ut så här:

[![Logga strömning från Azure CLI ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Använd `az spring-cloud app logs -h` för att utforska fler parametrar och logg strömmars funktioner.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Hämta loggarna med Azure Toolkit for IntelliJ:

1. Välj **Azure Explorer** och sedan **våren Cloud**.

1. Högerklicka på appen som körs.

1. Välj **strömmande loggar** från den nedrullningsbara listan.

   ![Välj strömmande loggar](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Välj **instans**.

   ![Välj instans](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Direkt uppspelnings loggen visas i fönstret utdata.

   ![Strömmande logg utdata](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Gå till **tjänsten | Sidan översikt** och välj **loggar** i avsnittet **övervakning** . Klicka på **Kör** på en av exempel frågorna för Azure våren Cloud. 

   [![Loggar Analytics-post ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Sedan visas filtrerade loggar. I [Azure Log Analytics-dokument](../azure-monitor/logs/get-started-queries.md) finns mer information om hur du skriver frågor.

   [![Loggar Analytics-fråga ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Mått

1. Gå till **tjänsten | Sidan översikt** och välj **mått** i avsnittet **övervakning** . Lägg till ditt första mått genom att välja `system.cpu.usage` för **mått** och `Avg` för **agg regering** för att se tids linjen för total CPU-användning.

   [![Mått post ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Klicka på **Lägg till filter** i verktygsfältet ovan, Välj `App=Gateway` för att Visa CPU-användning för **Gateway** -appen.

   [![Använd filter i mått ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Ignorera filtret som har skapats ovan, klicka på **tillämpa delning** och välj `App` för **värden** för att se CPU-användning av olika appar.

   [![Använd delning i mått ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Distribuerad spårning

1. Gå till **tjänsten | Översikts** sida och välj **distribuerad spårning** i avsnittet **övervakning** . Klicka sedan på fliken **Visa program karta** till höger.

   [![Distribuerad spårnings post ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Nu kan du se status för anrop mellan Piggymetrics-appar. 

   [![Översikt över ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) distribuerad spårning](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Klicka på länken mellan **Gateway** och **konto tjänst** för att se mer information som LÅNGSAMMASTE anrop via http-metoder.

   [![Distribuerad spårning ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Klicka slutligen på **Undersök prestanda** för att utforska mer kraftfulla inbyggda prestanda analyser.

   [![Prestanda ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) för distribuerad spårning](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

I de här snabb starterna skapade du Azure-resurser som kommer fortsätta att debiteras om de finns kvar i din prenumeration. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen med hjälp av portalen eller genom att köra följande kommando i Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

I en tidigare snabb start anger du också standard resurs gruppens namn. Om du inte tänker fortsätta med nästa snabb start tar du bort standardvärdet genom att köra följande CLI-kommando:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Nästa steg

Information om hur du utforskar fler övervaknings funktioner i Azure våren Cloud finns i:

> [!div class="nextstepaction"]
> [Diagnostiska tjänster](diagnostic-services.md)
>
> [Distribuerad spårning](spring-cloud-howto-distributed-tracing.md)
>
> [Strömma loggar i realtid](spring-cloud-howto-log-streaming.md)
