---
title: 'Självstudie: Konfigurera väg filter för Microsoft-peering – Azure CLI'
description: I den här självstudien beskrivs hur du konfigurerar väg filter för Microsoft-peering med hjälp av Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91976002"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>Självstudie: Konfigurera väg filter för Microsoft-peering: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Flödesfilter är ett sätt att använda en delmängd av tjänster som stöds via Microsoft-peering. Stegen i den här artikeln hjälper dig att konfigurera och hantera väg filter för ExpressRoute-kretsar.

Microsoft 365 tjänster som Exchange Online, SharePoint Online och Skype för företag är tillgängliga via Microsoft-peering. När Microsoft-peering konfigureras i en ExpressRoute-krets annonseras alla prefix som är relaterade till dessa tjänster via de BGP-sessioner som etableras. Ett community-värde för BGP är kopplat till varje prefix för att identifiera vilken tjänst som erbjuds genom prefixet. En lista över värdena för BGP-communityn och de tjänster som de mappar till finns i [BGP-communities](expressroute-routing.md#bgp).

Anslutning till alla Azure-och Microsoft 365-tjänster gör att ett stort antal prefix annonseras via BGP. Det stora antalet prefix ökar avsevärt storleken på de väg tabeller som hanteras av routrarna i nätverket. Om du planerar att endast använda en delmängd av tjänster som erbjuds via Microsoft-peering kan du minska storleken på dina routningstabeller på två sätt. Du kan:

* Filtrera bort oönskade prefix genom att använda väg filter på BGP-communities. Flödes filtrering är en vanlig nätverks praxis och används ofta i många nätverk.

* Definiera väg filter och Använd dem i ExpressRoute-kretsen. Ett flödes filter är en ny resurs där du kan välja listan över tjänster som du planerar att använda via Microsoft-peering. ExpressRoute-routrar skickar bara listan över prefix som hör till de tjänster som identifieras i flödes filtret.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Hämta värden för BGP-communityn.
> - Skapa flödes filter och filter regel.
> - Koppla flödes filter till en ExpressRoute-krets.

### <a name="about-route-filters"></a><a name="about"></a>Om flödes filter

När Microsoft-peering konfigureras på din ExpressRoute-krets, upprättar Microsoft Edge-routrarna ett par med BGP-sessioner med dina gräns-routrar via anslutnings leverantören. Inga vägar annonseras till ditt nätverk. Om du vill aktivera vägannonseringar till ditt nätverk måste du associera ett flödesfilter.

Med ett flödesfilter kan du identifiera tjänster som du vill använda via Microsoft-peering för din ExpressRoute-krets. Det är i stort sett en tillåten lista över alla värden för BGP-communityn. När en väg filter resurs har definierats och kopplats till en ExpressRoute-krets annonseras alla prefix som mappar till BGP-communityns värden till nätverket.

Om du vill bifoga väg filter med Microsoft 365 tjänster måste du ha behörighet att använda Microsoft 365 tjänster via ExpressRoute. Om du inte har behörighet att använda Microsoft 365 tjänster via ExpressRoute Miss lyckas åtgärden att bifoga väg filter. Mer information om auktoriseringsprocessen finns i [Azure-ExpressRoute för Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänste prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen.
> 

## <a name="prerequisites"></a>Förutsättningar

För att kunna ansluta till tjänster via Microsoft-peering måste du utföra följande konfigurations steg:

* Du måste ha en aktiv ExpressRoute-krets som har Microsoft-peering etablerad. Du kan använda följande instruktioner för att utföra dessa uppgifter:
  * [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och låt den krets som aktive ras av anslutnings leverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillstånd som har tillhandahållits och Aktiver ATS.
  * [Skapa Microsoft-peering](howto-routing-cli.md) om du hanterar BGP-sessionen direkt. Du kan också använda din anslutnings leverantör för att etablera Microsoft-peering för din krets.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration

Logga in på ditt Azure-konto för att påbörja konfigurationen. Om du använder "testa det" är du inloggad automatiskt och kan hoppa över inloggnings steget. Använd följande exempel för att ansluta:

```azurecli
az login
```

Kontrollera prenumerationerna för kontot.

```azurecli-interactive
az account list
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets för.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Hämta en lista över prefix och värden för BGP-community

1. Använd följande cmdlet för att hämta listan över BGP community-värden och prefix som är kopplade till tjänster som är tillgängliga via Microsoft-peering:

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. Skapa en lista över värden för BGP-grupper som du vill använda i flödes filtret.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Skapa ett flödes filter och en filter regel

Ett flödes filter kan bara ha en regel och regeln måste vara av typen Allow. Den här regeln kan ha en lista över värden för BGP-communityn. Kommandot `az network route-filter create` skapar bara en Route filter-resurs. När du har skapat resursen måste du skapa en regel och koppla den till Route filter-objektet.

1. Om du vill skapa en Route filter-resurs kör du följande kommando:

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. Kör följande kommando för att skapa en flödes filter regel:
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Koppla väg filtret till en ExpressRoute-krets

Kör följande kommando för att koppla väg filtret till ExpressRoute-kretsen:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Vanliga åtgärder

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Hämta egenskaperna för ett flödes filter

Använd följande kommando för att hämta egenskaperna för ett flödes filter:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Så här uppdaterar du egenskaperna för ett flödes filter

Om flödes filtret redan är kopplat till en krets sprider uppdateringar till BGP community-listan automatiskt annonserings ändringar via den upprättade BGP-sessionen. Du kan uppdatera listan med BGP-grupper för ditt flödes filter med följande kommando:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Koppla bort ett flödes filter från en ExpressRoute-krets

När ett flödes filter kopplas från ExpressRoute-kretsen annonseras inga prefix via BGP-sessionen. Du kan koppla bort ett flödes filter från en ExpressRoute-krets med hjälp av följande kommando:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan bara ta bort ett flödes filter om det inte är kopplat till någon krets. Se till att flödes filtret inte är kopplat till någon krets innan du försöker ta bort det. Du kan ta bort ett flödes filter med följande kommando:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Information om konfigurations exempel för routern finns i:

> [!div class="nextstepaction"]
> [Konfigurations exempel för routern för att konfigurera och hantera routning](expressroute-config-samples-routing.md)
