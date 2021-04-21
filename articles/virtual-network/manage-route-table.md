---
title: Skapa, ändra eller ta bort en Azure-vägtabell
titlesuffix: Azure Virtual Network
description: Lär dig var du hittar information om trafikdirigering i virtuella nätverk och hur du skapar, ändrar eller tar bort en routningstabell.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: cdf702abb10b7330a4ca0f5478751df4bce3d7f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783385"
---
# <a name="create-change-or-delete-a-route-table"></a>Skapa, ändra eller ta bort en routningstabell

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures standardroutning gör du det genom att skapa en routningstabell. Om routning i virtuella nätverk är nytt för dig [](virtual-networks-udr-overview.md) kan du lära dig mer om det i trafikdirigering i virtuella nätverk eller genom att slutföra en [självstudiekurs.](tutorial-create-route-table-portal.md)

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har ett konto kan du konfigurera ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Utför sedan någon av dessa uppgifter innan du påbörjar stegen i något avsnitt i den här artikeln:

- **Portalanvändare:** Logga in på [Azure Portal](https://portal.azure.com) ditt Azure-konto.

- **PowerShell-användare:** Kör antingen [kommandona i Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. På Azure Cloud Shell webbläsarflik hittar du  listrutan Välj miljö och väljer **sedan PowerShell** om det inte redan är markerat.

    Om du kör PowerShell lokalt använder du Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör även `Connect-AzAccount` för att skapa en anslutning till Azure.

- **Azure CLI-användare (Command-Line Interface):** Kör antingen kommandona [i Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.31 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör även `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på eller [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ansluter till Azure [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) med måste tilldelas rollen Nätverksdeltagare eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i [Behörigheter](#permissions).

## <a name="create-a-route-table"></a>Skapa en routningstabell

Det finns en gräns för hur många vägtabeller du kan skapa per Azure-plats och prenumeration. Mer information finns i [Nätverksgränser – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

1. I sökrutan anger du *Route-tabellen*. När **route-tabellen** visas i sökresultatet väljer du den.

1. På sidan **Route table** (Vägtabell) väljer du **Create (Skapa).**

1. I **dialogrutan Skapa vägtabell:**

    1. Ange ett **Namn** för vägtabellen.
    1. Välj din **prenumeration**.
    1. Välj en befintlig **resursgrupp eller** välj **Skapa ny för** att skapa en ny resursgrupp.
    1. Välj en **plats**.
    1. Om du planerar att associera vägtabellen med ett undernät i ett virtuellt nätverk som är anslutet till ditt lokala nätverk via en VPN-gateway och du inte vill sprida dina lokala vägar till nätverksgränssnitten i undernätet anger du Spridning av virtuell **nätverksgateway** till **Inaktiverad.**

1. Välj **Skapa** för att skapa din nya vägtabell.

### <a name="create-route-table---commands"></a>Skapa vägtabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Visa vägtabeller

Gå till Azure Portal [för](https://portal.azure.com) att hantera ditt virtuella nätverk. Sök efter och välj **Route tables (Dirigera tabeller).** De vägtabeller som finns i din prenumeration visas.

### <a name="view-route-table---commands"></a>Visa vägtabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table list](/cli/azure/network/route-table#az_network_route_table_list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Visa information om en vägtabell

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera ditt virtuella nätverk. Sök efter och välj **Route tables (Dirigera tabeller).**

1. I listan med vägtabeller väljer du den vägtabell som du vill visa information om.

1. På sidan för vägtabellen går  du **till Inställningar** och visar vägarna i vägtabellen eller de undernät **som** vägtabellen är associerad med.

Mer information om vanliga Azure-inställningar finns i följande information:

- [Aktivitetslogg](../azure-monitor/essentials/platform-logs-overview.md)
- [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
- [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automationsskript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Visa information om vägtabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table show](/cli/azure/network/route-table#az_network_route_table_show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Ändra en vägtabell

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera ditt virtuella nätverk. Sök efter och välj **Route tables (Dirigera tabeller).**

1. I listan med vägtabeller väljer du den vägtabell som du vill ändra.

De vanligaste ändringarna är att [lägga till](#create-a-route) [vägar,](#delete-a-route) ta bort [vägar,](#associate-a-route-table-to-a-subnet) associera vägtabeller till undernät eller koppla [bort vägtabeller](#dissociate-a-route-table-from-a-subnet) från undernät.

### <a name="change-a-route-table---commands"></a>Ändra en vägtabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table update](/cli/azure/network/route-table#az_network_route_table_update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Om du vill kan du associera en vägtabell med ett undernät. En vägtabell kan associeras med noll eller flera undernät. Eftersom vägtabeller inte är associerade med virtuella nätverk måste du associera en vägtabell till varje undernät som du vill att vägtabellen ska associeras till. Azure dirigerar all trafik som lämnar undernätet baserat på [](virtual-networks-udr-overview.md#default)vägar som du har skapat i vägtabeller, standardvägar och vägar som sprids från ett lokalt nätverk, om det virtuella nätverket är anslutet till en virtuell Nätverksgateway i Azure (ExpressRoute eller VPN). Du kan bara associera en routningstabell till undernät i virtuella nätverk som finns på samma Azure-plats och i samma prenumeration som routningstabellen.

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera ditt virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

1. I listan över virtuella nätverk väljer du det virtuella nätverk som innehåller det undernät som du vill associera en vägtabell med.

1. I menyraden för virtuellt nätverk väljer **du Undernät.**

1. Välj det undernät som du vill associera vägtabellen med.

1. I **Route table**(Vägtabell) väljer du den vägtabell som du vill associera till undernätet.

1. Välj **Spara**.

Om ditt virtuella nätverk är anslutet till en Azure VPN-gateway ska du inte associera en vägtabell till [gatewayundernätet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) som innehåller en väg med målet *0.0.0.0/0.* Om du gör det så förhindrar du gatewayen från att fungera korrekt. Mer information om hur du använder *0.0.0.0/0* i en väg finns i [Trafikdirigering i virtuella nätverk.](virtual-networks-udr-overview.md#default-route)

### <a name="associate-a-route-table---commands"></a>Associera en vägtabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Avassociera en routningstabell från ett undernät

När du separerar en vägtabell från ett undernät dirigerar Azure trafik baserat på dess [standardvägar.](virtual-networks-udr-overview.md#default)

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera ditt virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

1. I listan över virtuella nätverk väljer du det virtuella nätverk som innehåller det undernät som du vill ta bort en vägtabell från.

1. I menyraden för virtuellt nätverk väljer **du Undernät.**

1. Välj det undernät som du vill ta bort vägens tabell från.

1. I **tabellen Route** väljer du **Ingen.**

1. Välj **Spara**.

### <a name="dissociate-a-route-table---commands"></a>Ta bort en vägtabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Ta bort en routningstabell

Du kan inte ta bort en vägtabell som är associerad med några undernät. [Koppla bort](#dissociate-a-route-table-from-a-subnet) en routningstabell från alla undernät innan du försöker ta bort den.

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera dina vägtabeller. Sök efter och välj **Route tables (Dirigera tabeller).**

1. I listan med vägtabeller väljer du den vägtabell som du vill ta bort.

1. Välj **Ta** bort och välj **sedan Ja** i bekräftelsedialogrutan.

### <a name="delete-a-route-table---commands"></a>Ta bort en vägtabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table delete](/cli/azure/network/route-table#az_network_route_table_delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Skapa en väg

Det finns en gräns för hur många vägar per vägtabell som kan skapas per Azure-plats och prenumeration. Mer information finns i [Nätverksgränser – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera dina vägtabeller. Sök efter och välj **Route tables (Dirigera tabeller).**

1. I listan med vägtabeller väljer du den vägtabell som du vill lägga till en väg till.

1. Från menyraden för vägtabellen väljer du Vägar **Lägg**  >  **till**.

1. Ange ett unikt **vägnamn** för vägen i vägtabellen.

1. Ange **adressprefixet** i CIDR-notationen (Classless Inter-Domain Routing) som du vill dirigera trafik till. Prefixet kan inte dupliceras i mer än en väg i vägtabellen, även om prefixet kan finnas i ett annat prefix. Om du till exempel har definierat *10.0.0.0/16* som ett prefix i en väg kan du fortfarande definiera en annan väg med adressprefixet *10.0.0.0/22.* Azure väljer en väg för trafik baserat på den längsta prefixmatchningen. Mer information finns i [Hur Azure väljer en väg.](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. Välj en **Nästa hopptyp.** Mer information om nästa hopptyper finns i [Trafikdirigering i virtuella nätverk.](virtual-networks-udr-overview.md)

1. Om du väljer nästa **hopptypen Virtuell installation** **anger du** en IP-adress för **Nästa hopp-adress.**

1. Välj **OK**.

### <a name="create-a-route---commands"></a>Skapa en väg – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Visa vägar

En vägtabell innehåller noll eller flera vägar. Mer information om den information som visas när du visar vägar finns i [Trafikdirigering i virtuella nätverk.](virtual-networks-udr-overview.md)

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera dina vägtabeller. Sök efter och välj **Route tables (Dirigera tabeller).**

1. I listan med vägtabeller väljer du den vägtabell som du vill visa vägar för.

1. I menyraden för vägtabellen väljer **du Vägar** för att se listan över vägar.

### <a name="view-routes---commands"></a>Visa vägar – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table route list](/cli/azure/network/route-table/route#az_network_route_table_route_list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Visa information om en väg

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera dina vägtabeller. Sök efter och välj **Route tables (Dirigera tabeller).**

1. I listan med vägtabeller väljer du den vägtabell som innehåller den väg som du vill visa information om.

1. I menyraden för vägtabellen väljer **du Vägar** för att se listan över vägar.

1. Välj den väg som du vill visa information om.

### <a name="view-details-of-a-route---commands"></a>Visa information om en väg – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table route show](/cli/azure/network/route-table/route#az_network_route_table_route_show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Ändra en väg

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera dina vägtabeller. Sök efter och välj **Route tables (Dirigera tabeller).**

1. I listan med vägtabeller väljer du den vägtabell som innehåller den väg som du vill ändra.

1. I menyraden för vägtabellen väljer **du Vägar** för att se listan över vägar.

1. Välj den väg som du vill ändra.

1. Ändra befintliga inställningar till sina nya inställningar och välj sedan **Spara.**

### <a name="change-a-route---commands"></a>Ändra en väg – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table route update](/cli/azure/network/route-table/route#az_network_route_table_route_update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Ta bort en väg

1. Gå till Azure Portal [för](https://portal.azure.com) att hantera dina vägtabeller. Sök efter och välj **Route tables (Dirigera tabeller).**

1. I listan med vägtabeller väljer du den vägtabell som innehåller den väg som du vill ta bort.

1. I menyraden för vägtabellen väljer **du Vägar** för att se listan över vägar.

1. Välj den väg som du vill ta bort.

1. Välj **Ta** bort och välj **sedan** Ja i bekräftelsedialogrutan.

### <a name="delete-a-route---commands"></a>Ta bort en väg – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table route delete](/cli/azure/network/route-table/route#az_network_route_table_route_delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Visa effektiva vägar

De effektiva vägarna för varje VM-kopplat nätverksgränssnitt är en kombination av vägtabeller som du har skapat, Azures standardvägar och alla vägar som sprids från lokala nätverk via Border Gateway Protocol (BGP) via en virtuell Nätverksgateway i Azure. Att förstå de effektiva vägarna för ett nätverksgränssnitt är användbart när du felsöker routningsproblem. Du kan visa de gällande vägarna för alla nätverksgränssnitt som är anslutna till en virtuell dator som körs.

1. Gå till [Azure Portal för](https://portal.azure.com) att hantera dina virtuella datorer. Sök efter och välj **Virtuella datorer.**

1. I listan över virtuella datorer väljer du den virtuella dator som du vill visa gällande vägar för.

1. I menyraden för virtuella datorer väljer du **Nätverk.**

1. Välj namnet på ett nätverksgränssnitt.

1. I menyraden för nätverksgränssnittet väljer du **Gällande vägar.**

1. Granska listan över effektiva vägar för att se om rätt väg finns för den plats där du vill dirigera trafik. Läs mer om nästa hopptyper som du ser i den här listan i [Trafikdirigering för virtuella nätverk.](virtual-networks-udr-overview.md)

### <a name="view-effective-routes---commands"></a>Visa effektiva vägar – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Verifiera routning mellan två slutpunkter

Du kan bestämma nästa hopptyp mellan en virtuell dator och IP-adressen för en annan Azure-resurs, en lokal resurs eller en resurs på Internet. Att fastställa Azures routning är användbart när du felsöker routningsproblem. För att slutföra den här uppgiften måste du ha en befintlig network watcher. Om du inte har en befintlig network watcher skapar du en genom att slutföra stegen i [Skapa en Network Watcher instans](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Gå till den [Azure Portal för](https://portal.azure.com) att hantera dina nätverksserare. Sök efter och välj **Network Watcher**.

1. Välj Nästa hopp på menyraden i **network watcher.**

1. I **Network Watcher | Nästa hoppsida:**

    1. Välj din **prenumeration** och **resursgruppen för den** virtuella källdatorn som du vill verifiera routningen från.

    1. Välj den **virtuella datorn** och **nätverksgränssnittet** som är kopplat till den virtuella datorn.
    
    1. Ange en **KÄLL-IP-adress** som tilldelats till nätverksgränssnittet som du vill verifiera routningen från.

    1. Ange en **mål-IP-adress** som du vill verifiera routningen till.

1. Välj **Nästa hopp.**

Efter en kort stund meddelar Azure dig nästa hopptyp och ID för den väg som dirigerade trafiken. Läs mer om nästa hopptyper som returneras i [Trafikdirigering i virtuella nätverk.](virtual-networks-udr-overview.md)

### <a name="validate-routing-between-two-endpoints---commands"></a>Verifiera routning mellan två slutpunkter – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter på vägtabeller [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) och vägar måste [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ditt konto tilldelas till rollen Nätverksdeltagare eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i följande tabell:

| Action                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Läsa en vägtabell                                    |
| Microsoft.Network/routeTables/write                             |   Skapa eller uppdatera en vägtabell                        |
| Microsoft.Network/routeTables/delete                            |   Ta bort en routningstabell                                  |
| Microsoft.Network/routeTables/join/action                       |   Associera en routningstabell till ett undernät                   |
| Microsoft.Network/routeTables/routes/read                       |   Läsa en väg                                          |
| Microsoft.Network/routeTables/routes/write                      |   Skapa eller uppdatera en väg                              |
| Microsoft.Network/routeTables/routes/delete                     |   Ta bort en väg                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Hämta den effektiva vägtabellen för ett nätverksgränssnitt |
| Microsoft.Network/networkWatchers/nextHop/action                |   Hämtar nästa hopp från en virtuell dator                           |

## <a name="next-steps"></a>Nästa steg

- Skapa en vägtabell med [powershell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller Azure [Resource Manager mallar](template-samples.md)
- Skapa och tilldela [Azure Policy definitioner](./policy-reference.md) för virtuella nätverk