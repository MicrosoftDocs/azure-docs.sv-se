---
title: 'Snabb start: Java Web App Analytics med Azure Application Insights'
description: 'Övervakning av programprestanda för Java-webbappar med Application Insights. '
ms.topic: conceptual
ms.date: 11/22/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: cdad5a0ec158f216a63ae7e1e2abc10d2841b3bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593723"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Snabbstart: Kom igång med Application Insights i ett Java-webbprojekt


> [!CAUTION]
> Från och med november 2020, för övervakning av Java-program, rekommenderar vi automatisk Instrumentation med hjälp av Azure Monitor Application Insights Java 3,0-agenten. Mer information om hur du kommer igång finns [Application Insights Java 3,0-agent](./java-in-process-agent.md).

I den här snabb starten använder du Application Insights SDK för att hantera begäran, spåra beroenden och samla in prestanda räknare, diagnostisera prestanda problem och undantag och skriv kod för att spåra vad användarna gör med din app.

Application Insights är en utökningsbar analystjänst för webbutvecklare som hjälper dig att förstå prestanda och användningen av ditt liveprogram. Application Insights har stöd för Java-appar som körs på Linux, Unix eller Windows.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Ett fungerande Java-program.

## <a name="get-an-application-insights-instrumentation-key"></a>Hämta en Application Insights-instrumenteringsnyckel

> [!IMPORTANT]
> Nya Azure-regioner **kräver** att anslutnings strängar används i stället för instrument knappar. [Anslutnings strängen](./sdk-connection-string.md?tabs=java) identifierar den resurs som du vill associera dina telemetridata med. Du kan också ändra de slut punkter som resursen kommer att använda som mål för din telemetri. Du måste kopiera anslutnings strängen och lägga till den i programmets kod eller till en miljö variabel.
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Skapa en Application Insights-resurs på Azure Portal. Ange programtypen till Java-webbapp.

3. Leta upp instrumenteringsnyckeln för den nya resursen. Du kommer att behöva klistra in den här nyckeln i projektkoden inom kort.

    ![I den nya resursöversikten klickar du på Egenskaper och kopierar instrumenteringsnyckeln.](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Lägga till Application Insights SDK för Java till ditt projekt

*Välj en projekt typ.*

# <a name="maven"></a>[Maven](#tab/maven)

Om ditt projekt redan har kon figurer ATS för att använda Maven för build, sammanfogar du följande kod till din *pom.xml* -fil.

Uppdatera sedan projektberoendena för att få binärfilerna.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.6.2</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Om ditt projekt redan har kon figurer ATS för att använda Gradle för build, sammanfogar du följande kod till din *build. Gradle* -fil.

Uppdatera sedan projektberoendena för att få binärfilerna.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.6.2'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

---

### <a name="questions"></a>Frågor
* *Vad är förhållandet mellan komponenterna och `-web-auto` `-web` `-core` ?*
  * `applicationinsights-web-auto` ger dig mått som spårar antalet begär Anden och svars tider för HTTP-servlet genom att automatiskt registrera Application Insights servlet-filtret vid körning.
  * `applicationinsights-web` ger dig också mått som spårar antalet begär Anden och svars tider för HTTP-servlet, men kräver manuell registrering av Application Insights servlet-filtret i ditt program.
  * `applicationinsights-core` ger dig bara det Bare-API: t, till exempel om ditt program inte är servlet-baserat.
  
* *Hur uppdaterar jag SDK till den senaste versionen?*
  * Från och med november 2020, för övervakning av Java-program, rekommenderar vi automatisk Instrumentation med hjälp av Azure Monitor Application Insights Java 3,0-agenten. Mer information om hur du kommer igång finns [Application Insights Java 3,0-agent](./java-in-process-agent.md).

## <a name="add-an-applicationinsightsxml-file"></a>Lägg till en *ApplicationInsights.xml* -fil
Lägg till *ApplicationInsights.xml* i mappen resurser i projektet eller kontrol lera att den har lagts till i projektets distributions klass Sök väg. Kopiera följande XML-kod till den.

Ersätt Instrumentation-tangenten med den som du fick från Azure Portal.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Konfigurations filen kan eventuellt vara valfri plats som är tillgänglig för ditt program.  Egenskapen system `-Dapplicationinsights.configurationDirectory` anger den katalog som innehåller *ApplicationInsights.xml*. Exempel: En konfigurationsfil som finns på `E:\myconfigs\appinsights\ApplicationInsights.xml` konfigureras med egenskapen `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Instrumenteringsnyckeln skickas tillsammans med alla element i telemetrin och uppmanar Application Insights att visa den i din resurs.
* Komponenten HTTP-begäran är valfri. Den skickar automatiskt telemetri om förfrågningar och svarstider till portalen.
* Händelsekorrelation är ett tillägg till komponenten HTTP-begäran. Den tilldelar en identifierare till varje begäran som tas emot av servern. Den lägger sedan till den här identifieraren som en egenskap för alla objekt i telemetri som egenskapen "Operation.Id". Detta gör att du kan korrelera telemetrin som är associerad med varje begäran genom att ange ett filter i [Diagnostiksökning][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Olika sätt att konfigurera instrumenteringsnyckeln på
Application Insights SDK:n söker efter nyckeln i följande ordning:

1. System egenskap:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Miljö variabel: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurations fil: *ApplicationInsights.xml*

Du kan också [ange den i koden](./api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Lägg till agent

[Installera Java-agenten](java-agent.md) för att avbilda utgående HTTP-anrop, JDBC-frågor, program loggning och bättre namngivning av åtgärder.

## <a name="run-your-application"></a>Köra ditt program
Kör programmet i felsökningsläge på utvecklingsdatorn eller publicera det till servern.

## <a name="view-your-telemetry-in-application-insights"></a>Visa telemetrin i Application Insights
Gå tillbaka till Application Insights-resursen på [Microsoft Azure Portal](https://portal.azure.com).

Data om HTTP-förfrågningar visas på översiktsbladet. (Om informationen inte visas väntar du några sekunder och klickar på Uppdatera.)

![Skärm bild av exempel data för översikt](./media/java-get-started/overview-graphs.png)

[Lär dig mer om mätvärden.][metrics]

Klicka dig vidare i diagrammen om du vill visa mer detaljerade aggregerade mätvärden.

![Fönstret Application Insightss problem med diagram](./media/java-get-started/006-barcharts.png)

### <a name="instance-data"></a>Instansdata
Klicka dig vidare inom en specifik begärandetyp om du vill visa enskilda instanser.

![Detaljgranska i en speciell exempel vy](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics: Kraftfullt frågespråk
Allt eftersom du ackumulerar mer data kan du köra frågor både för att aggregera data och för att hitta enskilda instanser.  [Analytics](../logs/log-query-overview.md) är ett kraftfullt verktyg både för att bättre förstå prestanda och användning, och för diagnostikändamål.

![Exempel med Analytics](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Installera din app på servern
Publicera appen på servern, låt användarna använda den och se hur telemetrin visas på portalen.

* Kontrollera att din brandvägg tillåter programmet att skicka telemetri till följande portar:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Om utgående trafik måste dirigeras via en brandvägg definierar du systemegenskaper `http.proxyHost` och `http.proxyPort`.

* Installera följande på Windows-servrar:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Den här komponenten gör det möjligt att använda prestandaräknare.)

## <a name="azure-app-service-aks-vms-config"></a>Azure App Service, AKS, VM-konfiguration

Det bästa och enklaste sättet att övervaka dina program som körs på någon av Azure-resurs leverantörer är att använda Application Insights Auto-Instrumentation via [Java 3,0-agenten](./java-in-process-agent.md).


## <a name="exceptions-and-request-failures"></a>Fel relaterade till begäranden och undantag
Ohanterade undantag och begär Anden som Miss lyckas samlas in automatiskt av Application Insights webb filter.

Om du vill samla in data om andra undantag kan du [Infoga anrop till trackException () i din kod][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Övervaka metodanrop och externa beroenden
[Installera Java-agenten](java-agent.md) om du vill logga angivna interna metoder och anrop som görs via JDBC, med tidsinställningsdata.

Och för automatisk namngivning av åtgärder.

## <a name="w3c-distributed-tracing"></a>Distribuerad W3C-spårning

Application Insights Java SDK stöder nu [distribuerad W3C-spårning](https://w3c.github.io/trace-context/).

Den inkommande SDK-konfigurationen förklaras ytterligare i vår artikel om [korrelation](correlation.md).

Den utgående SDK-konfigurationen definieras i [AI-Agent.xml](java-agent.md) -filen.

## <a name="performance-counters"></a>Prestandaräknare
Öppna **Undersök**, **statistik**, om du vill se en serie prestanda räknare.

![Skärm bild av mått fönstret med process privata byte valda](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Anpassa samlingen med prestandaräknare
Om du vill inaktivera insamling av standard uppsättningen med prestanda räknare lägger du till följande kod under rotnoden i *ApplicationInsights.xml* -filen:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Samla in data från fler prestandaräknare
Du kan ange ytterligare prestandaräknare som du vill samla in data från.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-räknare (exponeras av Java Virtual Machine)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – Namnet visas på Application Insights-portalen.
* `objectName` – JMX-objektnamnet.
* `attribute` – Attributet för JMX-objektnamnet som ska hämtas
* `type` (valfritt) – typen för JMX-objektets attribut:
  * Standardvärde: en enkel typ, till exempel int eller long.
  * `composite`: prestandaräknardata har formatet ”Attribute.Data”
  * `tabular`: prestandaräknardata har tabellradsformat

#### <a name="windows-performance-counters"></a>Windows-prestandaräknare
Varje [Windows-prestandaräknare](/windows/win32/perfctrs/performance-counters-portal) är medlem i en kategori (på samma sätt som ett fält är medlem i en klass). Kategorier kan antingen vara globala eller ha numrerade eller namngivna instanser.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Namnet som visas på Application Insights-portalen.
* categoryName – Kategorin av prestandaräknare (prestandaobjekt) som den här prestandaräknaren är associerad med.
* counterName – Namnet på prestandaräknaren.
* instanceName – Namnet på instansen av prestandaräknarkategorin eller en tom sträng ("") om kategorin innehåller en enda instans. Om categoryName är Process och den prestandaräknare som du vill samla in data från hör till den aktuella JVM-processen som din app körs i anger du `"__SELF__"`.

### <a name="unix-performance-counters"></a>Unix-prestandaräknare
* [Installera collectd med Application Insights-plugin-programmet](java-collectd.md) om du vill samla in en mängd olika system- och nätverksdata.

## <a name="get-user-and-session-data"></a>Samla in användar- och sesionsdata
Du skickar telemetri från webbservern. Men för att få en heltäckande bild av ditt program kan du lägga till ännu mer övervakning:

* [Lägg till telemetri till webbsidor][usage] om du vill övervaka sidvisningar och användarmått.
* [Konfigurera webbtester][availability] för att se till att ditt program är aktivt och effektivt.

## <a name="send-your-own-telemetry"></a>Skicka din egen telemetri
Nu när du har installerat SDK kan du använda API:et för att skicka din egen telemetri.

* [Spåra anpassade händelser och mått][api] och ta reda på vad användare gör med ditt program.
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem.

## <a name="availability-web-tests"></a>Webbtester för tillgänglighet
Application Insights kan testa din webbplats med jämna mellanrum för att kontrollera att tjänsten är tillgänglig och att den svarar.

[Läs mer om hur du konfigurerar webb test för tillgänglighet.][availability]

## <a name="questions-problems"></a>Har du några frågor? Har du problem?
[Felsöka Java](java-troubleshoot.md)

## <a name="next-steps"></a>Nästa steg
* [Övervaka beroende anrop](java-agent.md)
* [Övervaka Unix-prestandaräknare](java-collectd.md)
* Lägg till [övervakning till dina webbsidor](javascript.md) för att övervaka sidans belastning, AJAX-anrop, webbläsarundantag.
* Skriv [anpassad telemetri](./api-custom-events-metrics.md) att spåra användningen i webbläsaren eller på servern.
* Använda  [analyser](../logs/log-query-overview.md) för kraftfulla frågor via telemetri från din app
* Mer information finns i [Azure för Java-utvecklare](/java/azure).

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md
[usage]: javascript.md