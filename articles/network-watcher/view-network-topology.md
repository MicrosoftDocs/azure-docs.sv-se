---
title: Visa topologi för virtuella Azure-| Microsoft Docs
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
ms.openlocfilehash: f20fa22dac3fba4d01cbc5e398bafa4113e94a96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780307"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Visa topologin för ett virtuellt Azure-nätverk

I den här artikeln får du lära dig hur du visar resurser Microsoft Azure ett virtuellt nätverk och relationerna mellan resurserna. Ett virtuellt nätverk innehåller till exempel undernät. Undernät innehåller resurser, till exempel Azure Virtual Machines (VM). Virtuella datorer har ett eller flera nätverksgränssnitt. Varje undernät kan ha en nätverkssäkerhetsgrupp och en associerad vägtabell. Med topologifunktionerna i Azure Network Watcher kan du visa alla resurser i ett virtuellt nätverk, de resurser som är kopplade till resurser i ett virtuellt nätverk och relationerna mellan resurserna.

Du kan använda [Azure Portal,](#azure-portal)Azure [CLI eller](#azure-cli) [PowerShell för](#powershell) att visa en topologi.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Visa topologi – Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som har de behörigheter som [krävs.](required-rbac-permissions.md)
2. Längst upp till vänster i portalen väljer du **Alla tjänster.**
3. I **filterrutan Alla** tjänster anger du *Network Watcher*. När **Network Watcher** visas i resultatet väljer du det.
4. Välj **Topologi**. För att generera en topologi krävs en nätverksserare i samma region som det virtuella nätverk som du vill generera topologin för finns i. Om du inte har en aktiverad nätverksserare i den region som det virtuella nätverk som du vill generera en topologi för finns i, skapas nätverksserare automatiskt åt dig i alla regioner. Nätverkshanteraren skapas i en resursgrupp med namnet **NetworkWatcherRG.**
5. Välj en prenumeration, resursgruppen för ett virtuellt nätverk som du vill visa topologin för och välj sedan det virtuella nätverket. I följande bild visas en topologi för ett virtuellt nätverk med namnet *MyVnet* i resursgruppen med namnet *MyResourceGroup*:

    ![Visa topologi](./media/view-network-topology/view-topology.png)

    Som du ser i föregående bild innehåller det virtuella nätverket tre undernät. Ett undernät har en virtuell dator distribuerad. Den virtuella datorn har ett nätverksgränssnitt kopplat till sig och en offentlig IP-adress som är kopplad till den. De andra två undernäten har en associerad vägtabell. Varje vägtabell innehåller två vägar. Ett undernät har en associerad nätverkssäkerhetsgrupp. Topologiinformation visas endast för resurser som är:
    
    - Inom samma resursgrupp och region som det *virtuella nätverket myVnet.* En nätverkssäkerhetsgrupp som finns i en annan resursgrupp än *MyResourceGroup* visas till exempel inte, även om nätverkssäkerhetsgruppen är associerad med ett undernät i det virtuella *nätverket MyVnet.*
    - Inom, eller associerat till resurser i, det *virtuella nätverket myVnet.* Till exempel visas inte en nätverkssäkerhetsgrupp som inte är associerad med ett undernät eller nätverksgränssnitt i det *virtuella nätverket myVnet,* även om nätverkssäkerhetsgruppen finns i resursgruppen *MyResourceGroup.*

   Topologin som visas på bilden är för det virtuella nätverk som skapas efter distribution av skriptexempel för Dirigera trafik via en virtuell nätverksinstallation, som du kan distribuera med hjälp av [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)eller [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Välj **Ladda ned topologi** för att ladda ned bilden som en redigerbar fil i svg-format.

Resurserna som visas i diagrammet är en delmängd av nätverkskomponenterna i det virtuella nätverket. När till exempel en nätverkssäkerhetsgrupp visas visas inte säkerhetsreglerna i den i diagrammet. Även om de inte skiljer sig åt i diagrammet representerar linjerna en av två relationer: *Inneslutning* eller *associerad*. Om du vill se en fullständig lista över resurser i det virtuella nätverket och typen av relation mellan resurserna genererar du topologin med [PowerShell](#powershell) eller [Azure CLI.](#azure-cli)

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Visa topologi – Azure CLI

Du kan köra kommandona i följande steg:
- I Azure Cloud Shell du Prova **längst upp** till höger i ett kommando. Den Azure Cloud Shell är ett kostnadsfritt interaktivt gränssnitt som har vanliga Azure-verktyg förinstallerade och konfigurerade för användning med ditt konto.
- Genom att köra CLI från datorn. Om du kör CLI från datorn kräver stegen i den här artikeln Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra för `az login` att skapa en anslutning till Azure.

Det konto som du använder måste ha de behörigheter som [krävs.](required-rbac-permissions.md)

1. Om du redan har en nätverks bevakare i samma region som det virtuella nätverk som du vill skapa en topologi för går du vidare till steg 3. Skapa en resursgrupp som ska innehålla en network watcher [med az group create](/cli/azure/group). I följande exempel skapas resursgruppen i *regionen eastus:*

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Skapa en network watcher med [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure). I följande exempel skapas en nätverks bevakare i *regionen eastus:*

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Visa topologin med [az network watcher show-topology](/cli/azure/network/watcher#az_network_watcher_show_topology). I följande exempel visas topologin för en resursgrupp med namnet *MyResourceGroup:*

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topologiinformation returneras endast för resurser som finns i samma resursgrupp som *resursgruppen MyResourceGroup* och samma region som network watcher. En nätverkssäkerhetsgrupp som finns i en annan resursgrupp än *MyResourceGroup* visas till exempel inte, även om nätverkssäkerhetsgruppen är associerad med ett undernät i det virtuella *nätverket MyVnet.*

   Läs mer om relationer och egenskaper [i](#properties) returnerade utdata. Om du inte har något befintligt virtuellt nätverk att visa en topologi för kan du skapa ett med skriptexempel för Dirigera trafik via en [virtuell](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) nätverksinstallation. Om du vill visa ett diagram över topologin och ladda ned den i en redigerbar fil använder du [portalen](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Visa topologi – PowerShell

Du kan köra kommandona i följande steg:
- I den Azure Cloud Shell du prova **det längst** upp till höger i ett kommando. Det Azure Cloud Shell är ett kostnadsfritt interaktivt gränssnitt som har vanliga Azure-verktyg förinstallerade och konfigurerade för användning med ditt konto.
- Genom att köra PowerShell från datorn. Om du kör PowerShell från datorn kräver den här artikeln Azure PowerShell `Az` modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Det konto som du använder måste ha de behörigheter som [krävs.](required-rbac-permissions.md)

1. Om du redan har en nätverks bevakare i samma region som det virtuella nätverk som du vill skapa en topologi för går du vidare till steg 3. Skapa en resursgrupp som ska innehålla en network watcher [med New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas resursgruppen i *regionen eastus:*

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Skapa en nätverks watcher med [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). I följande exempel skapas en nätverks bevakare i regionen eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Hämta en Network Watcher instans med [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). I följande exempel hämtas en nätverks bevakare i regionen USA, östra:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Hämta en topologi med [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). I följande exempel hämtas en topologi för ett virtuellt nätverk i resursgruppen med namnet *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topologiinformation returneras endast för resurser som finns i samma resursgrupp som *resursgruppen MyResourceGroup* och samma region som network watcher. Till exempel visas inte en nätverkssäkerhetsgrupp som finns i en annan resursgrupp än *MyResourceGroup,* även om nätverkssäkerhetsgruppen är associerad med ett undernät i det *virtuella nätverket MyVnet.*

   Läs mer om relationer och egenskaper [i](#properties) returnerade utdata. Om du inte har något befintligt virtuellt nätverk att visa en topologi för kan du skapa ett med hjälp av skriptexempelskriptet Dirigera trafik [via](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en virtuell nätverksinstallation. Om du vill visa ett diagram över topologin och ladda ned det i en redigerbar fil använder du [portalen](#azure-portal).

## <a name="relationships"></a>Relationer

Alla resurser som returneras i en topologi har någon av följande typer av relationer till en annan resurs:

| Typ av relation | Exempel                                                                                                |
| ---               | ---                                                                                                    |
| Behållare       | Ett virtuellt nätverk innehåller ett undernät. Ett undernät innehåller ett nätverksgränssnitt.                            |
| Associerade        | Ett nätverksgränssnitt är associerat med en virtuell dator. En offentlig IP-adress är associerad med ett nätverksgränssnitt. |

## <a name="properties"></a>Egenskaper

Alla resurser som returneras i en topologi har följande egenskaper:

- **Namn:** Namnet på resursen
- **Id:** Resursens URI.
- **Plats:** Den Azure-region som resursen finns i.
- **Associationer:** En lista med associationer till det refererade objektet. Varje association har följande egenskaper:
    - **AssociationType:** Refererar till relationen mellan det underordnade objektet och det överordnade objektet. Giltiga värden är *Contains* eller *Associated*.
    - **Namn:** Namnet på den refererade resursen.
    - **ResourceId**: – URI:en för resursen som refereras till i associationen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [du diagnostiserar problem med filtrering av nätverkstrafik till](diagnose-vm-network-traffic-filtering-problem.md) eller från en virtuell Network Watcher med hjälp av funktionen kontrollera IP-flöde
- Lär dig hur [du diagnostiserar problem med routning av nätverkstrafik](diagnose-vm-network-routing-problem.md) från en Network Watcher med hjälp av funktionen för nästa hopp
