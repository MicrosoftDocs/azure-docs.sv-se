---
title: Prestanda övervakning av Java-webbappar – Azure Application insikter
description: Utökad prestanda och användnings övervakning av din Java-webbplats med Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: c753e4e254890f9198da9bc913b29bdaae335b78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573842"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Övervaka beroenden, fångade undantag och metod körnings tider i Java-webbappar

> [!IMPORTANT]
> Den metod som beskrivs i det här dokumentet rekommenderas inte längre.
>
> Den rekommenderade metoden för att övervaka Java-program är att använda den automatiska Instrumentation utan att ändra koden. Följ rikt linjerna för [Application Insights Java 3,0-agenten](./java-in-process-agent.md).

Om du har [instrumenterat Java-webbappen med Application Insights SDK][java]kan du använda Java-agenten för att få djupare insikter utan några kod ändringar:

* **Beroenden:** Data om samtal som ditt program gör till andra komponenter, inklusive:
  * **Utgående HTTP-anrop** som görs via Apache httpclient, OkHttp och `java.net.HttpURLConnection` samlas in.
  * **Redis-anrop** som görs via Jedis-klienten fångas.
  * **JDBC-frågor** – för MySQL och PostgreSQL, om samtalet tar längre än 10 sekunder, rapporterar agenten frågeplan.

* **Program loggning:** Avbilda och korrelera dina program loggar med HTTP-förfrågningar och annan telemetri
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Bättre namngivning av åtgärder:** (används för agg regering av förfrågningar i portalen)
  * **Våren** – baserat på `@RequestMapping` .
  * **JAX – RS** -baserad på `@Path` . 

Om du vill använda Java-agenten installerar du den på servern. Dina webb program måste vara instrumenterade med [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installera Application Insights agent för Java
1. [Hämta 2. x-agenten](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/2.6.2)på den dator som kör Java-servern. Kontrol lera att den version av Java-agenten 2. x som du använder matchar den version av 2. x Application Insights Java SDK som du använder.
2. Redigera start skriptet för program servern och Lägg till följande JVM-argument:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Till exempel i Tomcat på en Linux-dator:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Starta om program servern.

## <a name="configure-the-agent"></a>Konfigurera agenten
Skapa en fil med namnet `AI-Agent.xml` och placera den i samma mapp som agentens jar-fil.

Ange innehållet i XML-filen. Redigera följande exempel för att inkludera eller utelämna de funktioner som du vill använda.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Ytterligare konfiguration (våren boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

För Azure App tjänster gör du följande:

* Välj Inställningar > Programinställningar
* Under Appinställningar lägger du till ett nytt nyckel/värde-par:

Nyckel: `JAVA_OPTS` värde: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.6.2.jar`

Agenten måste paketeras som en resurs i ditt projekt så att den blir i D:/Home/site/wwwroot/Directory. Du kan bekräfta att agenten har rätt app service katalog genom att gå till **utvecklingsverktyg**  >  **Avancerade verktyg**  >  **Felsök konsol** och undersöka innehållet i plats katalogen.    

* Spara inställningarna och starta om din app. (Dessa steg gäller endast för App Services som körs i Windows.)

> [!NOTE]
> AI-Agent.xml och agentens jar-fil ska finnas i samma mapp. De placeras ofta tillsammans i `/resources` mappen i projektet.  

#### <a name="enable-w3c-distributed-tracing"></a>Aktivera distribuerad W3C-spårning

Lägg till följande i AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Läget för bakåtkompatibilitet är aktiverat som standard och enableW3CBackCompat-parametern är valfri och ska endast användas när du vill inaktivera den. 

Vi rekommenderar att detta är fallet när alla dina tjänster har uppdaterats till en nyare version av SDK: er som stöder W3C-protokollet. Vi rekommenderar starkt att du flyttar till en nyare version av SDK: er med stöd för W3C så snart som möjligt.

Se till att **både [inkommande](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) och utgående (agent) konfigurationer** är exakt samma.

## <a name="view-the-data"></a>Visa data
I Application Insights-resursen visas sammanställda fjärrberoende och metod körnings tider [under prestanda panelen][metrics].

Om du vill söka efter enskilda instanser av beroende, undantag och metod rapporter öppnar du [Sök][diagnostic].

[Diagnostisera beroende problem – Läs mer](./asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Har du några frågor? Har du problem?
* Ser du inga data? [Ange brand Väggs undantag](./ip-addresses.md)
* [Felsöka Java](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

