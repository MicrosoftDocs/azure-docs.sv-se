---
title: Så här använder du Application Insights Java In-Process Agent i Azure Spring Cloud
description: Så här övervakar du appar och mikrotjänster med hjälp Application Insights Java In-Process Agent i Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c7083cb6669d7bc779a8e69babfef38988819f8c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483781"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Java In-Process Agent i Azure Spring Cloud (förhandsversion)

Det här dokumentet beskriver hur du övervakar appar och mikrotjänster med hjälp Application Insights Java-agenten i Azure Spring Cloud. 

Med den här funktionen kan du:

* Sök efter spårningsdata med olika filter.
* Visa beroendekarta över mikrotjänster.
* Kontrollera begärandeprestanda.
* Övervaka realtidsbaserade livemått.
* Kontrollera misslyckade förfrågningar.
* Kontrollera programmått.

Application Insights ger många observerbara perspektiv, inklusive:

* Programkarta
* Prestanda
* Fel
* Mått
* Live Metrics
* Tillgänglighet

## <a name="enable-java-in-process-agent-for-application-insights"></a>Aktivera Java In-Process Agent för Application Insights

Aktivera förhandsversionsfunktionen för Java In-Process Agent med hjälp av följande procedur.

1. Gå till tjänstöversiktssidan för tjänstinstansen.
2. Klicka **Application Insights** under övervakningsbladet.
3. Klicka **på Application Insights** för att aktivera **Application Insights** integrering.
4. Välj en befintlig instans Application Insights skapa en ny.
5. Enable **Java in-process agent to** enable preview Java in-process agent feature. Här kan du också anpassa samplingsfrekvensen från 0 till 100.
6.  Spara ändringen genom att klicka på **Spara**.

## <a name="portal"></a>Portalen

1. Gå till **| Översiktssidan** och **Application Insights** i **avsnittet** Övervakning. 
2. Klicka **på Aktivera Application Insights** för att Application Insights i Azure Spring Cloud.
3. Klicka **på Enable Java in-process agent (Aktivera processagent** för Java) för att aktivera förhandsversionsfunktionen i Java IPA. När en IPA-förhandsgranskningsfunktion är aktiverad kan du konfigurera en valfri samplingsfrekvens (standardvärdet är 10,0 %).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Använda Application Insights funktion

När **Application Insights** har aktiverats kan du:

I det vänstra navigeringsfönstret klickar du **på Application Insights** för att gå **till översiktssidan** för Application Insights. 

* Klicka **på Programkarta** för att se status för anrop mellan program.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Klicka på länken mellan customers-service och `petclinic` om du vill se mer information, till exempel en fråga från SQL.

* I det vänstra navigeringsfönstret klickar **du på Prestanda** för att se prestandadata för alla programåtgärder, samt beroenden och roller.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* I det vänstra navigeringsfönstret klickar du på **Fel för** att se om något oväntat från dina program.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* I det vänstra navigeringsfönstret klickar du på Mått och väljer namnområdet. Du ser både Spring Boot mått och anpassade mått, om det finns några. 

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* I det vänstra navigeringsfönstret klickar du **på Live Metrics** för att se realtidsmått för olika dimensioner.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* I det vänstra navigeringsfönstret klickar du på **Tillgänglighet** för att övervaka tillgängligheten och svarstiden för webbappar genom att [skapa tillgänglighetstester i Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM-mall
Om du vill Azure Resource Manager mallen kopierar du följande innehåll till `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI
Använd ARM-mall med CLI-kommandot:

* För en befintlig Azure Spring Cloud instans:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" â€“name "assignedName" â€“resource-group "resourceGroupName"
```
* För en nyligen Azure Spring Cloud instans:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" â€“resource-group "resourceGroupName"
```
* Så här inaktiverar du appinsikter:

```azurecli
az spring-cloud app-insights update --disable â€“name "assignedName" â€“resource-group "resourceGroupName"

```

## <a name="see-also"></a>Se även
* [Använda distribuerad spårning med Azure Spring Cloud](spring-cloud-howto-distributed-tracing.md)
* [Analysera loggar och mått](diagnostic-services.md)
* [Strömma loggar i realtid](spring-cloud-howto-log-streaming.md)
