---
title: Diagnostisera ett problem med Routning av virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du diagnostiserar ett problem med en virtuell dator genom att Visa effektiva vägar för en virtuell dator.
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
ms.openlocfilehash: 1c23244707179e05c63ed44b5915e58eefd3f4a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84705057"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnostisera ett problem med Routning av virtuell dator

I den här artikeln får du lära dig hur du diagnostiserar ett problem med Routning genom att visa de vägar som är effektiva för ett nätverks gränssnitt på en virtuell dator (VM). Azure skapar flera standard vägar för varje virtuellt nätverks under nät. Du kan åsidosätta Azures standard vägar genom att definiera vägar i en routningstabell och sedan associera routningstabellen till ett undernät. Kombinationen av vägar som du skapar, Azures standard vägar och alla vägar som sprids från ditt lokala nätverk via en Azure VPN-gateway (om ditt virtuella nätverk är anslutet till ditt lokala nätverk) via BGP (Border Gateway Protocol) är de effektiva vägarna för alla nätverks gränssnitt i ett undernät. Om du inte är bekant med Virtual Network, nätverks gränssnitt eller routnings koncept, se [Översikt över virtuella nätverk](virtual-networks-overview.md), [nätverks gränssnitt](virtual-network-network-interface.md)och [routning](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator, men anslutningen Miss lyckas. För att avgöra varför du inte kan ansluta till den virtuella datorn kan du Visa effektiva vägar för ett nätverks gränssnitt med hjälp av Azure [Portal](#diagnose-using-azure-portal), [POWERSHELL](#diagnose-using-powershell)eller [Azure CLI](#diagnose-using-azure-cli).

Stegen nedan förutsätter att du har en befintlig virtuell dator för att Visa effektiva vägar för. Om du inte har en befintlig virtuell dator distribuerar du först en [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator för att slutföra uppgifterna i den här artikeln med. Exemplen i den här artikeln gäller för en virtuell dator med namnet *myVM* med ett nätverks gränssnitt med namnet *myVMNic1*. Den virtuella datorn och nätverks gränssnittet finns i en resurs grupp med namnet *myResourceGroup* och finns i regionen *USA, östra* . Ändra värdena i stegen efter behov för den virtuella dator som du diagnostiserar problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med Azure Portal

1. Logga in på Azure [Portal](https://portal.azure.com) med ett Azure-konto som har de [behörigheter som krävs](virtual-network-network-interface.md#permissions).
2. Överst i Azure Portal anger du namnet på en virtuell dator som körs i rutan Sök. När namnet på den virtuella datorn visas i Sök resultatet väljer du det.
3. Under **Inställningar** till vänster väljer du **nätverk** och navigerar till nätverks gränssnitts resursen genom att välja dess namn.
     ![Visa nätverks gränssnitt](./media/diagnose-network-routing-problem/view-nics.png)
4. Välj **effektiva vägar** till vänster. De effektiva vägarna för ett nätverks gränssnitt med namnet **myVMNic1** visas i följande bild: ![ Visa effektiva vägar](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Om det finns flera nätverks gränssnitt som är anslutna till den virtuella datorn kan du Visa effektiva vägar för alla nätverks gränssnitt genom att välja det. Eftersom varje nätverks gränssnitt kan finnas i ett annat undernät kan varje nätverks gränssnitt ha olika effektiva vägar.

    I exemplet som visas i föregående bild är de listade vägarna standard vägar som Azure skapar för varje undernät. Listan har minst dessa vägar, men kan ha ytterligare vägar, beroende på vilka funktioner du kan ha aktiverat för det virtuella nätverket, till exempel att det är peer-kopplat med ett annat virtuellt nätverk eller anslutit till ditt lokala nätverk via en Azure VPN-gateway. Om du vill veta mer om varje väg och andra vägar som du kan se för ditt nätverks gränssnitt kan du läsa mer i [trafik routning för virtuella nätverk](virtual-networks-udr-overview.md). Om listan har ett stort antal vägar, kan det vara lättare att välja **Hämta** för att ladda ned en CSV-fil med en lista över vägar.

Trots att effektiva vägar har visats via den virtuella datorn i föregående steg, kan du också Visa effektiva vägar via en:
- **Enskilt nätverks gränssnitt**: Lär dig hur du [visar ett nätverks gränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **Enskild routningstabell**: Lär dig hur du [visar en](manage-route-table.md#view-details-of-a-route-table)routningstabell.

## <a name="diagnose-using-powershell"></a>Diagnostisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att logga in på Azure med ett konto som har de [behörigheter som krävs](virtual-network-network-interface.md#permissions).

Hämta effektiva vägar för ett nätverks gränssnitt med [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). I följande exempel hämtas effektiva vägar för ett nätverks gränssnitt med namnet *myVMNic1*, som finns i en resurs grupp med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Information som returneras i utdata finns i [Operationsföljd översikt](virtual-networks-udr-overview.md). Utdata returneras bara om den virtuella datorn är i körnings läge. Om det finns flera nätverks gränssnitt som är anslutna till den virtuella datorn kan du granska de effektiva vägarna för varje nätverks gränssnitt. Eftersom varje nätverks gränssnitt kan finnas i ett annat undernät kan varje nätverks gränssnitt ha olika effektiva vägar. Om du fortfarande har problem med kommunikationen, se [ytterligare diagnostik](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverks gränssnitt, men känner till namnet på den virtuella dator som nätverks gränssnittet är kopplat till, returnerar följande kommandon ID: n för alla nätverks gränssnitt som är anslutna till en virtuell dator:

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

I föregående utdata är nätverks gränssnittets namn *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med Azure CLI

Du kan köra kommandona som följer i  [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra `az login` och logga in på Azure med ett konto som har de [behörigheter som krävs](virtual-network-network-interface.md#permissions).

Hämta effektiva vägar för ett nätverks gränssnitt med [AZ Network NIC show-effektiv-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). I följande exempel hämtas effektiva vägar för ett nätverks gränssnitt med namnet *myVMNic1* som finns i en resurs grupp med namnet *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Information som returneras i utdata finns i [Operationsföljd översikt](virtual-networks-udr-overview.md). Utdata returneras bara om den virtuella datorn är i körnings läge. Om det finns flera nätverks gränssnitt som är anslutna till den virtuella datorn kan du granska de effektiva vägarna för varje nätverks gränssnitt. Eftersom varje nätverks gränssnitt kan finnas i ett annat undernät kan varje nätverks gränssnitt ha olika effektiva vägar. Om du fortfarande har problem med kommunikationen, se [ytterligare diagnostik](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverks gränssnitt, men känner till namnet på den virtuella dator som nätverks gränssnittet är kopplat till, returnerar följande kommandon ID: n för alla nätverks gränssnitt som är anslutna till en virtuell dator:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Lösa ett problem

Att lösa problem med Routning består vanligt vis av:

- Lägga till en anpassad väg för att åsidosätta en av Azures standard vägar. Lär dig hur du [lägger till en anpassad väg](manage-route-table.md#create-a-route).
- Ändra eller ta bort en anpassad väg som kan leda till en oönskad plats. Lär dig hur du [ändrar](manage-route-table.md#change-a-route) eller [tar bort](manage-route-table.md#delete-a-route) en anpassad väg.
- Se till att routningstabellen som innehåller anpassade vägar som du har definierat är kopplad till under nätet som nätverks gränssnittet finns i. Lär dig hur du [kopplar en routningstabell till ett undernät](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Att se till att enheter som Azure VPN-gateway eller virtuella nätverks enheter som du har distribuerat fungerar. Använd funktionen för [VPN-diagnostik](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för Network Watcher för att fastställa eventuella problem med en Azure VPN-gateway.

Om du fortfarande har kommunikations problem, se [överväganden](#considerations) och ytterligare diagnos.

## <a name="considerations"></a>Överväganden

Tänk på följande när du felsöker kommunikations problem:

- Routning baseras på den längsta prefix matchningen (LPM) mellan vägar som du har definierat, BGP (Border Gateway Protocol) och system vägar. Om det finns fler än en väg med samma LPM-matchning, väljs en väg baserat på dess ursprung i den ordning som visas i [Översikt över routningen](virtual-networks-udr-overview.md#how-azure-selects-a-route). Med effektiva vägar kan du bara se effektiva vägar som är en LPM-matchning, baserat på alla tillgängliga vägar. Att se hur vägarna utvärderas för ett nätverks gränssnitt gör det mycket enklare att felsöka vissa vägar som kan påverka kommunikationen från den virtuella datorn.
- Om du har definierat anpassade vägar till en virtuell nätverks installation (NVA), med *virtuell* installation som nästa hopp typ, kontrollerar du att IP-vidarebefordring är aktiverat på NVA som tar emot trafiken, eller att paketen tas bort. Läs mer om hur du [aktiverar IP-vidarebefordring för ett nätverks gränssnitt](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Dessutom måste operativ systemet eller programmet i NVA också kunna vidarebefordra nätverks trafik och konfigureras för att göra det.
- Om du har skapat en väg till 0.0.0.0/0 dirigeras all utgående Internet trafik till nästa hopp som du har angett, till exempel en NVA eller VPN-gateway. Att skapa en sådan väg kallas ofta för Tvingad tunnel trafik. Fjärr anslutningar med hjälp av RDP-eller SSH-protokollen från Internet till den virtuella datorn kanske inte fungerar med den här vägen, beroende på hur nästa hopp hanterar trafiken. Tvingad tunnel trafik kan aktive ras:
    - När du använder plats-till-plats-VPN, genom att skapa en väg med nästa hopp typ *VPN gateway*. Läs mer om hur du [konfigurerar Tvingad tunnel trafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Om en 0.0.0.0/0 (standard väg) annonseras via BGP via en virtuell nätverksgateway när du använder en plats-till-plats-VPN-eller ExpressRoute-krets. Läs mer om hur du använder BGP med en [plats-till-plats-VPN-](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- För att peer-trafiken ska fungera korrekt måste en system väg med en nästa hopp typ av *VNet-peering* finnas för det peer-kopplade virtuella nätverkets prefixlängd. Om det inte finns någon väg och det virtuella nätverkets peering-länk är **ansluten**:
    - Vänta några sekunder och försök igen. Om det är en nyligen etablerad peering-länk tar det ibland längre tid att sprida vägar till alla nätverks gränssnitt i ett undernät. Mer information om peering för virtuella nätverk finns i [Översikt över virtuella nätverk](virtual-network-peering-overview.md) och [Hantera peering för virtuella nätverk](virtual-network-manage-peering.md).
    - Regler för nätverks säkerhets grupper kan påverka kommunikationen. Mer information finns i [diagnostisera ett problem med nätverks trafik filter för virtuella datorer](diagnose-network-traffic-filter-problem.md).
- Även om Azure tilldelar standard vägar till varje Azure-nätverks gränssnitt, om du har flera nätverks gränssnitt som är anslutna till den virtuella datorn, tilldelas bara det primära nätverks gränssnittet en standard väg (0.0.0.0/0) eller gateway i den virtuella datorns operativ system. Lär dig hur du skapar en standard väg för sekundära nätverks gränssnitt som är anslutna till en virtuell [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) -eller [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) -dator. Läs mer om [primära och sekundära nätverks gränssnitt](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Om du vill köra ett snabb test för att fastställa nästa hopp typ för trafik till en plats använder du [nästa hopp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funktion i Azure Network Watcher. Nästa hopp anger vad nästa hopp typ är för trafik till en angiven plats.
* Om det inte finns några vägar som orsakar att en virtuell dators nätverkskommunikation fungerar, kan problemet bero på att brand Väggs programmet körs i den virtuella datorns operativ system
* Om du [tvingar fram tunnel](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) trafik till en lokal enhet via en VPN-gateway eller NVA kan du inte ansluta till en virtuell dator från Internet, beroende på hur du har konfigurerat routning för enheterna. Bekräfta att den routning som du har konfigurerat för enheten dirigerar trafik till antingen en offentlig eller privat IP-adress för den virtuella datorn.
* Använd [anslutnings fel söknings](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funktionen i Network Watcher för att fastställa routning, filtrering och operativ system orsaker till problem med utgående kommunikation.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla aktiviteter, egenskaper och inställningar för en routningstabell [och vägar](manage-route-table.md).
- Lär dig mer om alla [typer av nästa hopp, system vägar och hur Azure väljer en väg](virtual-networks-udr-overview.md).
