---
title: Hantera NSG Flow-loggar – Azure PowerShell
titleSuffix: Azure Network Watcher
description: På den här sidan förklaras hur du hanterar flödes loggar för nätverks säkerhets grupper i Azure Network Watcher med PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 771b4ce2999357d729c3ffe557b778cf62a5c0f6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98010990"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurera flödes loggar för nätverks säkerhets grupper med PowerShell

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödes loggar för nätverks säkerhets grupper är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp. Dessa flödes loggar skrivs i JSON-format och visar utgående och inkommande flöden per regel, vilket nätverkskort flödet gäller för, 5-tuple-information om flödet (käll-/mål-IP, käll-och mål Port, protokoll) och om trafiken tillåts eller nekas.

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödes loggningen ska fungera korrekt måste **Microsoft. Insights** -providern vara registrerad. Om du inte är säker på om **Microsoft. Insights** -providern är registrerad kör du följande skript.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Aktivera flödes loggar för nätverks säkerhets grupper och Trafikanalys

Kommandot för att aktivera flödes loggar visas i följande exempel:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Det lagrings konto som du anger kan inte ha nätverks regler som har kon figurer ATS för att begränsa nätverks åtkomsten till enbart Microsoft-tjänster eller vissa virtuella nätverk. Lagrings kontot kan vara i samma eller en annan Azure-prenumeration än NSG som du aktiverar flödes loggen för. Om du använder olika prenumerationer måste båda vara kopplade till samma Azure Active Directory-klient. Det konto som du använder för varje prenumeration måste ha de [behörigheter som krävs](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Inaktivera flödes loggar för Trafikanalys och nätverks säkerhets grupper

Använd följande exempel för att inaktivera trafik analyser och flödes loggar:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Hämta en flödes logg

Lagrings platsen för en flödes logg definieras när den skapas. Ett användbart verktyg för att komma åt dessa flödes loggar som sparas till ett lagrings konto är Microsoft Azure Storage Explorer, som kan hämtas här:  https://storageexplorer.com/

Om ett lagrings konto har angetts sparas flödes logg filen på ett lagrings konto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Mer information om strukturen för logg besöks [logg översikt över nätverks säkerhets grupp](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Efterföljande moment

Lär dig hur du [visualiserar dina NSG Flow-loggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG Flow-loggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
