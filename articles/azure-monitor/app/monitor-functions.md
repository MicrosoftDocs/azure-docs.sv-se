---
title: Övervaka program som körs på Azure Functions med Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor integreras sömlöst med ditt program som körs på Azure Functions och gör att du kan övervaka prestanda och upptäcka problem med dina appar på en gång.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 6d790a6f02fdc07609e374639c6e452b9088262e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87024579"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Övervaka Azure Functions med Azure Monitor Application Insights

[Azure Functions](../../azure-functions/functions-overview.md) erbjuder inbyggd integrering med Azure Application insikter för att övervaka funktioner. 

Application Insights samlar in logg-, prestanda-och fel data och identifierar automatiskt prestanda avvikelser. Application Insights innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå hur dina funktioner används. När du har insyn i dina program data kan du kontinuerligt förbättra prestanda och användbarhet. Du kan även använda Application Insights under utveckling av program projekt i lokal funktion. 

Den nödvändiga Application Insights Instrumentation är inbyggd i Azure Functions. Det enda du behöver är en giltig Instrumentation-nyckel för att ansluta din app till en Application Insights-resurs. Instrumentation-nyckeln ska läggas till i program inställningarna när din Function-app-resurs skapas i Azure. Om din Function-app inte redan har den här nyckeln kan du ange den manuellt. Mer information finns i mer om [övervaknings Azure Functions](../../azure-functions/functions-monitoring.md?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Distribuerad spårning för Java-program i Windows (offentlig för hands version)

> [!IMPORTANT]
> Den här funktionen finns för närvarande i en offentlig för hands version av Java Azure Functions i Windows, distribuerad spårning för Java Azure Functions på Linux stöds inte. För förbruknings plan har den ett kall start på 8-9 sekunder.

Om dina program är skrivna i Java kan du Visa rikare data från dina Functions-program, inklusive, förfrågningar, beroenden, loggar och mått. Med ytterligare data kan du också se och diagnostisera transaktioner från slut punkt till slut punkt och se program kartan, som sammanställer många transaktioner för att visa en Top-vy över hur systemen interagerar och vilka genomsnitts prestanda och fel frekvens som är.

Den kompletta diagnostiken och program kartan ger insyn i en enda transaktion/begäran. Tillsammans är de här två funktionerna användbara för att hitta rotor saken till problem med tillförlitlighet och prestanda Flask halsar per begäran.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Hur aktiverar jag distribuerad spårning för Java Function-appar?

Gå till bladet Functions-översikt, gå till konfigurationer. Under program inställningar klickar du på "+ ny program inställning". Lägg till följande två program inställningar med värden nedan och klicka sedan på Spara längst upp till vänster. MÖJLIGT!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du övervakar övervaknings [Azure Functions](../../azure-functions/functions-monitoring.md)
* Få en översikt över [Distributed tracing](./distributed-tracing.md)
* Se vilken [program karta](./app-map.md?tabs=net) som kan utföras för ditt företag
* Läs om [begär Anden och beroenden för Java-appar](./java-in-process-agent.md)
* Läs mer om [Azure Monitor](../overview.md) och [Application Insights](./app-insights-overview.md)
