---
title: Använda Application Insights Java-In-Process agent i Azure våren Cloud
description: Övervaka appar och mikrotjänster med hjälp av Application Insights Java In-Process agent i Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f6f1ed1a3e09397a720dfd4d842b79cd88aa738d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878561"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Java In-Process agent i Azure våren Cloud (för hands version)

Det här dokumentet beskriver hur du övervakar appar och mikrotjänster med Application Insights Java-agenten i Azure våren Cloud. 

Med den här funktionen kan du:

* Sök spårnings data med olika filter.
* Visa beroende karta för mikrotjänster.
* Kontrol lera prestanda för begäran.
* Övervaka Live-mått i real tid.
* Kontrol lera begär Anden som Miss lyckas.
* Kontrol lera program måtten.

Application Insights ge många observerbara perspektiv, inklusive:

* Programkarta
* Prestanda
* Fel
* Mått
* Live Metrics
* Tillgänglighet

## <a name="enable-java-in-process-agent-for-application-insights"></a>Aktivera Java In-Process agent för Application Insights

Aktivera för hands versionen av Java In-Process-agenten med hjälp av följande procedur.

1. Gå till tjänst översikts sidan för din tjänst instans.
2. Klicka på **Application Insights** post under övervaknings bladet.
3. Klicka på **aktivera Application Insights** om du vill aktivera **Application Insights** -integrering.
4. Välj en befintlig instans av Application Insights eller skapa en ny.
5. Kyckling **Aktivera Java in process-agenten** för att aktivera förhands granskning av java i process agent-funktionen. Här kan du också anpassa samplings frekvensen från 0 till 100.
6.  Spara ändringen genom att klicka på **Spara**.

## <a name="portal"></a>Portal

1. Gå till **tjänsten | Sidan översikt** och välj **Application Insights** i avsnittet **övervakning** . 
2. Aktivera Application Insights i Azure våren Cloud genom att klicka på **aktivera Application Insights** .
3. Klicka på **Aktivera Java-agent i processen** för att aktivera funktionen för för hands version av Java-IPA. När en IPA för hands version är aktive rad kan du konfigurera en valfri samplings frekvens (standard 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Använda funktionen Application Insights

När **Application Insights** funktionen är aktive rad kan du:

I det vänstra navigerings fönstret klickar du på **Application Insights** för att gå till **översikts** sidan för Application Insights. 

* Klicka på **program karta** för att se status för anrop mellan program.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Klicka på länken mellan kunder – tjänst och `petclinic` för att se mer information, till exempel en fråga från SQL.

* I det vänstra navigerings fönstret klickar du på **prestanda** för att visa prestanda data för alla programs åtgärder, samt beroenden och roller.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* I det vänstra navigerings fönstret klickar du på **fel** för att se om något oväntade från dina program.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* I det vänstra navigerings fönstret klickar du på **mått** och väljer namn området. du kommer att se både våren Boot-mått och anpassade mått, om det finns några.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* I det vänstra navigerings fönstret klickar du på **Live-mått** för att se Real tids mått för olika dimensioner.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* I det vänstra navigerings fönstret klickar du på **tillgänglighet** för att övervaka tillgänglighet och svars tider för webbappar genom att skapa [tillgänglighets test i Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM-mall
Om du vill använda mallen Azure Resource Manager kopierar du följande innehåll till `azuredeploy.json` .

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

* För en befintlig Azure våren Cloud-instans:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* För en nyligen skapad Azure våren-moln instans:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* Så här inaktiverar du program insikter:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Se även
* [Använd distribuerad spårning med Azure våren Cloud](spring-cloud-howto-distributed-tracing.md)
* [Analysera loggar och mått](diagnostic-services.md)
* [Strömma loggar i realtid](spring-cloud-howto-log-streaming.md)