---
title: Övervakning av Azure Service Fabric program nivå
description: Lär dig mer om program-och service nivå händelser och loggar som används för att övervaka och diagnostisera Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: a1df5d033701195f4fe5f6b7174f3883b84393ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791281"
---
# <a name="application-logging"></a>Programloggning

Att instrumentera din kod är inte bara ett sätt att få insikter om dina användare, utan även det enda sättet du kan veta om något är fel i ditt program och för att diagnosticera vad som behöver åtgärdas. Även om det är tekniskt möjligt att ansluta en fel sökare till en produktions tjänst är det inte en vanlig metod. Därför är det viktigt att ha detaljerad information om instrumentering.

För vissa produkter instrumenteras koden automatiskt. Även om dessa lösningar fungerar bra, är manuell Instrumentation nästan alltid nödvändigt för att vara specifika för din affärs logik. I slutet måste du ha tillräckligt med information för att forensically ska felsöka programmet. Service Fabric program kan instrumenteras med valfritt loggnings ramverk. I det här dokumentet beskrivs några olika metoder för att instrumentera din kod och när du vill välja en annan metod än en annan. 

Exempel på hur du använder dessa förslag finns i [lägga till loggning i Service Fabric-programmet](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights har en omfattande integrering med Service Fabric. Användare kan lägga till AI-Service Fabric NuGet-paket och ta emot data och loggar som skapats och samlats in i Azure Portal. Dessutom uppmanas användarna att lägga till sin egen telemetri för att kunna diagnostisera och felsöka sina program och spåra vilka tjänster och delar av programmet som används mest. [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) -klassen i SDK erbjuder många olika sätt att spåra telemetri i dina program. Kolla in ett exempel på hur du kan Instrumenta och lägga till Application Insights i ditt program i vår självstudie för att [övervaka och diagnostisera ett .NET-program](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

När du skapar en Service Fabric-lösning från en mall i Visual Studio genereras en **EventSource**-härledd klass (**ServiceEventSource** eller **ActorEventSource**). En mall skapas där du kan lägga till händelser för ditt program eller din tjänst. **EventSource** namn **måste** vara unikt och får inte ges ett nytt namn från standardmalls strängen Company-Solution- &lt; &gt; - &lt; projekt &gt; . Om du har flera **EventSource** -definitioner som använder samma namn orsakar det ett problem vid körnings tillfället. Varje definierad händelse måste ha en unik identifierare. Om en identifierare inte är unik uppstår ett körnings fel. Vissa organisationer förtilldelar värde intervall för identifierare för att undvika konflikter mellan separata utvecklings team. Mer information finns i [Vance blogg](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) eller [MSDN-dokumentationen](/previous-versions/msp-n-p/dn774985(v=pandp.20)).

## <a name="aspnet-core-logging"></a>ASP.NET Core loggning

Det är viktigt att planera noggrant hur du ska planera din kod. Den rätta instrument planen kan hjälpa dig att undvika att eventuellt göra en stabilisering av kodbasen och sedan behöver du Omplanera koden. Du kan minska risken genom att välja ett instrument bibliotek som [Microsoft. Extensions. logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), som är en del av Microsoft ASP.net Core. ASP.NET Core har ett [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) -gränssnitt som du kan använda med valfri Provider, samtidigt som du minimerar effekten på befintlig kod. Du kan använda koden i ASP.NET Core på Windows och Linux, och i den fullständiga .NET Framework, så att Instrumentation-koden är standardiserad.

## <a name="next-steps"></a>Nästa steg

När du har valt din loggnings leverantör för att Instrumenta dina program och tjänster måste dina loggar och händelser aggregeras innan de kan skickas till någon analys plattform. Läs mer om [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) och [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) för att bättre förstå några av de Azure Monitor rekommenderade alternativen.
