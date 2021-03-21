---
title: Aktivera Snapshot Debugger för .NET-appar i Azure App Service | Microsoft Docs
description: Aktivera Snapshot Debugger för .NET-appar i Azure App Service
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 5a637a6f355be32f82878a52a30c77d020190651
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211625"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Aktivera Snapshot Debugger för .NET-appar i Azure App Service

Snapshot Debugger stöder för närvarande ASP.NET och ASP.NET Core appar som körs på Azure App Service i Windows Service-planer.

Vi rekommenderar att du kör programmet på den grundläggande tjänst nivån eller senare när du använder fel sökning av ögonblicks bilder.

För de flesta program har inte de kostnads fria och delade tjänst nivåerna tillräckligt med minne eller disk utrymme för att spara ögonblicks bilder.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Aktivera Snapshot Debugger
Följ anvisningarna nedan om du vill aktivera Snapshot Debugger för en app.

Om du kör en annan typ av Azure-tjänst, finns här instruktioner för att aktivera Snapshot Debugger på andra plattformar som stöds:
* [Azure-funktion](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalnings uppsättningar för virtuella datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Om du använder en för hands version av .NET Core, eller om ditt program refererar Application Insights SDK, direkt eller indirekt via en beroende sammansättning, följer du anvisningarna för att [aktivera Snapshot debugger för andra miljöer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som innehåller NuGet-paketet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) med programmet och Slutför resten av anvisningarna nedan. 
>
> Kod installation av Application Insights Snapshot Debugger följer support policyn för .NET Core.
> Mer information om körningar som stöds finns i [.net Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Snapshot Debugger är förinstallerat som en del av App Services runtime, men du måste aktivera det för att skapa ögonblicks bilder för din App Service-app.

När du har distribuerat en app följer du stegen nedan för att aktivera fel sökning av ögonblicks bild:

1. Navigera till Azures kontroll panel för din App Service.
2. Gå till sidan **inställningar > Application Insights** .

   ![Aktivera App Insights på App Services Portal](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Följ anvisningarna på sidan för att skapa en ny resurs eller Välj en befintlig App Insights-resurs för att övervaka din app. Se också till att båda växlar för Snapshot Debugger finns **på**.

   ![Lägg till App Insights-webbplatsens tillägg][Enablement UI]

4. Snapshot Debugger har nu Aktiver ATS med en inställning för App Services app.

    ![App-inställning för Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Aktivera Snapshot Debugger för andra moln

För närvarande är de enda regionerna som kräver slut punkts ändringar [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) och [Azure Kina](https://docs.microsoft.com/azure/china/resources-developer-guide) genom Application Insights anslutnings sträng.

|Egenskap för anslutnings sträng    | Moln för amerikanska myndigheter | Kina, moln |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Mer information om andra åsidosättningar av anslutningar finns i [Application Insights-dokumentationen](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net#connection-string-with-explicit-endpoint-overrides).

## <a name="disable-snapshot-debugger"></a>Inaktivera Snapshot Debugger

Följ samma steg som för **aktivering av Snapshot debugger**, men växla båda växlar för Snapshot debugger till **av**.

Vi rekommenderar att du har Snapshot Debugger aktiverat i alla dina appar för att under lätta diagnostiken av program undantag.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

För en Azure App Service kan du ange inställningar för appar i Azure Resource Manager-mallen för att aktivera Snapshot Debugger och profiler, se följande mall-kodfragment:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Nästa steg

- Generera trafik till ditt program som kan utlösa ett undantag. Vänta sedan 10 till 15 minuter för ögonblicks bilder som ska skickas till Application Insights-instansen.
- Se [ögonblicks bilder](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) i Azure Portal.
- Hjälp med fel sökning Snapshot Debugger problem finns i [Snapshot debugger fel sökning](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

