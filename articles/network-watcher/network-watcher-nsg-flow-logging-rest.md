---
title: Hantera NSG Flow-loggar – Azure REST API
titleSuffix: Azure Network Watcher
description: På den här sidan förklaras hur du hanterar flödes loggar för nätverks säkerhets grupper i Azure Network Watcher med REST API
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
ms.openlocfilehash: ea24716dba5e4e824a4fa986602007035be8e365
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018385"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurera flödes loggar för nätverks säkerhets grupper med hjälp av REST API

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödes loggar för nätverks säkerhets grupper är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp. Dessa flödes loggar skrivs i JSON-format och visar utgående och inkommande flöden per regel, vilket nätverkskort flödet gäller för, 5-tuple-information om flödet (käll-/mål-IP, käll-och mål Port, protokoll) och om trafiken tillåts eller nekas.

## <a name="before-you-begin"></a>Innan du börjar

ARMclient används för att anropa REST API med hjälp av PowerShell. ARMClient finns på choklad på [ARMClient](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [skapa ett Network Watcher](network-watcher-create.md) för att skapa ett Network Watcher.

> [!Important]
> För Network Watcher REST API anropar resurs grupps namnet i URI-begäran den resurs grupp som innehåller Network Watcher, inte de resurser som du utför diagnostiska åtgärder på.

## <a name="scenario"></a>Scenario

I det scenario som beskrivs i den här artikeln beskrivs hur du aktiverar, inaktiverar och frågar flödes loggar med hjälp av REST API. Läs mer om flödes loggning för nätverks säkerhets grupper genom att besöka [nätverks säkerhets grupp flöde logga loggning – översikt](network-watcher-nsg-flow-logging-overview.md).

I det här scenariot kommer du att:

* Aktivera flödes loggar (version 2)
* Inaktivera flödes loggar
* Status för flödes loggar för frågor

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

Logga in på armclient med dina Azure-autentiseringsuppgifter.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödes loggningen ska fungera korrekt måste **Microsoft. Insights** -providern vara registrerad. Om du inte är säker på om **Microsoft. Insights** -providern är registrerad kör du följande skript.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera flödes loggar för nätverks säkerhets grupper

Kommandot för att aktivera flödes loggar version 2 visas i följande exempel. För version 1 ersätter du fältet ' version ' med ' 1 ':

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

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera flödes loggar för nätverks säkerhets grupper

Använd följande exempel för att inaktivera flödes loggar. Anropet är detsamma som att aktivera flödes loggar, förutom **falskt** anges för egenskapen Enabled.

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

## <a name="query-flow-logs"></a>Skicka frågor till flödes loggar

Följande REST-anrop frågar efter status för flödes loggar i en nätverks säkerhets grupp.

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

## <a name="download-a-flow-log"></a>Hämta en flödes logg

Lagrings platsen för en flödes logg definieras när den skapas. Ett användbart verktyg för att komma åt dessa flödes loggar som sparas till ett lagrings konto är Microsoft Azure Storage Explorer, som kan hämtas här:  https://storageexplorer.com/

Om ett lagrings konto anges sparas paket insamlings filer till ett lagrings konto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualiserar dina NSG Flow-loggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG Flow-loggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
