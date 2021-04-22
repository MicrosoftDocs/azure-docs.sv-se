---
title: Analysera loggar och mått i Azure Spring Cloud | Microsoft Docs
description: Lär dig hur du analyserar diagnostikdata i Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6472ba7dd055de97a1855211f21fd0c75eea814f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874125"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analysera loggar och mått med diagnostikinställningar

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Med diagnostikfunktionen i Azure Spring Cloud kan du analysera loggar och mått med någon av följande tjänster:

* Använd Azure Log Analytics, där data skrivs till Azure Storage. Det finns en fördröjning när du exporterar loggar till Log Analytics.
* Spara loggar till ett lagringskonto för granskning eller manuell granskning. Du kan ange kvarhållningstiden (i dagar).
* Strömma loggar till din händelsehubb för inmatning av en tjänst från tredje part eller en anpassad analyslösning.

Välj den loggkategori och den måttkategori som du vill övervaka.

> [!TIP]
> Vill du bara strömma loggarna? Kolla in det här [Azure CLI-kommandot!](/cli/azure/spring-cloud/app#az_spring_cloud_app_logs)

## <a name="logs"></a>Loggar

|Loggas | Description |
|----|----|
| **ApplicationConsole** | Konsollogg för alla kundprogram. |
| **SystemLogs** | För närvarande används [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) i den här kategorin. |

## <a name="metrics"></a>Mått

En fullständig lista över mått finns i [Spring Cloud Mått](./spring-cloud-concept-metrics.md#user-metrics-options).

Kom igång genom att aktivera en av dessa tjänster för att ta emot data. Mer information om hur du konfigurerar Log Analytics [finns i Kom igång med Log Analytics i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Konfigurera diagnostikinställningar

1. I Azure Portal du till din Azure Spring Cloud instans.
1. Välj **alternativet Diagnostikinställningar** och välj sedan Lägg **till diagnostikinställning.**
1. Ange ett namn för inställningen och välj sedan var du vill skicka loggarna. Du kan välja valfri kombination av följande tre alternativ:
    * **Arkivera till ett lagringskonto**
    * **Strömma till en händelsehubb**
    * **Skicka till Log Analytics**

1. Välj vilken loggkategori och måttkategori du vill övervaka och ange sedan kvarhållningstiden (i dagar). Kvarhållningstiden gäller endast för lagringskontot.
1. Välj **Spara**.

> [!NOTE]
> 1. Det kan finnas en lucka på upp till 15 minuter mellan när loggar eller mått genereras och när de visas i ditt lagringskonto, din händelsehubb eller Log Analytics.
> 1. Om Azure Spring Cloud instansen tas bort eller flyttas överlappar åtgärden inte resurserna **för diagnostikinställningar.** Resurserna **för diagnostikinställningar** måste tas bort manuellt innan åtgärden utförs mot dess överordnade objekt, dvs. Azure Spring Cloud instansen. Om en ny Azure Spring Cloud-instans etableras med samma resurs-ID som den borttagna, eller om Azure Spring Cloud-instansen flyttas tillbaka, fortsätter de tidigare diagnostikinställningsresurserna att utöka den. 

## <a name="view-the-logs-and-metrics"></a>Visa loggar och mått
Det finns olika metoder för att visa loggar och mått enligt beskrivningen i följande rubriker.

### <a name="use-the-logs-blade"></a>Använda bladet Loggar

1. I Azure Portal du till din Azure Spring Cloud instans.
1. Öppna fönstret **Loggsökning** genom att välja **Loggar**.
1. I **sökrutan** Tabeller
   * Om du vill visa loggar anger du en enkel fråga, till exempel:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Om du vill visa mått anger du en enkel fråga, till exempel:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Om du vill visa sökresultatet väljer du **Kör**.

### <a name="use-log-analytics"></a>Använda Log Analytics

1. I Azure Portal väljer du Log Analytics i det **vänstra fönstret.**
1. Välj den Log Analytics-arbetsyta som du valde när du lade till diagnostikinställningarna.
1. Öppna fönstret **Loggsökning** genom att välja **Loggar**.
1. I **sökrutan** Tabeller
   * om du vill visa loggar anger du en enkel fråga, till exempel:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * om du vill visa mått anger du en enkel fråga, till exempel:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Om du vill visa sökresultatet väljer du **Kör**.
1. Du kan söka i loggarna för det specifika programmet eller instansen genom att ange ett filtervillkor:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` är fallkänsligt, men `=~` det är det inte.

Mer information om frågespråket som används i Log Analytics finns i Azure Monitor [loggfrågor](/azure/data-explorer/kusto/query/). Om du vill fråga efter alla Log Analytics-loggar från en centraliserad klient kan du [kolla Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

### <a name="use-your-storage-account"></a>Använda ditt lagringskonto

1. I Azure Portal du **Lagringskonton i** den vänstra navigeringspanelen eller i sökrutan.
1. Välj det lagringskonto som du valde när du lade till diagnostikinställningarna.
1. Öppna fönstret **Blobcontainer** genom att **välja Blobar**.
1. Om du vill granska programloggar söker du efter en container **med namnet insights-logs-applicationconsole**.
1. Om du vill granska programmått söker du efter en container **med namnet insights-metrics-pt1m**.

Mer information om hur du skickar diagnostikinformation till ett lagringskonto finns [i Lagra och visa diagnostikdata i Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Använda din händelsehubb

1. I rutan Azure Portal du **Event Hubs** i den vänstra navigeringspanelen eller sökrutan.

1. Sök efter och välj den händelsehubb som du valde när du lade till diagnostikinställningarna.
1. Öppna fönstret **Händelsehubblista** genom att välja **Event Hubs**.
1. Om du vill granska programloggar söker du efter en **händelsehubb med namnet insights-logs-applicationconsole**.
1. Om du vill granska programmått söker du efter en händelsehubb **med namnet insights-metrics-pt1m**.

Mer information om hur du skickar diagnostikinformation till en händelsehubb finns i Strömma Azure Diagnostics data i den heta sökvägen [med hjälp av Event Hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>Analysera loggarna

Azure Log Analytics körs med en Kusto-motor så att du kan köra frågor mot loggarna för analys. En snabb introduktion till att köra frågor mot loggar med hjälp av Kusto finns i [Log Analytics-självstudien](../azure-monitor/logs/log-analytics-tutorial.md).

Programloggar ger viktig information och utförliga loggar om programmets hälsotillstånd, prestanda med mera. I nästa avsnitt finns några enkla frågor som hjälper dig att förstå programmets aktuella och tidigare tillstånd.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Visa programloggar från Azure Spring Cloud

Om du vill granska en lista över programloggar från Azure Spring Cloud, sorterade efter tid med de senaste loggarna som visas först, kör du följande fråga:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Visa loggposter som innehåller fel eller undantag

Om du vill granska osorterade loggposter som nämner ett fel eller undantag kör du följande fråga:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Använd den här frågan för att hitta fel eller ändra frågevillkoren för att hitta specifika felkoder eller undantag.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Visa antalet fel och undantag som rapporterats av programmet under den senaste timmen

Om du vill skapa ett cirkeldiagram som visar antalet fel och undantag som loggats av programmet under den senaste timmen kör du följande fråga:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Läs mer om att köra frågor mot programloggar

Azure Monitor omfattande stöd för att köra frågor mot programloggar med hjälp av Log Analytics. Mer information om den här tjänsten finns [i Kom igång med loggfrågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md). Mer information om hur du skapar frågor för att analysera dina programloggar finns [i Översikt över loggfrågor i Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Hur konverterar jag Java-stackspårningar med flera linjer till en enda rad?

Det finns en lösning för att konvertera dina spårningar av flerradsstackar till en enda rad. Du kan ändra Java-loggutdata för att formatera om stackspårningsmeddelanden och ersätta tecken på ny rad med en token. Om du använder Java Logback-biblioteket kan du formatera om stackspårningsmeddelanden genom att lägga `%replace(%ex){'[\r\n]+', '\\n'}%nopex` till följande:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
Sedan kan du ersätta token med tecken på nyrad igen i Log Analytics enligt nedan:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Du kanske kan använda samma strategi för andra Java-loggbibliotek.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Distribuera ditt Azure Spring Cloud program](spring-cloud-quickstart.md)
