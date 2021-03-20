---
title: Felsöka Application Insights i ett Java-webbprojekt
description: Fel söknings guide – övervakning av direktsända Java-appar med Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: df1f58418452ecafacddd013ab815ebca5cb8a35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097559"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java-sdk"></a>Fel sökning och frågor och svar för Application Insights för Java SDK

> [!IMPORTANT]
> Den rekommenderade metoden för att övervaka Java-program är att använda den automatiska Instrumentation utan att ändra koden. Följ rikt linjerna för [Application Insights Java 3,0-agenten](./java-in-process-agent.md).

Är du av frågor eller problem med [Azure Application Insights i Java][java]? Här följer några tips.

## <a name="build-errors"></a>Build-fel
**I Sol förmörkelse eller IntelliJ-idén, när jag lägger till Application Insights SDK via maven eller Gradle, får jag skapa eller kontroll Summa verifierings fel.**

* Om beroende `<version>` elementet använder ett mönster med jokertecken (t. ex. (maven) `<version>[2.0,)</version>` eller (Gradle) `version:'2.+'` ), kan du prova att ange en särskild version i stället `2.6.2` .

## <a name="no-data"></a>Inga data
**Jag har lagt till Application Insights lyckades och körde appen, men jag har aldrig sett data i portalen.**

* Vänta en minut och klicka på Uppdatera. Diagrammen uppdateras regelbundet, men du kan också uppdatera manuellt. Uppdaterings intervallet beror på diagrammets tidsintervall.
* Kontrol lera att du har en Instrumentation-nyckel som har definierats i ApplicationInsights.xml-filen (i mappen resurser i projektet) eller konfigurerat som miljö variabel.
* Kontrol lera att det inte finns någon `<DisableTelemetry>true</DisableTelemetry>` nod i XML-filen.
* I brand väggen kan du behöva öppna TCP-portarna 80 och 443 för utgående trafik till dc.services.visualstudio.com. Se den [fullständiga listan över brand Väggs undantag](./ip-addresses.md)
* I Microsoft Azure start-tavlan tittar du på tjänst status kartan. Om det finns några aviserings indikationer väntar du tills de har kommit tillbaka till OK och stänger sedan och öppnar bladet Application Insights program igen.
* [Aktivera loggning](#debug-data-from-the-sdk) genom att lägga till ett `<SDKLogger />` element under rotnoden i ApplicationInsights.xml-filen (i mappen resurser i projektet) och Sök efter poster som föregås av AI: info/warn/Error för eventuella misstänkta loggar. 
* Kontrol lera att rätt ApplicationInsights.xml fil har lästs in av Java SDK genom att titta på konsolens utgående meddelanden för en "konfigurations fil" har hittats "-instruktionen.
* Om det inte går att hitta konfigurations filen kontrollerar du meddelandena för att se var konfigurations filen genomsöks och kontrollerar att ApplicationInsights.xml finns på någon av dessa Sök platser. Som en tumregel kan du placera konfigurations filen nära Application Insights SDK-jar v7. Exempel: i Tomcat kan detta innebära mappen WEB-INF/classes. Under utvecklingen kan du placera ApplicationInsights.xml i mappen resurser i ditt webb projekt.
* Titta även på [sidan med GitHub-problem](https://github.com/microsoft/ApplicationInsights-Java/issues) för kända problem med SDK: n.
* Se till att använda samma version av Application Insights Core, webb, agent och logg tillägg för att undvika eventuella problem med versions konflikter.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag har använt för att se data, men har stoppats
* Har du nått din månads kvot för data punkter? Öppna inställningar/kvot och priser för att ta reda på det. I så fall kan du uppgradera planen eller betala för ytterligare kapacitet. Se [pris Planen](https://azure.microsoft.com/pricing/details/application-insights/).
* Har du nyligen uppgraderat din SDK? Kontrol lera att det bara finns unika SDK-jar v7 i projekt katalogen. Det finns inte två olika versioner av SDK.
* Tittar du på rätt AI-resurs? Matcha iKey för ditt program till den resurs där du förväntar dig telemetri. De bör vara identiska.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Jag ser inte alla data jag förväntar mig
* Öppna sidan användning och uppskattad kostnad och kontrol lera om [samplingen](./sampling.md) är i drift. (100% överföring innebär att samplingen inte är i drift.) Application Insightss tjänsten kan ställas in så att den endast accepterar en bråkdel av Telemetrin som kommer från din app. Detta hjälper dig att hålla dig inom din månatliga kvot för telemetri.
* Har SDK-sampling Aktiver ATS? Om ja, kommer data att samplas enligt den angivna frekvensen för alla tillämpliga typer.
* Kör du en äldre version av Java SDK? Från och med version 2.0.1 har vi infört fel tolerans mekanism för att hantera tillfälliga nätverks-och Server dels fel samt data beständighet på lokala enheter.
* Får du en begränsning på grund av överdriven telemetri? Om du aktiverar informations loggning visas ett meddelande om att "appen är begränsad". Vår aktuella gräns är 32k för telemetri-objekt per sekund.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java-agenten kan inte avbilda beroende data
* Har du konfigurerat Java agent genom att följa [Konfigurera Java-agenten](java-agent.md) ?
* Se till att både The Java agent jar och AI-Agent.xml-filen placeras i samma mapp.
* Se till att det beroende som du försöker att automatiskt samla in stöds för automatisk insamling. För närvarande stöder vi bara MySQL, MsSQL, Oracle DB och Azure cache för Redis-beroende samling.

## <a name="no-usage-data"></a>Inga användnings data
**Jag ser information om begär Anden och svars tider, men ingen sid visning, webbläsare eller användar data.**

Du har konfigurerat appen så att du kan skicka telemetri från servern. Nu är nästa steg att [Konfigurera dina webb sidor för att skicka telemetri från webbläsaren][usage].

Alternativt, om din klient är en app på en [telefon eller en annan enhet][platforms], kan du skicka telemetri därifrån.

Använd samma Instrumentation-nyckel för att konfigurera både din klient-och Server telemetri. Data visas i samma Application Insights resurs och du kan korrelera händelser från klient och server.

## <a name="disabling-telemetry"></a>Inaktivera telemetri
**Hur kan jag Inaktivera telemetri-samlingen?**

I kod:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Eller**

Uppdatera ApplicationInsights.xml (i mappen resurser i projektet). Lägg till följande under rotnoden:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Med XML-metoden måste du starta om programmet när du ändrar värdet.

## <a name="changing-the-target"></a>Ändra målet
**Hur kan jag ändra vilken Azure-resurs mitt projekt skickar data till?**

* [Hämta Instrumentation-nyckeln för den nya resursen.][java]
* Om du har lagt till Application Insights i projektet med Azure Toolkit for Eclipse högerklickar du på ditt webb projekt, väljer **Azure**, **konfigurerar Application Insights** och ändrar nyckeln.
* Om du har konfigurerat Instrumentation-nyckeln som miljö variabel uppdaterar du värdet för miljövariabeln med New iKey.
* Annars uppdaterar du nyckeln i ApplicationInsights.xml i mappen resurser i projektet.

## <a name="debug-data-from-the-sdk"></a>Felsöka data från SDK

**Hur kan jag ta reda på vad SDK gör?**

Om du vill ha mer information om vad som händer i API: et lägger du till `<SDKLogger/>` under rotnoden i ApplicationInsights.xml konfigurations filen.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Du kan också instruera loggen att skicka utdata till en fil:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Start starter för våren

Lägg till följande i filen om du vill aktivera SDK-loggning med våren Boot Apps med Application Insights våren Boot starter `application.properties` :

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

eller Skriv ut till standard fel:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java-agenten

Så här aktiverar du JVM-agent loggning uppdatera [AI-Agent.xml-filen](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Egenskaper för Java-kommando rads
_Sedan version 2.4.0_

Aktivera loggning med kommando rads alternativ, utan att ändra konfigurationsfiler:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

eller Skriv ut till standard fel:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Start skärmen för Azure
**Jag tittar på [Azure Portal](https://portal.azure.com). Vet kartan mig något om min app?**

Nej, den visar hälsan för Azure-servrar runtom i världen.

*Hur hittar jag data om min app från Azure start-tavlan (Start skärmen)?*

Förutsatt att du [konfigurerar appen för Application Insights][java]klickar du på Bläddra, väljer Application Insights och väljer den app-resurs som du har skapat för din app. För att det ska gå snabbare i framtiden kan du fästa appen på Start kortet.

## <a name="intranet-servers"></a>Intranät servrar
**Kan jag övervaka en server på intranätet?**

Ja, förutsatt att servern kan skicka telemetri till Application Insights-portalen via det offentliga Internet.

Du kan behöva [öppna vissa utgående portar i serverns brand vägg](./ip-addresses.md#outgoing-ports) för att tillåta SDK att skicka data till portalen.

## <a name="data-retention"></a>Datakvarhållning
**Hur länge sparas data i portalen? Är det säkert?**

Se [data kvarhållning och sekretess][data].

## <a name="debug-logging"></a>Fel söknings loggning
Application Insights använder `org.apache.http` . Detta finns i Application Insights Core-jar v7 under namn området `com.microsoft.applicationinsights.core.dependencies.http` . Detta gör att Application Insights kan hantera scenarier där olika versioner av samma `org.apache.http` finns i en kodbas.

>[!NOTE]
>Om du aktiverar loggning av fel SÖKNINGs nivå för alla namn områden i appen, kommer den att följas av alla moduler som körs, inklusive `org.apache.http` omdöpt till `com.microsoft.applicationinsights.core.dependencies.http` . Application Insights kan inte använda filtrering för dessa anrop eftersom logg anropet görs av Apache-biblioteket. Loggning av fel SÖKNINGs nivå ger en stor mängd loggdata och rekommenderas inte för direktsända produktions instanser.

## <a name="next-steps"></a>Nästa steg
**Jag har konfigurerat Application Insights för My Java Server-appen. Vad mer kan jag göra?**

* [Övervaka tillgängligheten för dina webb sidor][availability]
* [Övervaka webb sidans användning][usage]
* [Spåra användning och diagnostisera problem i dina enhets program][platforms]
* [Skriv kod för att spåra användningen av din app][track]
* [Samla in diagnostikloggar][javalogs]

## <a name="get-help"></a>Få hjälp
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Fil ett problem på GitHub](https://github.com/microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[data]: ./data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ./platforms.md
[track]: ./api-custom-events-metrics.md
[usage]: javascript.md

