---
title: Hantera NSG-flödesloggar – Azure REST API
titleSuffix: Azure Network Watcher
description: Den här sidan förklarar hur du hanterar flödesloggar för nätverkssäkerhetsgrupp i Azure Network Watcher med REST API
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
ms.openlocfilehash: b45d066d0996aaba2a25500f8134085f5e9b6ffb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535190"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurera flödesloggar för nätverkssäkerhetsgruppen med hjälp av REST API

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödesloggar för nätverkssäkerhetsgrupp är en funktion i Network Watcher som gör att du kan visa information om in- och utgående IP-trafik via en nätverkssäkerhetsgrupp. Dessa flödesloggar skrivs i json-format och visar utgående och inkommande flöden per regel, det nätverkskort som flödet gäller för, 5-tuppelinformation om flödet (käll-/mål-IP, käll-/målport, protokoll) och om trafiken tilläts eller nekades.

## <a name="before-you-begin"></a>Innan du börjar

ARMclient används för att anropa REST API powershell. ARMClient finns på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient). Detaljerade specifikationer för NSG-flödesloggar REST API finns [här](https://docs.microsoft.com/rest/api/network-watcher/flowlogs) 

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en Network Watcher](network-watcher-create.md) för att skapa en Network Watcher.

> [!Important]
> För Network Watcher REST API anropar resursgruppens namn i URI:en för begäran är den resursgrupp som innehåller Network Watcher, inte de resurser som du utför diagnostikåtgärderna på.

## <a name="scenario"></a>Scenario

Scenariot som beskrivs i den här artikeln visar hur du aktiverar, inaktiverar och frågar flödesloggar med hjälp av REST API. Mer information om flödesloggningar för nätverkssäkerhetsgruppen finns i [Flödesloggning för nätverkssäkerhetsgrupp – översikt.](network-watcher-nsg-flow-logging-overview.md)

I det här scenariot kommer du att:

* Aktivera flödesloggar (version 2)
* Inaktivera flödesloggar
* Status för frågeflödesloggar

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

Logga in på armclient med dina Azure-autentiseringsuppgifter.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödesloggningen ska fungera måste **Providern Microsoft.Insights** vara registrerad. Om du inte är säker på om **Providern Microsoft.Insights** är registrerad kör du följande skript.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera flödesloggar för nätverkssäkerhetsgrupp

Kommandot för att aktivera flödesloggar version 2 visas i följande exempel. För version 1 ersätter du fältet "version" med "1":

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Svaret som returnerades från föregående exempel är följande:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera flödesloggar för nätverkssäkerhetsgrupp

Använd följande exempel för att inaktivera flödesloggar. Anropet är detsamma som att aktivera flödesloggar, **förutom att false** har angetts för den aktiverade egenskapen.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Svaret som returnerades från föregående exempel är följande:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Frågeflödesloggar

Följande REST-anrop frågar efter status för flödesloggar i en nätverkssäkerhetsgrupp.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Följande är ett exempel på svaret som returneras:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Ladda ned en flödeslogg

Lagringsplatsen för en flödeslogg definieras när den skapas. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats till ett lagringskonto är Microsoft Azure Storage Explorer, som du kan hämta här:  https://storageexplorer.com/

Om ett lagringskonto anges sparas paketfångstfiler till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualiserar dina NSG-flödesloggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG-flödesloggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
