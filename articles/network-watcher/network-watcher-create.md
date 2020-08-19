---
title: Skapa en Azure Network Watcher-instans | Microsoft Docs
description: Lär dig hur du skapar en Azure-Network Watcher i en Azure-region med hjälp av Azure Portal eller andra tekniker och hur du tar bort en Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 6809e8a44120eddbae4965bff30f80b617bdd024
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272169"
---
# <a name="create-an-azure-network-watcher-instance"></a>Skapa en Azure Network Watcher-instans

Network Watcher är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure. Med övervakning av scenarionivå kan du diagnostisera problem på en slutpunkt-till-slutpunkt-vy på nätverks nivå. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. Network Watcher aktive ras genom att en Network Watcher-resurs skapas. Med den här resursen kan du använda Network Watcher-funktioner.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher aktive ras automatiskt
När du skapar eller uppdaterar ett virtuellt nätverk i din prenumeration aktiveras Network Watcher automatiskt i din virtuella nätverksregion. Dina resurser påverkas inte av den automatiska aktiveringen av Network Watcher, och inga kostnader tillkommer.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Inaktivera Network Watcher automatisk aktivering
Om du vill inaktivera Network Watcher automatisk aktivering kan du göra det genom att köra följande kommandon:

> [!WARNING]
> Väljer-Network Watcher automatisk aktivering är en permanent ändring. När du har avanmält dig kan du inte välja utan att [kontakta supporten](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Skapa en Network Watcher i portalen

Gå till **alla tjänster**  >  **nätverk**  >  **Network Watcher**. Du kan välja alla prenumerationer som du vill aktivera Network Watcher för. Den här åtgärden skapar en Network Watcher i varje region som är tillgänglig.

![skapa en nätverks övervakare](./media/network-watcher-create/figure1.png)

När du aktiverar Network Watcher med hjälp av portalen anges namnet på Network Watcher-instansen automatiskt till *NetworkWatcher_region_name* där *Region_name* motsvarar den Azure-region där instansen är aktive rad. Till exempel heter en Network Watcher som är aktive rad i regionen västra centrala USA *NetworkWatcher_westcentralus*.

Network Watcher-instansen skapas automatiskt i en resurs grupp med namnet *NetworkWatcherRG*. Resurs gruppen skapas om den inte redan finns.

Om du vill anpassa namnet på en Network Watcher-instans och resurs gruppen som den placeras i, kan du använda PowerShell, Azure CLI, REST API eller ARMClient metoder som beskrivs i avsnitten som följer. I varje alternativ måste resurs gruppen finnas innan du skapar en Network Watcher.  

## <a name="create-a-network-watcher-with-powershell"></a>Skapa en Network Watcher med PowerShell

Kör följande exempel för att skapa en instans av Network Watcher:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Skapa en Network Watcher med Azure CLI

Kör följande exempel för att skapa en instans av Network Watcher:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Skapa en Network Watcher med REST API

ARMclient används för att anropa REST API med hjälp av PowerShell. ARMClient finns på choklad på [ARMClient på choklad](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Logga in med ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Skapa nätverks bevakaren

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>Skapa en Network Watcher med hjälp av Azure snabb starts mal len

Om du vill skapa en instans av Network Watcher kan du se den här [snabb starts mallen](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/)

## <a name="delete-a-network-watcher-in-the-portal"></a>Ta bort ett Network Watcher i portalen

Gå till **alla tjänster**  >  **nätverk**  >  **Network Watcher**.

Välj fliken Översikt om du inte redan är där. Använd List rutan för att välja den prenumeration som du vill inaktivera nätverks övervakaren i.
Expandera listan över regioner för din valda prenumeration genom att klicka på pilen. Använd de 3 punkterna till höger för att få åtkomst till snabb menyn.
Klicka på "inaktivera Network Watcher" för att börja inaktivera. Du uppmanas att bekräfta det här steget. Klicka på Ja för att fortsätta.
På portalen måste du göra detta individuellt för varje region i varje prenumeration.


## <a name="delete-a-network-watcher-with-powershell"></a>Ta bort en Network Watcher med PowerShell

Om du vill ta bort en instans av Network Watcher kör du följande exempel:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en instans av Network Watcher kan du läsa mer om tillgängliga funktioner:

* [Topologi](network-watcher-topology-overview.md)
* [Paketfångst](network-watcher-packet-capture-overview.md)
* [Kontrollera IP-flöde](network-watcher-ip-flow-verify-overview.md)
* [Nästa hopp](network-watcher-next-hop-overview.md)
* [Säkerhetsgruppvy](network-watcher-security-group-view-overview.md)
* [NSG flödes loggning](network-watcher-nsg-flow-logging-overview.md)
* [Virtual Network Gateway-felsökning](network-watcher-troubleshoot-overview.md)
