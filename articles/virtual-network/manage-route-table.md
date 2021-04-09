---
title: Skapa, ändra eller ta bort en Azure route-tabell
titlesuffix: Azure Virtual Network
description: Lär dig var du hittar information om routning av virtuella nätverks trafik och hur du skapar, ändrar eller tar bort en routningstabell.
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
ms.openlocfilehash: 642a9a9f798492d85ee2a9784a1fe5ad4f854d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574120"
---
# <a name="create-change-or-delete-a-route-table"></a>Skapa, ändra eller ta bort en routningstabell

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures standardroutning gör du det genom att skapa en routningstabell. Om du inte har använt routning i virtuella nätverk kan du läsa mer om det i [routning av virtuella nätverks trafik](virtual-networks-udr-overview.md) eller genom att slutföra en [själv studie kurs](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har något konfigurerar du ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Slutför sedan en av de här uppgifterna innan du startar stegen i något avsnitt i den här artikeln:

- **Portal användare**: Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

- **PowerShell-användare**: kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. På fliken Azure Cloud Shell webbläsare letar du upp List rutan **Välj miljö** och väljer sedan **PowerShell** om den inte redan är markerad.

    Om du kör PowerShell lokalt använder du Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör också `Connect-AzAccount` för att skapa en anslutning till Azure.

- **Kommando rads gränssnitt för Azure (CLI)**: kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.31 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör också `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på eller ansluta till Azure med måste tilldelas [rollen nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="create-a-route-table"></a>Skapa en routningstabell

Det finns en gräns för hur många väg tabeller du kan skapa per Azure-plats och prenumeration. Mer information finns i [nätverks gränser – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

1. I rutan Sök anger du *routningstabellen*. När **routningstabellen** visas i Sök resultaten väljer du den.

1. På sidan **routningstabell** väljer du **skapa**.

1. I dialog rutan **skapa routningstabell** :

    1. Ange ett **namn** för routningstabellen.
    1. Välj din **prenumeration**.
    1. Välj en befintlig **resurs grupp** eller skapa en ny resurs grupp genom att välja **Skapa ny** .
    1. Välj en **plats**.
    1. Om du planerar att koppla routningstabellen till ett undernät i ett virtuellt nätverk som är anslutet till ditt lokala nätverk via en VPN-gateway och du inte vill sprida dina lokala vägar till nätverks gränssnitten i under nätet ställer du in **väg spridning för virtuell nätverks-Gateway** till **inaktive rad**.

1. Välj **skapa** för att skapa en ny routningstabell.

### <a name="create-route-table---commands"></a>Skapa routningstabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Visa routningstabeller

Gå till [Azure Portal](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **routningstabeller**. De routningstabeller som finns i din prenumeration visas.

### <a name="view-route-table---commands"></a>Visa väg tabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table List](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Visa information om en routningstabell

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **routningstabeller**.

1. I listan routningstabell väljer du den routningstabell som du vill visa information om.

1. På sidan väg tabell går du till **Inställningar** och visar **vägarna** i routningstabellen eller **under näten** som routningstabellen är kopplad till.

Mer information om vanliga Azure-inställningar finns i följande information:

- [Aktivitetslogg](../azure-monitor/essentials/platform-logs-overview.md)
- [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)
- [Taggar](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Lås](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automationsskript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Visa information om routningstabellen – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ nätverks väg-tabell show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Ändra en routningstabell

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **routningstabeller**.

1. I listan routningstabell väljer du den routningstabell som du vill ändra.

De vanligaste ändringarna är att [lägga till](#create-a-route) vägar, [ta bort](#delete-a-route) vägar, koppla [väg tabeller till undernät eller](#dissociate-a-route-table-from-a-subnet) [koppla](#associate-a-route-table-to-a-subnet) bort väg tabeller från undernät.

### <a name="change-a-route-table---commands"></a>Ändra en routningstabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ-nätverks väg – uppdatering av tabell](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Du kan också associera en routningstabell till ett undernät. En routningstabell kan kopplas till noll eller flera undernät. Eftersom routningstabeller inte är kopplade till virtuella nätverk måste du associera en routningstabell till varje undernät som du vill att routningstabellen ska associeras med. Azure dirigerar all trafik som lämnar under nätet baserat på vägar som du har skapat i routningstabeller, [standard vägar](virtual-networks-udr-overview.md#default)och vägar som har spridits från ett lokalt nätverk, om det virtuella nätverket är anslutet till en virtuell Azure-nätverksgateway (EXPRESSROUTE eller VPN). Du kan bara associera en routningstabell till undernät i virtuella nätverk som finns på samma Azure-plats och i samma prenumeration som routningstabellen.

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **virtuella nätverk**.

1. I listan virtuellt nätverk väljer du det virtuella nätverk som innehåller det undernät som du vill associera en routningstabell till.

1. I meny raden virtuellt nätverk väljer du **undernät**.

1. Välj det undernät som du vill koppla routningstabellen till.

1. I **routningstabell** väljer du den routningstabell som du vill koppla till under nätet.

1. Välj **Spara**.

Om det virtuella nätverket är anslutet till en Azure VPN-gateway ska du inte associera en routningstabell till [Gateway-undernätet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) som innehåller en väg med målet *0.0.0.0/0*. Om du gör det så förhindrar du gatewayen från att fungera korrekt. Mer information om hur du använder *0.0.0.0/0* i en väg finns i [trafik dirigering i virtuella nätverk](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Koppla en routningstabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Avassociera en routningstabell från ett undernät

När du kopplar bort en routningstabell från ett undernät dirigerar Azure trafik baserat på dess [standard vägar](virtual-networks-udr-overview.md#default).

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **virtuella nätverk**.

1. I listan virtuellt nätverk väljer du det virtuella nätverk som innehåller det undernät som du vill koppla från en routningstabell från.

1. I meny raden virtuellt nätverk väljer du **undernät**.

1. Välj det undernät som du vill koppla routningstabellen från.

1. I **routningstabellen** väljer du **ingen**.

1. Välj **Spara**.

### <a name="dissociate-a-route-table---commands"></a>Koppla bort en routningstabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Ta bort en routningstabell

Du kan inte ta bort en routningstabell som är kopplad till några undernät. [Koppla bort](#dissociate-a-route-table-from-a-subnet) en routningstabell från alla undernät innan du försöker ta bort den.

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina routningstabeller. Sök efter och välj **routningstabeller**.

1. I listan routningstabell väljer du den routningstabell som du vill ta bort.

1. Välj **ta bort** och välj sedan **Ja** i bekräftelse dialog rutan.

### <a name="delete-a-route-table---commands"></a>Ta bort en routningstabell – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ nätverks väg-ta bort tabell](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Skapa en väg

Det finns en gräns för hur många vägar per routningstabell som kan skapas per Azure-plats och prenumeration. Mer information finns i [nätverks gränser – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina routningstabeller. Sök efter och välj **routningstabeller**.

1. I listan routningstabell väljer du den routningstabell som du vill lägga till en väg till.

1. I meny raden i routningstabellen väljer du **flöden**  >  **Lägg till**.

1. Ange ett unikt **väg namn** för vägen i routningstabellen.

1. Ange adressprefixet **, i** CIDR-notation (Classless Inter-Domain routing), som du vill dirigera trafiken till. Prefixet kan inte dupliceras i fler än en väg i routningstabellen, även om prefixet kan vara inom ett annat prefix. Om du till exempel har definierat *10.0.0.0/16* som ett prefix i en väg kan du fortfarande definiera en annan väg med adressprefixet *10.0.0.0/22* . Azure väljer en väg för trafik baserat på den längsta prefix matchningen. Mer information finns i [hur Azure väljer en väg](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Välj en **nästa hopp typ**. Mer information om nästa hopp typer finns i [trafik dirigering i virtuella nätverk](virtual-networks-udr-overview.md).

1. Om du väljer en **nästa hopp typ** för **virtuell** installation anger du en IP-adress för **nästa hopp adress**.

1. Välj **OK**.

### <a name="create-a-route---commands"></a>Skapa en Route-kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Visa vägar

En routningstabell innehåller noll eller flera vägar. Mer information om den information som visas när du visar vägar finns i [trafik dirigering i virtuella nätverk](virtual-networks-udr-overview.md).

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina routningstabeller. Sök efter och välj **routningstabeller**.

1. I listan routningstabell väljer du den routningstabell som du vill visa vägar för.

1. I meny raden i routningstabellen väljer du **vägar** för att se listan över vägar.

### <a name="view-routes---commands"></a>Visa vägar – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ nätverks väg – tabell väg lista](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Visa information om en väg

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina routningstabeller. Sök efter och välj **routningstabeller**.

1. I listan routningstabell väljer du den routningstabell som innehåller den väg som du vill visa information om.

1. I meny raden i routningstabellen väljer du **vägar** för att se listan över vägar.

1. Välj den väg som du vill visa information om.

### <a name="view-details-of-a-route---commands"></a>Visa information om en Route-kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ nätverks väg – tabell väg show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Ändra en väg

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina routningstabeller. Sök efter och välj **routningstabeller**.

1. I listan routningstabell väljer du den routningstabell som innehåller den väg som du vill ändra.

1. I meny raden i routningstabellen väljer du **vägar** för att se listan över vägar.

1. Välj den väg som du vill ändra.

1. Ändra de befintliga inställningarna till de nya inställningarna och välj sedan **Spara**.

### <a name="change-a-route---commands"></a>Ändra en Route-kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ nätverks väg – uppdatering av tabell väg](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Ta bort en väg

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina routningstabeller. Sök efter och välj **routningstabeller**.

1. I listan routningstabell väljer du den routningstabell som innehåller den väg som du vill ta bort.

1. I meny raden i routningstabellen väljer du **vägar** för att se listan över vägar.

1. Välj den väg som du vill ta bort.

1. Välj **ta bort** och välj sedan **Ja** i bekräftelse dialog rutan.

### <a name="delete-a-route---commands"></a>Ta bort ett Route-kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Route Delete](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Visa effektiva vägar

De effektiva vägarna för varje VM-kopplat nätverks gränssnitt är en kombination av väg tabeller som du har skapat, Azures standard vägar och alla vägar som sprids från lokala nätverk via Border Gateway Protocol (BGP) via en virtuell Azure-nätverksgateway. Att förstå de effektiva vägarna för ett nätverks gränssnitt är till hjälp vid fel sökning av problem med Routning. Du kan visa effektiva vägar för alla nätverks gränssnitt som är kopplade till en virtuell dator som körs.

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina virtuella datorer. Sök efter och välj **virtuella datorer**.

1. I listan virtuell dator väljer du den virtuella dator som du vill visa effektiva vägar för.

1. Välj **nätverk** i meny raden VM.

1. Välj ett nätverks gränssnitts namn.

1. I meny raden för nätverks gränssnittet väljer du **effektiva vägar**.

1. Granska listan över effektiva vägar för att se om rätt väg finns för den plats där du vill dirigera trafiken till. Lär dig mer om nästa hopp typer som visas i den här listan i [trafik dirigering i virtuella nätverk](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Visa effektiva vägar – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network NIC show-effektiv-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Verifiera routning mellan två slut punkter

Du kan fastställa nästa hopp typ mellan en virtuell dator och IP-adressen för en annan Azure-resurs, en lokal resurs eller en resurs på Internet. Att fastställa Azures routning är användbart när du felsöker problem med Routning. Du måste ha en befintlig nätverks Övervakare för att kunna slutföra den här uppgiften. Om du inte har en befintlig nätverks övervakare kan du skapa en genom att följa stegen i [skapa en Network Watcher-instans](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina nätverks övervakare. Sök efter och välj **Network Watcher**.

1. I meny raden för Network Watcher väljer du **nästa hopp**.

1. I **Network Watcher | Nästa hopp** -sida:

    1. Välj din **prenumeration** och **resurs gruppen** för den virtuella käll datorn som du vill validera routning från.

    1. Välj den **virtuella datorn** och **nätverks gränssnittet** som är kopplat till den virtuella datorn.
    
    1. Ange en **käll-IP-adress** som tilldelats det nätverks gränssnitt som du vill validera routning från.

    1. Ange en **mål-IP-adress** som du vill validera routning till.

1. Välj **nästa hopp**.

Efter en kort väntan meddelar Azure dig nästa hopp typ och ID för den väg som dirigerade trafiken. Läs mer om de typer av nästa hopp som visas i [routning av virtuella nätverks trafik](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Verifiera routning mellan två slut punkter – kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [AZ Network Watcher show-Next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i routningstabeller och vägar måste ditt konto tilldelas [rollen nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder i följande tabell:

| Action                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft. Network/routeTables/Read                              |   Läsa en routningstabell                                    |
| Microsoft. Network/routeTables/Write                             |   Skapa eller uppdatera en routningstabell                        |
| Microsoft. Network/routeTables/Delete                            |   Ta bort en routningstabell                                  |
| Microsoft. Network/routeTables/JOIN/åtgärd                       |   Associera en routningstabell till ett undernät                   |
| Microsoft. Network/routeTables/routes/Read                       |   Läs en väg                                          |
| Microsoft. Network/routeTables/routes/Write                      |   Skapa eller uppdatera en väg                              |
| Microsoft. Network/routeTables/routes/Delete                     |   Ta bort en väg                                        |
| Microsoft. Network/networkInterfaces/effectiveRouteTable/Action  |   Hämta den effektiva routningstabellen för ett nätverks gränssnitt |
| Microsoft. Network/networkWatchers/nextHop/Action                |   Hämtar nästa hopp från en virtuell dator                           |

## <a name="next-steps"></a>Nästa steg

- Skapa en routningstabell med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tilldela [Azure policy definitioner](./policy-reference.md) för virtuella nätverk