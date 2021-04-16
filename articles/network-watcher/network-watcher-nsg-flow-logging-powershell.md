---
title: Hantera NSG-flödesloggar – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Den här sidan förklarar hur du hanterar flödesloggar för nätverkssäkerhetsgrupp i Azure Network Watcher med PowerShell
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
ms.openlocfilehash: 29340852cabcc77b7488f734a4677697b4a9b972
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535220"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurera flödesloggar för nätverkssäkerhetsgrupp med PowerShell

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödesloggar för nätverkssäkerhetsgrupp är en funktion i Network Watcher som gör att du kan visa information om in- och utgående IP-trafik via en nätverkssäkerhetsgrupp. Dessa flödesloggar skrivs i json-format och visar utgående och inkommande flöden per regel, det nätverkskort som flödet gäller för, 5-tuppelinformation om flödet (käll-/mål-IP, käll-/målport, protokoll) och om trafiken tilläts eller nekades.

Den detaljerade specifikationen för alla kommandon för NSG-flödesloggar för olika versioner av AzPowerShell finns [här](https://docs.microsoft.com/powershell/module/az.network/#network-watcher)

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödesloggningen ska fungera korrekt måste **Providern Microsoft.Insights** vara registrerad. Om du inte är säker på om **Providern Microsoft.Insights** är registrerad kör du följande skript.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Aktivera flödesloggar och Trafikanalys

Kommandot för att aktivera flödesloggar visas i följande exempel:

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

Det lagringskonto som du anger kan inte ha nätverksregler konfigurerade för det som begränsar nätverksåtkomsten till endast Microsoft-tjänster eller specifika virtuella nätverk. Lagringskontot kan finnas i samma eller en annan Azure-prenumeration än den NSG som du aktiverar flödesloggen för. Om du använder olika prenumerationer måste båda vara kopplade till samma Azure Active Directory klientorganisation. Det konto som du använder för varje prenumeration måste ha [de behörigheter som krävs.](required-rbac-permissions.md)

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Inaktivera Trafikanalys och flödesloggar för nätverkssäkerhetsgrupp

Använd följande exempel för att inaktivera trafikanalys och flödesloggar:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Ladda ned en flödeslogg

Lagringsplatsen för en flödeslogg definieras när den skapas. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats till ett lagringskonto är Microsoft Azure Storage Explorer, som du kan hämta här:  https://storageexplorer.com/

Om ett lagringskonto anges sparas flödesloggfilerna till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Information om loggens struktur finns i Översikt [över flödesloggen för nätverkssäkerhetsgrupp](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualiserar dina NSG-flödesloggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG-flödesloggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
