---
title: Hantera NSG Flow-loggar – Azure CLI
titleSuffix: Azure Network Watcher
description: På den här sidan förklaras hur du hanterar flödes loggar för nätverks säkerhets grupper i Azure Network Watcher med Azure CLI
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
ms.openlocfilehash: 46d12db413fdf01995bc84ae018065e877afb15e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017824"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurera flödes loggar för nätverks säkerhets grupper med Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödes loggar för nätverks säkerhets grupper är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverks säkerhets grupp. Dessa flödes loggar skrivs i JSON-format och visar utgående och inkommande flöden per regel, vilket nätverkskort flödet gäller för, 5-tuple-information om flödet (käll-/mål-IP, käll-och mål Port, protokoll) och om trafiken tillåts eller nekas.

För att utföra stegen i den här artikeln måste du [Installera Azures kommando rads gränssnitt för Mac, Linux och Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödes loggningen ska fungera korrekt måste **Microsoft. Insights** -providern vara registrerad. Om du inte är säker på om **Microsoft. Insights** -providern är registrerad kör du följande skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera flödes loggar för nätverks säkerhets grupper

Kommandot för att aktivera flödes loggar visas i följande exempel:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

Det lagrings konto som du anger kan inte ha nätverks regler som har kon figurer ATS för att begränsa nätverks åtkomsten till enbart Microsoft-tjänster eller vissa virtuella nätverk. Lagrings kontot kan vara i samma eller en annan Azure-prenumeration än NSG som du aktiverar flödes loggen för. Om du använder olika prenumerationer måste båda vara kopplade till samma Azure Active Directory-klient. Det konto som du använder för varje prenumeration måste ha de [behörigheter som krävs](required-rbac-permissions.md). 

Om lagrings kontot finns i en annan resurs grupp eller prenumeration än nätverks säkerhets gruppen anger du det fullständiga ID: t för lagrings kontot i stället för namnet. Om lagrings kontot till exempel finns i en resurs grupp med namnet *RG-Storage*, i stället för att ange *storageAccountName* i föregående kommando, anger du */Subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera flödes loggar för nätverks säkerhets grupper

Använd följande exempel för att inaktivera flödes loggar:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Hämta en flödes logg

Lagrings platsen för en flödes logg definieras när den skapas. Ett användbart verktyg för att komma åt dessa flödes loggar som sparas till ett lagrings konto är Microsoft Azure Storage Explorer, som kan hämtas här:  https://storageexplorer.com/

Om ett lagrings konto har angetts sparas flödes logg filen på ett lagrings konto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualiserar dina NSG Flow-loggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG Flow-loggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
