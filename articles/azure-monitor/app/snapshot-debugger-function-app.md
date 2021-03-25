---
title: Aktivera Snapshot Debugger för .NET-och .NET Core-appar i Azure Functions | Microsoft Docs
description: Aktivera Snapshot Debugger för .NET-och .NET Core-appar i Azure Functions
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: 48eb3cf81384446a07fea69572ac16e0b80cee38
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025636"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Aktivera Snapshot Debugger för .NET-och .NET Core-appar i Azure Functions

Snapshot Debugger fungerar för närvarande för ASP.NET och ASP.NET Core appar som körs på Azure Functions i Windows Service-planer.

Vi rekommenderar att du kör programmet på tjänst nivån Basic eller högre när du använder Snapshot Debugger.

För de flesta program har inte de kostnads fria och delade tjänst nivåerna tillräckligt med minne eller disk utrymme för att spara ögonblicks bilder.

## <a name="prerequisites"></a>Förutsättningar

* [Aktivera Application Insights övervakning i Funktionsapp](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Aktivera Snapshot Debugger

Om du kör en annan typ av Azure-tjänst, finns här instruktioner för att aktivera Snapshot Debugger på andra plattformar som stöds:
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalnings uppsättningar för virtuella datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Om du vill aktivera Snapshot Debugger i din Function-app måste du uppdatera `host.json` filen genom att lägga till egenskapen `snapshotConfiguration` som definieras nedan och distribuera om funktionen.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Snapshot Debugger är förinstallerat som en del av Azure Functions runtime, vilket som standard är inaktiverat.

Eftersom Snapshot Debugger den ingår i Azure Functions runtime behöver du inte lägga till extra NuGet-paket eller program inställningar.

Precis som referens, för en enkel Function-app (.NET Core), ser det ut så här ser det ut `.csproj` , `{Your}Function.cs` och `host.json` efter aktiverat Snapshot debugger på den.

Project-CSPROJ

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Funktions klass

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Värd fil

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Aktivera Snapshot Debugger för andra moln

För närvarande är de enda regionerna som kräver slut punkts ändringar [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) och [Azure Kina](/azure/china/resources-developer-guide).

Nedan visas ett exempel på den `host.json` uppdaterade med den amerikanska regeringens moln agents slut punkt:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

Nedan visas de åsidosättningar som stöds av Snapshot Debugger agent-slutpunkten:

|Egenskap    | Moln för amerikanska myndigheter | Kina, moln |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>Inaktivera Snapshot Debugger

Om du vill inaktivera Snapshot Debugger i din Function-app behöver du bara uppdatera `host.json` filen genom att ställa in på `false` egenskapen `snapshotConfiguration.isEnabled` .

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

Vi rekommenderar att du har Snapshot Debugger aktiverat i alla dina appar för att under lätta diagnostiken av program undantag.

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program som kan utlösa ett undantag. Vänta sedan 10 till 15 minuter för ögonblicks bilder som ska skickas till Application Insights-instansen.
- [Visa ögonblicks bilder](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) i Azure Portal.
- Anpassa Snapshot Debugger konfiguration baserat på ditt användnings fall i din Function-app. Mer information finns i [ögonblicks bilds konfiguration i host.jspå](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Hjälp med fel sökning Snapshot Debugger problem finns i [Snapshot debugger fel sökning](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).