---
title: Azure Service Fabric övervaknings partner
description: Lär dig hur du övervakar Azure Service Fabric-program,-kluster och-infrastruktur med lösningar för partner övervakning.
ms.topic: article
ms.date: 10/16/2018
ms.openlocfilehash: 92ac0627f02ccefdd4c93aa51cac7c9dd7790460
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627735"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric övervaknings partner

Den här artikeln visar hur du kan övervaka Service Fabric-program,-kluster och-infrastruktur med en fåtal av partner lösningar. Vi har arbetat med varje partner nedan för att skapa integrerade erbjudanden för Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Vår integrering med dynaTrace innehåller många färdiga Box-funktioner för att övervaka Service Fabric-kluster. Genom att installera dynaTrace-OneAgent på VMSS-instanserna får du prestanda räknare och en topologi för din Service Fabric distribution till app-nivå. DynaTrace är också ett bra alternativ för lokal övervakning. Titta närmare på de funktioner som anges i [meddelandet](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) och [instruktioner](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) för att aktivera dynaTrace i klustret. 

## <a name="datadog"></a>Datadog

Datadog har ett tillägg för VMSS för både Windows-och Linux-instanser. Med Datadog kan du samla in Windows-händelseloggar och på så sätt samla in Service Fabric plattforms händelser i Windows. Ta en titt på anvisningarna om hur du skickar diagnostikdata till Datadog [här](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Service Fabric-integrering med AppDynamics finns på program nivå. Genom att uppdatera miljövariabler och använda app Dynamics NuGets kan du skicka programtelemetri till AppDynamics. Läs följande [instruktioner](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) för hur du integrerar dina .net Service Fabric-program med AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic är ett annat program prestanda hanterings verktyg som integrerar väl med Service Fabric program. Du kan installera de nya Relic NuGet-paketen och lägga till specifika miljövariabler i dina MANIFEST filer för att skicka programtelemetri till nya Relic. Kolla in de här [anvisningarna](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) för att aktivera ny Relic-telemetri för dina .net Service Fabric-program.

## <a name="elk"></a>ELK 

ELK-stacken är en samling tekniker med öppen källkod: ElasticSearch, Logstash och Kibana. Genom att använda dessa tekniker i kombination kan du samla in, lagra och analysera Service Fabric övervakning och diagnostikdata. Vi har en själv studie kurs om hur du gör detta med Service Fabric inbyggda Java-program [här](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

Humio är en logg insamlings tjänst som kan samla in loggar från dina program och händelser från Service Fabric i molnet eller lokalt i real tid. Utöver direkt observationer erbjuder Humio funktioner för avancerad analys och visualisering för att visa och samla in information från diagnostiken. Humio har kostnads effektiva pris planer och är byggd för att skalas samtidigt som den har en snabb och lätt snabb hastighet. Den integreras direkt med Service Fabric plattforms händelser och programtelemetri. Du kan läsa mer om Humio och Service Fabric integration [här](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Nästa steg

* Få en [Översikt över övervakning och diagnostik](service-fabric-diagnostics-overview.md) i Service Fabric
* Lär dig hur du [diagnostiserar vanliga scenarier](service-fabric-diagnostics-common-scenarios.md) med våra första parts verktyg
