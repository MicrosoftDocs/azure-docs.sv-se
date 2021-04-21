---
title: Lägga till, ändra eller ta bort ett virtuellt Azure-nätverksundernät
titlesuffix: Azure Virtual Network
description: Lär dig var du hittar information om virtuella nätverk och hur du lägger till, ändrar eller tar bort ett undernät för virtuella nätverk i Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 1e655b20d2f6295f0d6cfe8008fee7b360525611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774295"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Lägga till, ändra eller ta bort ett virtuellt nätverksundernät

Lär dig hur du lägger till, ändrar eller tar bort ett undernät för ett virtuellt nätverk. Alla Azure-resurser som distribueras till ett virtuellt nätverk distribueras till ett undernät i ett virtuellt nätverk. Om du inte har börjat använda virtuella nätverk kan du läsa mer om dem i översikten över virtuella [nätverk](virtual-networks-overview.md) eller genom att slutföra en [snabbstart.](quick-create-portal.md) Mer information om hur du hanterar ett virtuellt nätverk finns [i Skapa, ändra eller ta bort ett virtuellt nätverk.](manage-virtual-network.md)

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har ett konto kan du konfigurera ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Utför sedan någon av dessa uppgifter innan du påbörjar stegen i något avsnitt i den här artikeln: 

- **Portalanvändare:** Logga in på [Azure Portal](https://portal.azure.com) ditt Azure-konto.

- **PowerShell-användare:** Kör antingen [kommandona i Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. På Azure Cloud Shell webbläsarflik hittar du  listrutan Välj miljö och väljer **sedan PowerShell** om det inte redan är markerat.

    Om du kör PowerShell lokalt använder du Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör även `Connect-AzAccount` för att skapa en anslutning till Azure.

- **Azure CLI-användare (Command-Line Interface):** Kör antingen kommandona [i Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.31 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör även `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på, eller ansluter [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) till Azure med, [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) måste tilldelas rollen Nätverksdeltagare eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i [Behörigheter.](#permissions)

## <a name="add-a-subnet"></a>Lägga till ett undernät

1. Gå till [Azure Portal](https://portal.azure.com) för att visa dina virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

2. Välj namnet på det virtuella nätverk som du vill lägga till ett undernät i.

3. Från **Inställningar** väljer du **Undernät**  >  **Undernät**.

4. I dialogrutan **Lägg till undernät** anger du värden för följande inställningar:

    | Inställning | Beskrivning |
    | --- | --- |
    | **Namn** | Namnet måste vara unikt inom det virtuella nätverket. För maximal kompatibilitet med andra Azure-tjänster rekommenderar vi att du använder en bokstav som det första tecknet i namnet. Till exempel Azure Application Gateway inte distribueras till ett undernät som har ett namn som börjar med ett tal. |
    | **Adressintervall** | <p>Intervallet måste vara unikt inom adressutrymmet för det virtuella nätverket. Intervallet får inte överlappa andra adressintervall för undernätet i det virtuella nätverket. Adressutrymmet måste anges med hjälp av CIDR-notation (Classless Inter-Domain Routing).</p><p>I ett virtuellt nätverk med adressutrymmet *10.0.0.0/16* kan du till exempel definiera ett adressutrymme för undernätet *på 10.0.0.0/22.* Det minsta intervallet som du kan ange *är /29*, som tillhandahåller åtta IP-adresser för undernätet. Azure reserverar den första och sista adressen i varje undernät för protokollöverensstämmelse. Ytterligare tre adresser är reserverade för användning av Azure-tjänster. Om du definierar ett undernät med *ett /29-adressintervall* resulterar det därför i tre användbara IP-adresser i undernätet.</p><p>Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway måste du skapa ett gateway-undernät. Läs mer om [specifika överväganden för adressintervall för gateway-undernät.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) Du kan ändra adressintervallet när undernätet har lagts till under specifika villkor. Information om hur du ändrar ett adressintervall för undernätet finns i [Ändra undernätsinställningar.](#change-subnet-settings)</p> |
    | **Nätverkssäkerhetsgrupp** | Om du vill filtrera inkommande och utgående nätverkstrafik för undernätet kan du associera en befintlig nätverkssäkerhetsgrupp till ett undernät. Nätverkssäkerhetsgruppen måste finnas i samma prenumeration och plats som det virtuella nätverket. Läs mer om [nätverkssäkerhetsgrupper](./network-security-groups-overview.md) och [hur du skapar en nätverkssäkerhetsgrupp.](tutorial-filter-network-traffic.md) |
    | **Routningstabell** | Om du vill styra routningen av nätverkstrafik till andra nätverk kan du eventuellt associera en befintlig routningstabell till ett undernät. Vägtabellen måste finnas i samma prenumeration och plats som det virtuella nätverket. Läs mer om [Azure-routning](virtual-networks-udr-overview.md) [och hur du skapar en routningstabell.](tutorial-create-route-table-portal.md) |
    | **Tjänstslutpunkter** | <p>Ett undernät kan också ha en eller flera tjänstslutpunkter aktiverade för det. Om du vill aktivera en tjänstslutpunkt för en tjänst väljer du den eller de tjänster som du vill aktivera tjänstslutpunkter för från **listan** Tjänster. Azure konfigurerar platsen automatiskt för en slutpunkt. Som standard konfigurerar Azure tjänstslutpunkterna för det virtuella nätverkets region. För att stödja regionala redundansscenarier konfigurerar Azure automatiskt slutpunkter [till Azure-parkopplade regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) för Azure Storage.</p><p>Om du vill ta bort en tjänstslutpunkt avmarkerar du den tjänst som du vill ta bort tjänstslutpunkten för. Mer information om tjänstslutpunkter och vilka tjänster de kan aktiveras för finns i [Tjänstslutpunkter för virtuellt nätverk.](virtual-network-service-endpoints-overview.md) När du aktiverar en tjänstslutpunkt för en tjänst måste du även aktivera nätverksåtkomst för undernätet för en resurs som skapats med tjänsten. Om du till exempel aktiverar tjänstslutpunkten för **Microsoft.Storage** måste du även aktivera nätverksåtkomst till alla Azure Storage-konton som du vill bevilja nätverksåtkomst till. Information om hur du aktiverar nätverksåtkomst till undernät som en tjänstslutpunkt är aktiverad för finns i dokumentationen för den enskilda tjänst som du aktiverade tjänstslutpunkten för.</p><p>Om du vill verifiera att en tjänstslutpunkt har aktiverats för ett undernät kan du visa de [gällande](diagnose-network-routing-problem.md) vägarna för alla nätverksgränssnitt i undernätet. När du konfigurerar en  slutpunkt visas en standardväg med tjänstens adressprefix och nexthop-typen **VirtualNetworkServiceEndpoint**. Mer information om routning finns i [Trafikdirigering i virtuella nätverk.](virtual-networks-udr-overview.md)</p> |
    | **Delegering av undernät** | Ett undernät kan också ha en eller flera delegeringar aktiverade för det. Undernätsdelegering ger explicit behörighet till tjänsten för att skapa tjänstspecifika resurser i undernätet med hjälp av en unik identifierare under tjänstdistributionen. Om du vill delegera för en tjänst väljer du den tjänst som du vill delegera till **från listan** Tjänster. |

5. Om du vill lägga till undernätet i det virtuella nätverk som du har valt väljer du **OK.**

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Ändra undernätsinställningar

1. Gå till Azure Portal [för](https://portal.azure.com) att visa dina virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

2. Välj namnet på det virtuella nätverk som innehåller det undernät som du vill ändra.

3. Från **Inställningar** väljer **du Undernät**.

4. I listan över undernät väljer du det undernät som du vill ändra inställningarna för.

5. Ändra någon av följande inställningar på undernätssidan:

    | Inställning | Beskrivning |
    | --- | --- |
    | **Adressintervall** | Om inga resurser distribueras i undernätet kan du ändra adressintervallet. Om det finns några resurser i undernätet måste du antingen flytta resurserna till ett annat undernät eller ta bort dem från undernätet först. Vilka steg du gör för att flytta eller ta bort en resurs varierar beroende på resursen. Information om hur du flyttar eller tar bort resurser som finns i undernät finns i dokumentationen för var och en av dessa resurstyper. Se begränsningarna för **Adressintervall i** steg 4 i Lägg [till ett undernät](#add-a-subnet). |
    | **Användare** | Du kan styra åtkomsten till undernätet med hjälp av inbyggda roller eller dina egna anpassade roller. Mer information om hur du tilldelar roller och användare för åtkomst till undernätet finns i [Tilldela Azure-roller.](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
    | **Nätverkssäkerhetsgrupp** och **Routningstabell** | Se steg 4 i [Lägga till ett undernät](#add-a-subnet). |
    | **Tjänstslutpunkter** | <p>Se tjänstslutpunkter i steg 4 i [Lägg till ett undernät](#add-a-subnet). När du aktiverar en tjänstslutpunkt för ett befintligt undernät ska du se till att inga kritiska uppgifter körs på någon resurs i undernätet. Tjänstslutpunkter växlar vägar i varje nätverksgränssnitt i undernätet. Tjänstslutpunkterna går från att använda standardvägen med adressprefixet *0.0.0.0/0* och nästa hopptypen *Internet* till att använda en ny väg med adressprefix för tjänsten och nästa hopptyp *virtualNetworkServiceEndpoint*.</p><p>Under växeln kan alla öppna TCP-anslutningar avslutas. Tjänstslutpunkten är inte aktiverad förrän trafik flödar till tjänsten för alla nätverksgränssnitt uppdateras med den nya vägen. Mer information om routning finns i [Trafikdirigering i virtuella nätverk.](virtual-networks-udr-overview.md)</p> |
    | **Delegering av undernät** | Se tjänstslutpunkter i steg 4 i [Lägg till ett undernät](#add-a-subnet). Undernätsdelegering kan ändras till noll eller flera delegeringar aktiverade för den. Om en resurs för en tjänst redan har distribuerats i undernätet kan inte undernätsdelegering läggas till eller tas bort förrän alla resurser för tjänsten tas bort. Om du vill delegera för en annan tjänst väljer du den tjänst som du vill delegera till **från listan** Tjänster. |

6. Välj **Spara**.

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Du kan bara ta bort ett undernät om det inte finns några resurser i undernätet. Om resurserna finns i undernätet måste du ta bort resurserna innan du kan ta bort undernätet. Vilka steg du gör för att ta bort en resurs varierar beroende på resursen. Information om hur du tar bort resurser som finns i undernät finns i dokumentationen för var och en av dessa resurstyper.

1. Gå till [Azure Portal](https://portal.azure.com) för att visa dina virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

2. Välj namnet på det virtuella nätverk som innehåller det undernät som du vill ta bort.

3. Från **Inställningar** väljer **du Undernät**.

4. I listan över undernät väljer du det undernät som du vill ta bort.

5. Välj **Ta** bort och välj **sedan Ja** i bekräftelsedialogrutan.

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i undernät [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) måste ditt konto [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tilldelas till rollen Nätverksdeltagare eller till en anpassad roll som har tilldelats lämpliga åtgärder i följande tabell:

|Action                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Läsa ett undernät för virtuellt nätverk              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Skapa eller uppdatera ett undernät för virtuellt nätverk  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Ta bort ett undernät för virtuellt nätverk            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Ansluta till ett virtuellt nätverk                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Aktivera en tjänstslutpunkt för ett undernät     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Hämta de virtuella datorerna i ett undernät       |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk och undernät med [hjälp av PowerShell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller med hjälp av Azure [Resource Manager mallar](template-samples.md)
- Skapa och tilldela [Azure Policy definitioner](./policy-reference.md) för virtuella nätverk