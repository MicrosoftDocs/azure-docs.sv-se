---
title: Använd distribuerad spårning med Azure våren Cloud
description: Lär dig att använda våren Cloud Distributed tracing via Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a78aec8c18f3b89629bbf696de3a097397ac59bc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337924"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Använd distribuerad spårning med Azure våren Cloud

Med de distribuerade spårnings verktygen i Azure våren-molnet kan du enkelt felsöka och övervaka komplexa problem. Azure våren Cloud integrerar [våren Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) med azures [Application Insights](../azure-monitor/app/app-insights-overview.md). Den här integrationen ger kraftfull, distribuerad spårnings funktion från Azure Portal.

::: zone pivot="programming-language-csharp"
I den här artikeln får du lära dig hur du aktiverar en .NET Core Steeltoe-app för att använda distribuerad spårning.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa dessa procedurer behöver du en Steeltoe-app som redan är [för beredd för distribution till Azure våren Cloud](spring-cloud-tutorial-prepare-app-deployment.md).

## <a name="dependencies"></a>Beroenden

För Steeltoe-2.4.4 lägger du till följande NuGet-paket:

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. Management. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

För Steeltoe-3.0.0 lägger du till följande NuGet-paket:

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)

## <a name="update-startupcs"></a>Uppdatera start. CS

1. För Steeltoe-2.4.4 anropar `AddDistributedTracing` `AddZipkinExporter` du och i- `ConfigureServices` metoden.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

   För Steeltoe-3.0.0, anropa `AddDistributedTracing` i- `ConfigureServices` metoden.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration, builder => builder.UseZipkinWithTraceOptions(services));
   }
   ```

1. För Steeltoe-2.4.4, anropa `UseTracingExporter` i- `Configure` metoden.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
        app.UseTracingExporter();
   }
   ```

   För Steeltoe-3.0.0 krävs inga ändringar i- `Configure` metoden.

## <a name="update-configuration"></a>Uppdatera konfiguration

Lägg till följande inställningar i konfigurations källan som ska användas när appen körs i Azure våren Cloud:

1. Ange `management.tracing.alwaysSample` till Sant.

2. Om du vill se spårnings förrymder som skickas mellan Eureka-servern, konfigurations servern och användarens appar: ange `management.tracing.egressIgnorePattern` till "/API/v2/spans |/v2/Apps/.*/Permissions |/Eureka/.*| /oauth/.*".

*appsettings.jspå* är till exempel följande egenskaper:
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

Mer information om distribuerad spårning i .NET Core Steeltoe-appar finns i [distribuerad spårning](https://steeltoe.io/docs/3/tracing/distributed-tracing) i Steeltoe-dokumentationen.
::: zone-end
::: zone pivot="programming-language-java"
I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Aktivera distribuerad spårning i Azure Portal.
> * Lägg till vår moln Sleuth i ditt program.
> * Visa beroende kartor för mikrotjänst program.
> * Sök spårnings data med olika filter.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa de här procedurerna behöver du en moln tjänst för Azure våren som redan är etablerad och körs. Slutför snabb starten för [ditt första Azure våren Cloud-program](spring-cloud-quickstart.md) för att etablera och köra en moln tjänst för Azure våren.

## <a name="add-dependencies"></a>Lägg till beroenden

1. Lägg till följande rad i filen Application. Properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Efter den här ändringen kan Zipkin-avsändaren skicka till webben.

1. Hoppa över det här steget om du följde vår [Guide för att förbereda ett Azure våren Cloud-program](spring-cloud-tutorial-prepare-app-deployment.md). Annars går du till din lokala utvecklings miljö och redigerar pom.xml-filen så att den inkluderar följande Sleuth-beroende för fjäder molnet:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Bygg och distribuera igen för din Azure våren Cloud-tjänst för att avspegla dessa ändringar.

## <a name="modify-the-sample-rate"></a>Ändra samplings frekvensen

Du kan ändra den hastighet som din telemetri samlas in genom att ändra samplings frekvensen. Om du t. ex. vill använda hälften så ofta öppnar du filen program. Properties och ändrar följande rad:

```xml
spring.sleuth.sampler.probability=0.5
```

Om du redan har skapat och distribuerat ett program kan du ändra samplings frekvensen. Gör detta genom att lägga till föregående rad som en miljö variabel i Azure CLI eller Azure Portal.
::: zone-end

## <a name="enable-application-insights"></a>Aktivera Application Insights

1. Gå till sidan Azure våren Cloud service i Azure Portal.
1. På sidan **övervakning** väljer du **distribuerad spårning**.
1. Välj **Redigera inställning** för att redigera eller lägga till en ny inställning.
1. Skapa en ny Application Insights fråga eller Välj en befintlig.
1. Välj vilken loggnings kategori som du vill övervaka och ange Retentions tiden i dagar.
1. Välj **tillämpa** för att tillämpa den nya spårningen.

## <a name="view-the-application-map"></a>Visa program kartan

Gå tillbaka till sidan **distribuerad spårning** och välj **Visa program karta**. Granska den visuella representationen av dina program och övervaknings inställningar. Information om hur du använder program kartan finns i [program karta: prioritering distribuerade program](../azure-monitor/app/app-map.md).

## <a name="use-search"></a>Använd Sök

Använd Sök funktionen för att fråga efter andra speciella telemetri-objekt. På sidan **distribuerad spårning** väljer du **Sök**. Mer information om hur du använder Sök funktionen finns i [använda Sök i Application Insights](../azure-monitor/app/diagnostic-search.md).

## <a name="use-application-insights"></a>Använd Application Insights

Application Insights tillhandahåller övervaknings funktioner utöver program kartan och Sök funktionen. Sök i Azure Portal efter programmets namn och öppna sedan en Application Insights sida för att hitta övervaknings information. Mer information om hur du använder dessa verktyg finns i [Azure Monitor logg frågor](/azure/data-explorer/kusto/query/).

## <a name="disable-application-insights"></a>Inaktivera Application Insights

1. Gå till sidan Azure våren Cloud service i Azure Portal.
1. Vid **övervakning** väljer du **distribuerad spårning**.
1. Välj **inaktivera** om du vill inaktivera Application Insights.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och förstår distribuerad spårning i Azure våren Cloud. Information om hur du binder tjänster till ett program finns i [binda en Azure Cosmos DB-databas till ett Azure våren Cloud-program](spring-cloud-tutorial-bind-cosmos.md).