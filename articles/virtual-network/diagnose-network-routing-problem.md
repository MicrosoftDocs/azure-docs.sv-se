---
title: Diagnostisera problem med routning av virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du diagnostiserar problem med routning av virtuella datorer genom att visa de effektiva vägarna för en virtuell dator.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: f84b74b054a073f2c1ae5ba2ac7d0d0a968367c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767681"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostisera problem med routning av virtuella datorer

I den här artikeln får du lära dig hur du diagnostiserar ett routningsproblem genom att visa de vägar som är effektiva för ett nätverksgränssnitt på en virtuell dator (VM). Azure skapar flera standardvägar för varje undernät i det virtuella nätverket. Du kan åsidosätta Azures standardvägar genom att definiera vägar i en vägtabell och sedan associera vägtabellen till ett undernät. Kombinationen av vägar som du skapar, Azures standardvägar och alla vägar som sprids från ditt lokala nätverk via en Azure VPN-gateway (om ditt virtuella nätverk är anslutet till ditt lokala nätverk) via BGP (Border Gateway Protocol) är de effektiva vägarna för alla nätverksgränssnitt i ett undernät. Om du inte är bekant med virtuella nätverk, nätverksgränssnitt eller routningsbegrepp kan du gå till Översikt över virtuellt [nätverk,](virtual-networks-overview.md) [Nätverksgränssnitt](virtual-network-network-interface.md)och [Routningsöversikt.](virtual-networks-udr-overview.md)

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator, men anslutningen misslyckas. Om du vill ta reda på varför du inte kan ansluta till den virtuella datorn kan du visa de effektiva vägarna för ett nätverksgränssnitt med [hjälp](#diagnose-using-azure-portal)av [Azure-portalen, PowerShell](#diagnose-using-powershell)eller [Azure CLI.](#diagnose-using-azure-cli)

Stegen nedan förutsätter att du har en befintlig virtuell dator som du kan visa de gällande vägarna för. Om du inte har en befintlig virtuell dator distribuerar du först en virtuell [Linux-](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows-dator](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att slutföra uppgifterna i den här artikeln med. Exemplen i den här artikeln gäller för en virtuell dator med *namnet myVM* med ett nätverksgränssnitt med namnet *myVMNic1.* Den virtuella datorn och nätverksgränssnittet finns i en resursgrupp med namnet *myResourceGroup* och finns i regionen *USA,* östra. Ändra värdena i stegen efter behov för den virtuella dator som du diagnostiserar problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Azure-konto som har [de behörigheter som krävs.](virtual-network-network-interface.md#permissions)
2. Längst upp i Azure Portal anger du namnet på en virtuell dator som körs i sökrutan. När namnet på den virtuella datorn visas i sökresultatet väljer du det.
3. Under **Inställningar** till vänster väljer du **Nätverk** och går till nätverksgränssnittsresursen genom att välja dess namn.
     ![Visa nätverksgränssnitt](./media/diagnose-network-routing-problem/view-nics.png)
4. Till vänster väljer du **Gällande vägar.** De effektiva vägarna för ett nätverksgränssnitt med **namnet myVMNic1** visas i följande bild: ![ Visa effektiva vägar](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Om det finns flera nätverksgränssnitt kopplade till den virtuella datorn kan du visa de gällande vägarna för alla nätverksgränssnitt genom att välja det. Eftersom varje nätverksgränssnitt kan finnas i olika undernät kan varje nätverksgränssnitt ha olika effektiva vägar.

    I exemplet som visas i föregående bild är de listade vägarna standardvägar som Azure skapar för varje undernät. Listan har minst dessa vägar, men kan ha ytterligare vägar, beroende på vilka funktioner du har aktiverat för ditt virtuella nätverk, till exempel att det är peerkopplat med ett annat virtuellt nätverk eller anslutet till ditt lokala nätverk via en Azure VPN-gateway. Mer information om var och en av vägarna och andra vägar som du kan se för nätverksgränssnittet finns i [Trafikroutning för virtuellt nätverk.](virtual-networks-udr-overview.md) Om listan har ett stort antal vägar kan det vara enklare att välja **Ladda** ned för att ladda ned en CSV-fil med listan över vägar.

Även om effektiva vägar visades via den virtuella datorn i föregående steg kan du också visa effektiva vägar via en:
- **Enskilt nätverksgränssnitt:** Lär dig hur du [visar ett nätverksgränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **Enskild vägtabell:** Lär dig hur du [visar en vägtabell](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnostisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Det Azure Cloud Shell är ett kostnadsfritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra för att logga `Connect-AzAccount` in på Azure med ett konto som har de behörigheter som [krävs.](virtual-network-network-interface.md#permissions)

Hämta de effektiva vägarna för ett nätverksgränssnitt med [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). I följande exempel hämtar de effektiva vägarna för ett nätverksgränssnitt med namnet *myVMNic1*, som finns i en resursgrupp med namnet *myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Information om informationen som returneras i utdata finns i [Routningsöversikt.](virtual-networks-udr-overview.md) Utdata returneras endast om den virtuella datorn är i körningstillstånd. Om det finns flera nätverksgränssnitt kopplade till den virtuella datorn kan du granska de gällande vägarna för varje nätverksgränssnitt. Eftersom varje nätverksgränssnitt kan finnas i olika undernät kan varje nätverksgränssnitt ha olika effektiva vägar. Om du fortfarande har kommunikationsproblem kan du se ytterligare [diagnos och](#additional-diagnosis) [överväganden.](#considerations)

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella dator som nätverksgränssnittet är kopplat till, returnerar följande kommandon DED:erna för alla nätverksgränssnitt som är anslutna till en virtuell dator:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Du får utdata som liknar följande exempel:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

I föregående utdata är nätverksgränssnittsnamnet *myVMNic1.*

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med Hjälp av Azure CLI

Du kan köra kommandona som följer i  [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra och logga in på `az login` Azure med ett konto som har de behörigheter som [krävs.](virtual-network-network-interface.md#permissions)

Hämta de effektiva vägarna för ett nätverksgränssnitt [med az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table). I följande exempel hämtar de effektiva vägarna för ett nätverksgränssnitt med namnet *myVMNic1* som finns i en resursgrupp med namnet *myResourceGroup:*

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Information om informationen som returneras i utdata finns i [Routningsöversikt.](virtual-networks-udr-overview.md) Utdata returneras endast om den virtuella datorn är i körningstillstånd. Om det finns flera nätverksgränssnitt kopplade till den virtuella datorn kan du granska de gällande vägarna för varje nätverksgränssnitt. Eftersom varje nätverksgränssnitt kan finnas i olika undernät kan varje nätverksgränssnitt ha olika effektiva vägar. Om du fortfarande har kommunikationsproblem kan du se ytterligare [diagnos och](#additional-diagnosis) [överväganden.](#considerations)

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella dator som nätverksgränssnittet är kopplat till, returnerar följande kommandon ALLA NÄTVERKSGRÄNSSNITT som är kopplade till en virtuell dator:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Lösa ett problem

Att lösa routningsproblem består vanligtvis av:

- Lägga till en anpassad väg för att åsidosätta en av Azures standardvägar. Lär dig hur du [lägger till en anpassad väg](manage-route-table.md#create-a-route).
- Ändra eller ta bort en anpassad väg som kan orsaka routning till en oönskad plats. Lär dig hur [du ändrar](manage-route-table.md#change-a-route) eller [tar bort](manage-route-table.md#delete-a-route) en anpassad väg.
- Se till att den vägtabell som innehåller alla anpassade vägar som du har definierat är associerad med det undernät som nätverksgränssnittet finns i. Lär dig hur [du associerar en vägtabell med ett undernät](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Se till att enheter som Azure VPN-gateway eller virtuella nätverksenheter som du har distribuerat fungerar. Använd [funktionen VPN-diagnostik](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Network Watcher att fastställa eventuella problem med en Azure VPN-gateway.

Om du fortfarande har kommunikationsproblem kan du gå till [Överväganden](#considerations) och Ytterligare diagnos.

## <a name="considerations"></a>Överväganden

Tänk på följande när du felsöker kommunikationsproblem:

- Routning baseras på längsta prefixmatchning (LPM) mellan vägar som du har definierat, BGP (Border Gateway Protocol) och systemvägar. Om det finns fler än en väg med samma LPM-matchning väljs en väg baserat på dess ursprung i den ordning som anges i [Routningsöversikt](virtual-networks-udr-overview.md#how-azure-selects-a-route). Med effektiva vägar kan du bara se effektiva vägar som är en LPM-matchning, baserat på alla tillgängliga vägar. Om du ser hur vägarna utvärderas för ett nätverksgränssnitt blir det mycket enklare att felsöka specifika vägar som kan påverka kommunikationen från den virtuella datorn.
- Om du har definierat anpassade vägar till en virtuell  nätverksinstallation (NVA), med virtuell installation som nästa hopptyp, ser du till att IP-vidare vidarebefordran är aktiverat på den NVA som tar emot trafiken eller att paketen tas bort. Läs mer om [hur du aktiverar IP-vidarebefordran för ett nätverksgränssnitt.](virtual-network-network-interface.md#enable-or-disable-ip-forwarding) Dessutom måste operativsystemet eller programmet i NVA också kunna vidarebefordra nätverkstrafik och konfigureras för att göra det.
- Om du har skapat en väg till 0.0.0.0/0 dirigeras all utgående Internettrafik till nästa hopp som du har angett, till exempel till en NVA- eller VPN-gateway. Att skapa en sådan väg kallas ofta tvingad tunneling. Fjärranslutningar med RDP- eller SSH-protokoll från Internet till den virtuella datorn kanske inte fungerar med den här vägen, beroende på hur nästa hopp hanterar trafiken. Tvingad tunneling kan aktiveras:
    - När du använder plats-till-plats-VPN skapar du en väg med nästa hopptyp *som VPN Gateway*. Läs mer om hur [du konfigurerar tvingad tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Om en 0.0.0.0/0 (standardväg) annonseras via BGP via en virtuell nätverksgateway när du använder en plats-till-plats-VPN eller ExpressRoute-krets. Läs mer om att använda BGP med [en plats-till-plats-VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [ExpressRoute.](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)
- För att peering-trafik för virtuella nätverk ska fungera korrekt måste det finnas en systemväg med nästa hopptyp *för VNet-peering* för det peer-ade virtuella nätverkets prefixintervall. Om det inte finns någon sådan väg och peeringlänken för det virtuella nätverket är **Ansluten:**
    - Vänta några sekunder och försök igen. Om det är en nyligen upprättad peeringlänk tar det ibland längre tid att sprida vägar till alla nätverksgränssnitt i ett undernät. Mer information om peering för virtuella nätverk finns i [Översikt över peering för virtuella nätverk](virtual-network-peering-overview.md) och hantera [peering för virtuella nätverk.](virtual-network-manage-peering.md)
    - Regler för nätverkssäkerhetsgrupp kan påverka kommunikationen. Mer information finns i Diagnostisera [problem med filtreringen av nätverkstrafik på virtuella datorer.](diagnose-network-traffic-filter-problem.md)
- Azure tilldelar standardvägar till varje Azure-nätverksgränssnitt, men om du har flera nätverksgränssnitt anslutna till den virtuella datorn tilldelas endast det primära nätverksgränssnittet en standardväg (0.0.0.0/0) eller gateway i den virtuella datorns operativsystem. Lär dig hur du skapar en standardväg för sekundära nätverksgränssnitt som är anslutna till en virtuell [Windows-](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) eller [Linux-dator.](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) Läs mer om [primära och sekundära nätverksgränssnitt.](virtual-network-network-interface-vm.md#constraints)

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Om du vill köra ett snabbtest för att fastställa nästa hopptyp för trafik till en plats använder du funktionen [Nästa](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hopp i Azure Network Watcher. Nästa hopp visar vilken nästa hopptyp som är avsedd för trafik till en angiven plats.
* Om det inte finns några vägar som orsakar att en virtuell dators nätverkskommunikation misslyckas kan problemet bero på att brandväggsprogramvaran körs i den virtuella datorns operativsystem
* Om du tvingar [fram tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) till en lokal enhet via en VPN-gateway eller NVA, kanske du inte kan ansluta till en virtuell dator från Internet, beroende på hur du har konfigurerat routning för enheterna. Bekräfta att den routning som du har konfigurerat för enheten dirigerar trafik till antingen en offentlig eller privat IP-adress för den virtuella datorn.
* Använd funktionen [anslutningsfelsök](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att Network Watcher att fastställa routning, filtrering och fel i operativsystemet som orsakar utgående kommunikationsproblem.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla uppgifter, egenskaper och inställningar för en [vägtabell och vägar.](manage-route-table.md)
- Lär dig mer [om alla nästa hopptyper, systemvägar och hur Azure väljer en väg.](virtual-networks-udr-overview.md)
