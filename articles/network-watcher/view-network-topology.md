---
title: Visa topologi för Azure Virtual Network | Microsoft Docs
description: Lär dig hur du visar resurserna i ett virtuellt nätverk och relationerna mellan resurserna.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: c04da65af27ebd5ac654bc059ae004c157a20f33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737537"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Visa topologin för ett virtuellt Azure-nätverk

I den här artikeln får du lära dig hur du visar resurser i ett Microsoft Azure virtuella nätverk och relationerna mellan resurserna. Ett virtuellt nätverk innehåller till exempel undernät. Undernät innehåller resurser, till exempel Azure Virtual Machines (VM). Virtuella datorer har ett eller flera nätverks gränssnitt. Varje undernät kan ha en nätverks säkerhets grupp och en routningstabell som är kopplad till den. Med topologin i Azure Network Watcher kan du Visa alla resurser i ett virtuellt nätverk, resurserna som är kopplade till resurser i ett virtuellt nätverk och relationerna mellan resurserna.

Du kan använda [Azure Portal](#azure-portal), [Azure CLI](#azure-cli)eller [PowerShell](#powershell) för att visa en topologi.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Visa topologi – Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som har de [behörigheter](required-rbac-permissions.md)som krävs.
2. I det övre vänstra hörnet i portalen väljer du **alla tjänster**.
3. I rutan **alla tjänster** filter anger du *Network Watcher*. När **Network Watcher** visas i resultatet markerar du det.
4. Välj **Topologi**. Att skapa en topologi kräver en nätverks övervakare i samma region som det virtuella nätverk som du vill skapa topologin för finns i. Om du inte har en nätverks övervakare aktive rad i den region där det virtuella nätverket som du vill skapa en topologi för finns i, skapas Network Watcher automatiskt i alla regioner. Nätverks bevakarna skapas i en resurs grupp med namnet **NetworkWatcherRG**.
5. Välj en prenumeration, resurs gruppen för ett virtuellt nätverk som du vill visa topologin för och välj sedan det virtuella nätverket. I följande bild visas en topologi för ett virtuellt nätverk med namnet *MyVnet* i resurs gruppen med namnet *MyResourceGroup*:

    ![Visa topologi](./media/view-network-topology/view-topology.png)

    Som du kan se i föregående bild innehåller det virtuella nätverket tre undernät. En virtuell dator har distribuerats i ett undernät. Den virtuella datorn har ett nätverks gränssnitt kopplat till det och en offentlig IP-adress som är kopplad till den. De andra två under näten har en kopplad routningstabell. Varje routningstabell innehåller två vägar. Ett undernät har en nätverks säkerhets grupp som är kopplad till den. Information om topologin visas bara för resurser som är:
    
    - I samma resurs grupp och region som det virtuella *myVnet* -nätverket. Till exempel visas inte en nätverks säkerhets grupp som finns i en annan resurs grupp än *MyResourceGroup*, även om nätverks säkerhets gruppen är kopplad till ett undernät i det virtuella *MyVnet* -nätverket.
    - I, eller är kopplade till resurser inom, *myVnet* virtuella nätverk. En nätverks säkerhets grupp som inte är kopplad till ett undernät eller ett nätverks gränssnitt i det virtuella *myVnet* -nätverket visas till exempel inte, även om nätverks säkerhets gruppen finns i resurs gruppen *MyResourceGroup* .

   Den topologi som visas i bilden är för det virtuella nätverk som skapas när du har distribuerat **väg trafiken via ett skript exempel för virtuella nätverk** som du kan distribuera med hjälp av [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)eller [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Välj **Hämta topologi** för att ladda ned bilden som en redigerbar fil i SVG-format.

Resurserna som visas i diagrammet är en del av nätverks komponenterna i det virtuella nätverket. När en nätverks säkerhets grupp till exempel visas, visas inte säkerhets reglerna i den i diagrammet. Även om de inte skiljer sig åt i diagrammet representerar linjerna en av två relationer: *inne slutning* eller *associerad*. Om du vill se en fullständig lista över resurser i det virtuella nätverket och typen av relation mellan resurserna genererar du topologin med [PowerShell](#powershell) eller [Azure CLI](#azure-cli).

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Visa topologi – Azure CLI

Du kan köra kommandona i de steg som följer:
- I Azure Cloud Shell, genom att välja **prova** överst till höger om ett kommando. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt där vanliga Azure-verktyg förinstalleras och konfigureras för användning med ditt konto.
- Genom att köra CLI från datorn. Om du kör CLI från datorn kräver stegen i den här artikeln Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

Kontot som du använder måste ha de [behörigheter](required-rbac-permissions.md)som krävs.

1. Om du redan har en nätverks övervakare i samma region som det virtuella nätverk som du vill skapa en topologi för går du vidare till steg 3. Skapa en resurs grupp som innehåller en nätverks övervakare med [AZ Group Create](/cli/azure/group). I följande exempel skapas resurs gruppen i regionen *östra* :

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Skapa en nätverks övervakare med [AZ Network Watcher-konfiguration](/cli/azure/network/watcher#az-network-watcher-configure). I följande exempel skapas en nätverks övervakare i regionen *östra* :

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Visa topologin med [AZ Network Watcher show-Topology](/cli/azure/network/watcher#az-network-watcher-show-topology). Följande exempel visar topologin för en resurs grupp med namnet *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Information om topologi returneras bara för resurser i samma resurs grupp som *MyResourceGroup* -resurs gruppen och samma region som nätverks bevakaren. Till exempel visas inte en nätverks säkerhets grupp som finns i en annan resurs grupp än *MyResourceGroup*, även om nätverks säkerhets gruppen är kopplad till ett undernät i det virtuella *MyVnet* -nätverket.

   Läs mer om relationer och [Egenskaper](#properties) i de utdata som returneras. Om du inte har ett befintligt virtuellt nätverk för att visa en topologi för, kan du skapa ett med hjälp av [väg trafiken via ett](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) skript exempel för virtuella nätverk. Använd [portalen](#azure-portal)för att visa ett diagram över topologin och ladda ned det i en redigerbar fil.

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Visa topologi – PowerShell

Du kan köra kommandona i de steg som följer:
- I Azure Cloud Shell, genom att välja **prova** överst till höger om ett kommando. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt där vanliga Azure-verktyg förinstalleras och konfigureras för användning med ditt konto.
- Genom att köra PowerShell från datorn. Om du kör PowerShell från datorn kräver den här artikeln Azure PowerShell- `Az` modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Kontot som du använder måste ha de [behörigheter](required-rbac-permissions.md)som krävs.

1. Om du redan har en nätverks övervakare i samma region som det virtuella nätverk som du vill skapa en topologi för går du vidare till steg 3. Skapa en resurs grupp som innehåller en nätverks övervakare med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas resurs gruppen i regionen *östra* :

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Skapa en nätverks övervakare med [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). I följande exempel skapas en nätverks övervakare i regionen Östra:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Hämta en Network Watcher-instans med [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). I följande exempel hämtas en nätverks övervakare i regionen USA, östra:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Hämta en topologi med [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). I följande exempel hämtas en topologi för ett virtuellt nätverk i resurs gruppen med namnet *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Information om topologi returneras bara för resurser i samma resurs grupp som *MyResourceGroup* -resurs gruppen och samma region som nätverks bevakaren. Till exempel visas inte en nätverks säkerhets grupp som finns i en annan resurs grupp än *MyResourceGroup*, även om nätverks säkerhets gruppen är kopplad till ett undernät i det virtuella *MyVnet* -nätverket.

   Läs mer om relationer och [Egenskaper](#properties) i de utdata som returneras. Om du inte har ett befintligt virtuellt nätverk för att visa en topologi för, kan du skapa ett med hjälp av [väg trafiken via ett](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) skript exempel för virtuella nätverk. Använd [portalen](#azure-portal)för att visa ett diagram över topologin och ladda ned det i en redigerbar fil.

## <a name="relationships"></a>Relationer

Alla resurser som returneras i en topologi har någon av följande typer av relationer till en annan resurs:

| Typ av relation | Exempel                                                                                                |
| ---               | ---                                                                                                    |
| Behållare       | Ett virtuellt nätverk innehåller ett undernät. Ett undernät innehåller ett nätverks gränssnitt.                            |
| Associerade        | Ett nätverks gränssnitt är associerat med en virtuell dator. En offentlig IP-adress är kopplad till ett nätverks gränssnitt. |

## <a name="properties"></a>Egenskaper

Alla resurser som returneras i en topologi har följande egenskaper:

- **Namn**: namnet på resursen
- **ID**: resursens URI.
- **Plats**: den Azure-region som resursen finns i.
- **Associationer**: en lista över associationer till det refererade objektet. Varje Association har följande egenskaper:
    - **AssociationType**: refererar till relationen mellan det underordnade objektet och det överordnade objektet. Giltiga värden är *contains* eller *associerade*.
    - **Namn**: namnet på den refererade resursen.
    - **ResourceID**:-URI för den resurs som refereras i associationen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [diagnostiserar ett problem med nätverks trafik filter till eller från en virtuell dator](diagnose-vm-network-traffic-filtering-problem.md) med hjälp av Network Watchers funktion för att verifiera IP-flöde
- Lär dig hur du [diagnostiserar ett problem med nätverks trafik dirigering från en virtuell dator](diagnose-vm-network-routing-problem.md) med hjälp av Network Watcher nästa hopp funktion
