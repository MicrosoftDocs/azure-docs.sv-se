---
title: Filtrera Azure Application Insights-telemetri i din Java-webbapp
description: Minska telemetri trafiken genom att filtrera bort händelserna som du inte behöver övervaka.
ms.topic: conceptual
ms.date: 3/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 35a95ac32fc4390e08d3c7fee2b9f9ff52202e4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100573823"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrera telemetri i din Java-webbapp

> [!IMPORTANT]
> Den metod som beskrivs i det här dokumentet rekommenderas inte längre.
>
> Den rekommenderade metoden för att övervaka Java-program är att använda den automatiska Instrumentation utan att ändra koden. Följ rikt linjerna för [Application Insights Java 3,0-agenten](./java-in-process-agent.md).

Filter är ett sätt att välja telemetri som din [Java-webbapp skickar till Application Insights](java-get-started.md). Det finns några färdiga filter som du kan använda och du kan också skriva egna anpassade filter.

De färdiga filtren är:

* Allvarlighets grad för spårning
* Vissa URL: er, nyckelord eller svars koder
* Snabba svar – det vill säga förfrågningar som appen svarade på snabbt
* Angivna händelse namn

> [!NOTE]
> Filter skevar måtten för din app. Du kan till exempel bestämma att du ska ta bort snabba svars tider genom att ange ett filter för att diagnostisera långsamma svar. Men du måste vara medveten om att den genomsnittliga svars tiden som rapporteras av Application Insights kommer att bli långsammare än den sanna hastigheten och antalet begär Anden blir mindre än det faktiska antalet.
> Om detta är ett problem kan du använda [sampling](./sampling.md) i stället.

## <a name="setting-filters"></a>Ange filter

I ApplicationInsights.xml lägger du till ett `TelemetryProcessors` avsnitt som det här exemplet:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```

[Kontrol lera den fullständiga uppsättningen inbyggda processorer](https://github.com/microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal).

## <a name="built-in-filters"></a>Inbyggda filter

### <a name="metric-telemetry-filter"></a>Filter för mått för telemetri

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` – Kommaavgränsad lista över anpassade mått namn.


### <a name="page-view-telemetry-filter"></a>Telemetri filter för sid visning

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` – Varaktigheten avser den tid det tar att läsa in sidan. Om detta ställs in rapporteras inte sidor som laddades snabbare än den tiden.
* `NotNeededNames` – Kommaavgränsad lista med sid namn.
* `NotNeededUrls` – Kommaavgränsad lista med URL-fragment. `"home"`Filtrerar till exempel alla sidor som har "hem" i URL: en.


### <a name="request-telemetry-filter"></a>Filtret begär telemetri


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filter för syntetisk källa

Filtrerar all telemetri som har värden i egenskapen SyntheticSource. Dessa omfattar förfrågningar från robotar, spindeler och tillgänglighets test.

Filtrera bort telemetri för alla syntetiska begär Anden:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtrera bort telemetri för vissa syntetiska källor:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` – Kommaavgränsad lista över namn på syntetiska källor.

### <a name="telemetry-event-filter"></a>Händelse filter för telemetri

Filtrerar anpassade händelser (loggas med [TrackEvent ()](./api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` – Kommaavgränsad lista över händelse namn.


### <a name="trace-telemetry-filter"></a>Filter för trace-telemetri

Filtrerar logg spårningar (loggas med [TrackTrace ()](./api-custom-events-metrics.md#tracktrace) eller en [insamlare för loggnings ramverk](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` giltiga värden är:
  *  Inaktivera-filtrera alla spårningar
  *  SPÅRNING – ingen filtrering. lika med spårnings nivå
  *  INFO – Filtera SPÅRNINGs nivå
  *  Varna – filtrera bort spårning och information
  *  FEL-filtrera ut varning, INFO, spåra
  *  KRITISKt-filtrera alla utom kritiska


## <a name="custom-filters"></a>Anpassade filter

### <a name="1-code-your-filter"></a>1. koda ditt filter

I din kod skapar du en klass som implementerar `TelemetryProcessor` :

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

        /* Any parameters that are required to support the filter.*/
        private final String successful;

        /* Initializers for the parameters, named "setParameterName" */
        public void setNotNeeded(String successful)
        {
            this.successful = successful;
        }

        /* This method is called for each item of telemetry to be sent.
           Return false to discard it.
           Return true to allow other processors to inspect it. */
        @Override
        public boolean process(Telemetry telemetry) {
            if (telemetry == null) { return true; }
            if (telemetry instanceof RequestTelemetry)
            {
                RequestTelemetry requestTelemetry = (RequestTelemetry)    telemetry;
                return request.getSuccess() == successful;
            }
            return true;
        }
    }

```

### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. anropa ditt filter i konfigurations filen

I ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. anropa ditt filter (Java våren)

För program som baseras på våren-ramverket måste anpassade telemetri-processorer registreras i huvud program klassen som en bönor. De blir sedan automatiskt när programmet startas.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Du måste skapa dina egna filter parametrar i och använda `application.properties` våren boot ' s external Configuration Framework för att skicka dessa parametrar till det anpassade filtret. 


## <a name="troubleshooting"></a>Felsökning

*Mitt filter fungerar inte.*

* Kontrol lera att du har angett giltiga parameter värden. Varaktigheter ska till exempel vara heltal. Ogiltiga värden kommer att orsaka att filtret ignoreras. Om det anpassade filtret genererar ett undantag från en konstruktor eller set-metod ignoreras det.

## <a name="next-steps"></a>Nästa steg

* [Sampling](./sampling.md) – Överväg att använda sampling som ett alternativ som inte snedställer dina mått.

