---
title: Analysera loggar och mått i Azure våren-molnet | Microsoft Docs
description: Lär dig hur du analyserar diagnostikdata i Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 52413f36a9ba5c7b7a3c68567a65a20bc3ce4090
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585198"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analysera loggar och mått med diagnostikinställningar

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Med hjälp av diagnostikprogrammet i Azure våren Cloud kan du analysera loggar och mät värden med någon av följande tjänster:

* Använd Azure Log Analytics, där data skrivs till Azure Storage. Det uppstår en fördröjning vid export av loggar till Log Analytics.
* Spara loggar till ett lagrings konto för granskning eller manuell kontroll. Du kan ange Retentions tid (i dagar).
* Strömma loggar till händelsehubben för inmatning av en tjänst från tredje part eller en anpassad analys lösning.

Välj den logg kategori och den mått kategori som du vill övervaka.

> [!TIP]
> Vill du bara strömma dina loggar? Kolla ut detta [Azure CLI-kommando](/cli/azure/ext/spring-cloud/spring-cloud/app?preserve-view=true&view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)!

## <a name="logs"></a>Loggar

|Loggas | Description |
|----|----|
| **ApplicationConsole** | Konsol logg för alla kund program. |
| **SystemLogs** | För närvarande är endast [vår moln konfigurations Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) loggar i den här kategorin. |

## <a name="metrics"></a>Mått

En fullständig lista över mått finns i [moln mått för våren](./spring-cloud-concept-metrics.md#user-metrics-options).

Kom igång genom att aktivera någon av dessa tjänster för att ta emot data. Information om hur du konfigurerar Log Analytics finns [i kom igång med Log Analytics i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Konfigurera diagnostikinställningar

1. I Azure Portal går du till din Azure våren Cloud-instans.
1. Välj alternativet **diagnostikinställningar** och välj sedan **Lägg till diagnostikinställningar**.
1. Ange ett namn för inställningen och välj sedan var du vill skicka loggarna. Du kan välja en valfri kombination av följande tre alternativ:
    * **Arkivera till ett lagrings konto**
    * **Strömma till en händelsehubb**
    * **Skicka till Log Analytics**

1. Välj vilken logg kategori och vilken mått kategori du vill övervaka och ange sedan kvarhållningsperiod (i dagar). Retentions tiden gäller endast för lagrings kontot.
1. Välj **Spara**.

> [!NOTE]
> 1. Det kan finnas en lucka i upp till 15 minuter mellan när loggarna eller måtten genereras och när de visas i ditt lagrings konto, Event Hub eller Log Analytics.
> 1. Om Azure våren Cloud-instansen tas bort eller flyttas, överlappar inte åtgärden de resurser som används för **diagnostik** . De **diagnostiska inställnings** resurserna måste tas bort manuellt före åtgärden mot dess överordnade objekt, D.v.s. Azure våren Cloud-instansen. Om en ny Azure våren Cloud-instans är etablerad med samma resurs-ID som den borttagna, eller om Azure våren Cloud-instansen flyttas tillbaka, fortsätter de tidigare **inställningarna för diagnostikinställningar** att utökas.

## <a name="view-the-logs-and-metrics"></a>Visa loggar och mått
Det finns olika metoder för att visa loggar och mått enligt beskrivningen under följande rubriker.

### <a name="use-the-logs-blade"></a>Använd bladet loggar

1. I Azure Portal går du till din Azure våren Cloud-instans.
1. Öppna fönstret **loggs ökning** genom att välja **loggar**.
1. I sökrutan **tabeller**
   * Om du vill visa loggar anger du en enkel fråga som:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Om du vill visa mått anger du en enkel fråga som:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Om du vill visa Sök resultatet väljer du **Kör**.

### <a name="use-log-analytics"></a>Använda Log Analytics

1. I Azure Portal väljer du **Log Analytics** i det vänstra fönstret.
1. Välj den Log Analytics arbets yta som du valde när du lade till diagnostikinställningar.
1. Öppna fönstret **loggs ökning** genom att välja **loggar**.
1. I sökrutan **tabeller** ,
   * Om du vill visa loggar anger du en enkel fråga som:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Om du vill visa mått anger du en enkel fråga som:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Om du vill visa Sök resultatet väljer du **Kör**.
1. Du kan söka i loggarna för det specifika programmet eller instansen genom att ange ett filter villkor:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` är Skift läges känslig, men `=~` är inte det.

Om du vill veta mer om frågespråket som används i Log Analytics, se [Azure Monitor logg frågor](/azure/data-explorer/kusto/query/).

### <a name="use-your-storage-account"></a>Använd ditt lagrings konto

1. Sök efter **lagrings konton** i den vänstra navigerings panelen eller sökrutan i Azure Portal.
1. Välj det lagrings konto som du valde när du lade till diagnostikinställningar.
1. Öppna fönstret **BLOB container** genom att välja **blobbar**.
1. Om du vill granska program loggarna söker du efter en behållare med namnet **Insights-logs-applicationconsole**.
1. Om du vill granska program mått söker du efter en behållare med namnet **Insights-Metrics-pt1m**.

Mer information om hur du skickar diagnostikinformation till ett lagrings konto finns i [lagra och Visa diagnostikdata i Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Använda händelsehubben

1. I Azure Portal söker du efter **Event Hubs** i den vänstra navigerings panelen eller sökrutan.

1. Sök efter och välj den händelsehubben som du valde när du lade till diagnostikinställningar.
1. Öppna fönstret **Event Hub List** genom att välja **Event Hubs**.
1. Om du vill granska program loggar kan du söka efter en Event Hub med namnet **Insights-logs-applicationconsole**.
1. Om du vill granska program mått söker du efter ett händelsehubben som kallas **Insights-Metrics-pt1m**.

Mer information om hur du skickar diagnostikinformation till en Event Hub finns i [strömma Azure-diagnostik data i den aktiva sökvägen med Event Hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>Analysera loggarna

Azure Log Analytics körs med en Kusto-motor så att du kan skicka frågor till dina loggar för analys. En snabb introduktion till att köra frågor mot loggar med hjälp av Kusto finns i [själv studie kursen om Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Program loggar ger viktig information och utförliga loggar om programmets hälsa, prestanda och mycket annat. I nästa avsnitt finns några enkla frågor som hjälper dig att förstå programmets aktuella och tidigare tillstånd.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Visa program loggar från Azure våren Cloud

Om du vill granska en lista över program loggar från Azure våren Cloud, sorterat efter tid med de senaste loggarna som visas först, kör du följande fråga:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Visa loggar poster som innehåller fel eller undantag

Om du vill granska osorterade logg poster som nämner ett fel eller undantag kör du följande fråga:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Använd den här frågan för att hitta fel eller ändra sökorden för att hitta vissa felkoder eller undantag.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Visa antalet fel och undantag som rapporter ATS av ditt program under den senaste timmen

Kör följande fråga för att skapa ett cirkel diagram som visar antalet fel och undantag som loggats av programmet den senaste timmen:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Läs mer om att skicka frågor till program loggar

Azure Monitor ger omfattande stöd för att skicka frågor till program loggar med hjälp av Log Analytics. Mer information om den här tjänsten finns [i kom igång med logg frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md). Mer information om hur du skapar frågor för att analysera dina program loggar finns i [Översikt över logg frågor i Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Hur konverterar du flera rader i Java stack-spår till en enda rad?

Det finns en lösning för att konvertera dina stack spårningar i flera rader till en enda rad. Du kan ändra Java-loggens utdata för att formatera om stack spårnings meddelanden, ersätta rad matnings tecken med en token. Om du använder Java logback-biblioteket kan du formatera om stack spårnings meddelanden genom att lägga till följande `%replace(%ex){'[\r\n]+', '\\n'}%nopex` :

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
Du kan sedan ersätta token med rad matnings tecken igen i Log Analytics enligt nedan:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Du kanske kan använda samma strategi för andra Java logg bibliotek.

## <a name="next-steps"></a>Nästa steg

* [Snabb start: Distribuera ditt första Azure våren Cloud-program](spring-cloud-quickstart.md)