---
title: Hantera NSG-flödesloggar – Azure CLI
titleSuffix: Azure Network Watcher
description: Den här sidan beskriver hur du hanterar flödesloggar för nätverkssäkerhetsgrupp i Azure Network Watcher med Azure CLI
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
ms.openlocfilehash: a25d14660e5006aca2913053b17852c752c786d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535266"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurera flödesloggar för nätverkssäkerhetsgrupp med Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödesloggar för nätverkssäkerhetsgrupp är en funktion i Network Watcher som gör att du kan visa information om in- och utgående IP-trafik via en nätverkssäkerhetsgrupp. Dessa flödesloggar skrivs i json-format och visar utgående och inkommande flöden per regel, det nätverkskort som flödet gäller för, 5-tuppelinformation om flödet (käll-/mål-IP, käll-/målport, protokoll) och om trafiken tilläts eller nekades.

Om du vill utföra stegen i den här artikeln måste du installera [Azure-kommandoradsgränssnittet för Mac, Linux och Windows (CLI).](/cli/azure/install-azure-cli) Den detaljerade specifikationen för alla flödesloggkommandon finns [här](https://docs.microsoft.com/cli/azure/network/watcher/flow-log?view=azure-cli-latest)

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödesloggningen ska fungera korrekt måste **providern Microsoft.Insights** vara registrerad. Om du inte är säker på om **Microsoft.Insights-providern** är registrerad kör du följande skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera flödesloggar för nätverkssäkerhetsgrupp

Kommandot för att aktivera flödesloggar visas i följande exempel:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

Det lagringskonto som du anger kan inte ha nätverksregler konfigurerade för det som begränsar nätverksåtkomsten till endast Microsoft-tjänster eller specifika virtuella nätverk. Lagringskontot kan finnas i samma eller en annan Azure-prenumeration än den NSG som du aktiverar flödesloggen för. Om du använder olika prenumerationer måste båda vara kopplade till samma Azure Active Directory klientorganisation. Det konto som du använder för varje prenumeration måste ha [de behörigheter som krävs.](required-rbac-permissions.md) 

Om lagringskontot finns i en annan resursgrupp eller prenumeration än nätverkssäkerhetsgruppen anger du lagringskontots fullständiga ID i stället för namnet. Om lagringskontot till exempel finns i en resursgrupp med namnet *RG-Storage* i stället för att ange *storageAccountName* i föregående kommando anger du */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera flödesloggar för nätverkssäkerhetsgrupp

Använd följande exempel för att inaktivera flödesloggar:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Ladda ned en flödeslogg

Lagringsplatsen för en flödeslogg definieras när den skapas. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats till ett lagringskonto är Microsoft Azure Storage Explorer, som du kan hämta här:  https://storageexplorer.com/

Om ett lagringskonto anges sparas flödesloggfilerna till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualiserar dina NSG-flödesloggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG-flödesloggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
