---
title: Profilera Live Azure Service Fabric-appar med Application Insights
description: Aktivera profiler för ett Service Fabric program
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589563"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilera Live Azure Service Fabric-program med Application Insights

Du kan också distribuera Application Insights Profiler på dessa tjänster:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurera definition av miljö distribution

Application Insights Profiler ingår i Azure-diagnostik. Du kan installera Azure-diagnostik-tillägget med hjälp av en Azure Resource Manager mall för ditt Service Fabric-kluster. Hämta en [mall som installerar Azure-diagnostik på ett Service Fabric-kluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Utför följande åtgärder för att konfigurera din miljö:

1. Profiler stöder .NET Framework och .net Core. Om du använder .NET Framework kontrollerar du att du använder [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) eller senare. Det räcker att bekräfta att det distribuerade operativ systemet är `Windows Server 2012 R2` eller senare. Profiler stöder .NET Core 2,1 och nyare program.

1. Sök efter [Azure-diagnostik](../agents/diagnostics-extension-overview.md) tillägget i distributions mal len fil.

1. Lägg till följande `SinksConfig` avsnitt som underordnat element i `WadCfg` . Ersätt `ApplicationInsightsProfiler` egenskap svärdet med din egen Application Insights Instrumentation-nyckel:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Information om hur du lägger till diagnostik-tillägget i distributions mal len finns i [använda övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager mallar](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json).

1. Distribuera Service Fabric-klustret med hjälp av din Azure Resource Manager-mall.  
  Om inställningarna är korrekta kommer Application Insights Profiler att installeras och aktive ras när Azure-diagnostik-tillägget har installerats. 

1. Lägg till Application Insights i ditt Service Fabric-program.  
  För att profileraren ska kunna samla in profiler för dina begär Anden måste ditt program spåra åtgärder med Application Insights. För tillstånds lösa API: er kan du referera till instruktioner för att [spåra begär Anden om profilering](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Mer information om hur du spårar anpassade åtgärder i andra typer av appar finns i [spåra anpassade åtgärder med Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Distribuera om ditt program.


## <a name="next-steps"></a>Nästa steg

* Generera trafik till ditt program (till exempel starta ett [tillgänglighets test](monitor-web-app-availability.md)). Vänta sedan 10 till 15 minuter så att spårningar börjar skickas till Application Insights-instansen.
* Se [profilerade spår](profiler-overview.md?toc=/azure/azure-monitor/toc.json) i Azure Portal.
* Hjälp med fel sökning av profilerings problem finns i [fel sökning av profileraren](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
