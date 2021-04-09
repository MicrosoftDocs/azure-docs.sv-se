---
title: Lägga till, ändra eller ta bort ett Azure Virtual Network-undernät
titlesuffix: Azure Virtual Network
description: Lär dig var du hittar information om virtuella nätverk och hur du lägger till, ändrar eller tar bort ett virtuellt nätverks under nät i Azure.
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
ms.openlocfilehash: d591194f09e85c3e34ed4b904249df994a14bff5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100558590"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Lägga till, ändra eller ta bort ett virtuellt nätverksundernät

Lär dig hur du lägger till, ändrar eller tar bort ett virtuellt nätverks under nät. Alla Azure-resurser som distribueras till ett virtuellt nätverk distribueras till ett undernät i ett virtuellt nätverk. Om du inte har använt virtuella nätverk tidigare kan du lära dig mer om dem i det [virtuella nätverkets översikt](virtual-networks-overview.md) eller genom att slutföra en [snabb start](quick-create-portal.md). Mer information om hur du hanterar ett virtuellt nätverk finns i [skapa, ändra eller ta bort ett virtuellt nätverk](manage-virtual-network.md).

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har något konfigurerar du ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Slutför sedan en av de här uppgifterna innan du startar stegen i något avsnitt i den här artikeln: 

- **Portal användare**: Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

- **PowerShell-användare**: kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/powershell)eller kör PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. På fliken Azure Cloud Shell webbläsare letar du upp List rutan **Välj miljö** och väljer sedan **PowerShell** om den inte redan är markerad.

    Om du kör PowerShell lokalt använder du Azure PowerShell-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az.Network` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Kör också `Connect-AzAccount` för att skapa en anslutning till Azure.

- **Kommando rads gränssnitt för Azure (CLI)**: kör antingen kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller kör CLI från datorn. Använd Azure CLI version 2.0.31 eller senare om du kör Azure CLI lokalt. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Kör också `az login` för att skapa en anslutning till Azure.

Det konto som du loggar in på, eller ansluter till Azure med, måste tilldelas [rollen nätverks deltagar roll](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions).

## <a name="add-a-subnet"></a>Lägga till ett undernät

1. Gå till [Azure Portal](https://portal.azure.com) om du vill visa dina virtuella nätverk. Sök efter och välj **virtuella nätverk**.

2. Välj namnet på det virtuella nätverk som du vill lägga till ett undernät i.

3. Under **Inställningar** väljer du **undernät**  >  **under** nät.

4. I dialog rutan **Lägg till undernät** anger du värden för följande inställningar:

    | Inställning | Beskrivning |
    | --- | --- |
    | **Namn** | Namnet måste vara unikt inom det virtuella nätverket. För maximal kompatibilitet med andra Azure-tjänster rekommenderar vi att du använder en bokstav som det första tecknet i namnet. Azure Application Gateway kommer till exempel inte att distribueras till ett undernät som har ett namn som börjar med en siffra. |
    | **Adressintervall** | <p>Intervallet måste vara unikt inom adress utrymmet för det virtuella nätverket. Intervallet får inte överlappa andra under näts adress intervall inom det virtuella nätverket. Adress utrymmet måste anges genom att använda CIDR-notation (Classless Inter-Domain routing).</p><p>I ett virtuellt nätverk med adress utrymmet *10.0.0.0/16* kan du till exempel definiera ett under näts adress utrymme på *10.0.0.0/22*. Det minsta intervallet du kan ange är */29*, vilket ger åtta IP-adresser för under nätet. Azure reserverar den första och sista adressen i varje undernät för protokoll avvikelse. Tre ytterligare adresser är reserverade för användning av Azure-tjänster. Det innebär att du kan definiera ett undernät med ett */29* -adressintervall som resulterar i tre användbara IP-adresser i under nätet.</p><p>Om du planerar att ansluta ett virtuellt nätverk till en VPN-gateway måste du skapa ett Gateway-undernät. Läs mer om [vissa adress intervall för gateway-undernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Du kan ändra adress intervallet när under nätet har lagts till under vissa förhållanden. Information om hur du ändrar ett adress intervall för ett undernät finns i [ändra under näts inställningar](#change-subnet-settings).</p> |
    | **Nätverkssäkerhetsgrupp** | Om du vill filtrera inkommande och utgående nätverks trafik för under nätet kan du associera en befintlig nätverks säkerhets grupp till ett undernät. Nätverks säkerhets gruppen måste finnas i samma prenumeration och på samma plats som det virtuella nätverket. Lär dig mer om [nätverks säkerhets grupper](./network-security-groups-overview.md) och [hur du skapar en nätverks säkerhets grupp](tutorial-filter-network-traffic.md). |
    | **Routningstabell** | Om du vill styra routning av nätverks trafik till andra nätverk kan du välja att associera en befintlig routningstabell till ett undernät. Routningstabellen måste finnas i samma prenumeration och på samma plats som det virtuella nätverket. Lär dig mer om [Azure-routning](virtual-networks-udr-overview.md) och [hur du skapar en](tutorial-create-route-table-portal.md)routningstabell. |
    | **Tjänstslutpunkter** | <p>Ett undernät kan eventuellt ha en eller flera tjänst slut punkter aktiverade för den. Om du vill aktivera en tjänst slut punkt för en tjänst väljer du den tjänst eller de tjänster som du vill aktivera tjänst slut punkter för från listan över **tjänster** . Azure konfigurerar platsen automatiskt för en slut punkt. Som standard konfigurerar Azure tjänstens slut punkter för det virtuella nätverkets region. För att stödja regionala failover-scenarier konfigurerar Azure automatiskt slut punkter till [Azure-kopplade regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) för Azure Storage.</p><p>Om du vill ta bort en tjänst slut punkt avmarkerar du den tjänst som du vill ta bort tjänstens slut punkt för. Om du vill veta mer om tjänst slut punkter och vilka tjänster de kan aktive ras för, se [tjänst slut punkter för virtuella nätverk](virtual-network-service-endpoints-overview.md). När du har aktiverat en tjänst slut punkt för en tjänst måste du också aktivera nätverks åtkomst för under nätet för en resurs som skapats med tjänsten. Om du till exempel aktiverar tjänstens slut punkt för **Microsoft. Storage** måste du också aktivera nätverks åtkomst till alla Azure Storage konton som du vill bevilja nätverks åtkomst till. Om du vill aktivera nätverks åtkomst till undernät som en tjänst slut punkt är aktive rad för, se dokumentationen för den enskilda tjänsten som du har aktiverat tjänstens slut punkt för.</p><p>Om du vill kontrol lera att en tjänst slut punkt är aktive rad för ett undernät visar du [effektiva vägar](diagnose-network-routing-problem.md) för alla nätverks gränssnitt i under nätet. När du konfigurerar en slut punkt visas en *standard* väg med adressprefix för tjänsten och en nästa hopp typ för **VirtualNetworkServiceEndpoint**. Mer information om routning finns i [trafik dirigering i virtuella nätverk](virtual-networks-udr-overview.md).</p> |
    | **Delegering av undernät** | Ett undernät kan eventuellt ha en eller flera delegeringar aktiverade för det. Under näts delegering ger explicita behörigheter till tjänsten för att skapa tjänstespecifika resurser i under nätet med en unik identifierare under tjänst distributionen. Om du vill delegera för en tjänst väljer du den tjänst som du vill delegera till från listan **tjänster** . |

5. Om du vill lägga till under nätet i det virtuella nätverk som du har valt väljer du **OK**.

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Ändra under näts inställningar

1. Gå till [Azure Portal](https://portal.azure.com) om du vill visa dina virtuella nätverk. Sök efter och välj **virtuella nätverk**.

2. Välj namnet på det virtuella nätverk som innehåller det undernät som du vill ändra.

3. Från **Inställningar** väljer du **undernät**.

4. I listan över undernät väljer du det undernät som du vill ändra inställningarna för.

5. På sidan undernät ändrar du någon av följande inställningar:

    | Inställning | Beskrivning |
    | --- | --- |
    | **Adressintervall** | Om inga resurser har distribuerats i under nätet kan du ändra adress intervallet. Om det finns några resurser i under nätet måste du antingen flytta resurserna till ett annat undernät eller ta bort dem från under nätet först. De steg du tar för att flytta eller ta bort en resurs varierar beroende på resursen. Läs dokumentationen för var och en av dessa resurs typer för att lära dig hur du flyttar eller tar bort resurser i undernät. Se begränsningarna för **adress intervall** i steg 4 i [Lägg till ett undernät](#add-a-subnet). |
    | **Användare** | Du kan styra åtkomsten till under nätet genom att använda inbyggda roller eller dina egna anpassade roller. Mer information om hur du tilldelar roller och användare åtkomst till under nätet finns i [tilldela Azure-roller](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). |
    | **Nätverkssäkerhetsgrupp** och **Routningstabell** | Se steg 4 i [Lägg till ett undernät](#add-a-subnet). |
    | **Tjänstslutpunkter** | <p>Se tjänst slut punkter i steg 4 i [Lägg till ett undernät](#add-a-subnet). När du aktiverar en tjänst slut punkt för ett befintligt undernät kontrollerar du att inga kritiska aktiviteter körs på någon resurs i under nätet. Tjänst slut punkter växlar vägar på varje nätverks gränssnitt i under nätet. Tjänst slut punkterna går från att använda standard vägen med adressprefixet *0.0.0.0/0* och nästa hopp typ av *Internet*, för att använda en ny väg med adressprefix för tjänsten och en nästa hopp typ för *VirtualNetworkServiceEndpoint*.</p><p>Under växeln kan alla öppna TCP-anslutningar avslutas. Tjänstens slut punkt aktive ras inte förrän trafik flöden till tjänsten för alla nätverks gränssnitt har uppdaterats med den nya vägen. Mer information om routning finns i [trafik dirigering i virtuella nätverk](virtual-networks-udr-overview.md).</p> |
    | **Delegering av undernät** | Se tjänst slut punkter i steg 4 i [Lägg till ett undernät](#add-a-subnet). Under näts delegering kan ändras till noll eller flera delegeringar som är aktiverade för den. Om en resurs för en tjänst redan har distribuerats i under nätet går det inte att lägga till eller ta bort under nät delegering förrän alla resurser för tjänsten tas bort. Om du vill delegera för en annan tjänst väljer du den tjänst som du vill delegera till från listan **tjänster** . |

6. Välj **Spara**.

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Du kan bara ta bort ett undernät om det inte finns några resurser i under nätet. Om det finns resurser i under nätet måste du ta bort dessa resurser innan du kan ta bort under nätet. De steg du tar för att ta bort en resurs varierar beroende på resursen. Läs dokumentationen för var och en av dessa resurs typer för att lära dig hur du tar bort resurser i undernät.

1. Gå till [Azure Portal](https://portal.azure.com) om du vill visa dina virtuella nätverk. Sök efter och välj **virtuella nätverk**.

2. Välj namnet på det virtuella nätverk som innehåller det undernät som du vill ta bort.

3. Från **Inställningar** väljer du **undernät**.

4. I listan över undernät väljer du det undernät som du vill ta bort.

5. Välj **ta bort** och välj sedan **Ja** i bekräftelse dialog rutan.

### <a name="commands"></a>Kommandon

| Verktyg | Kommando |
| ---- | ------- |
| Azure CLI | [ta bort AZ Network VNet Subnet Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter i undernät måste ditt konto tilldelas [rollen nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder i följande tabell:

|Action                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Läs ett undernät för virtuellt nätverk              |
|Microsoft. Network/virtualNetworks/subnets/Write                          |   Skapa eller uppdatera ett virtuellt nätverks under nät  |
|Microsoft. Network/virtualNetworks/subnets/Delete                         |   Ta bort ett undernät för virtuellt nätverk            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Anslut till ett virtuellt nätverk                     |
|Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/Action  |   Aktivera en tjänst slut punkt för ett undernät     |
|Microsoft. Network/virtualNetworks/subnets/virtualMachines/Read           |   Hämta de virtuella datorerna i ett undernät       |

## <a name="next-steps"></a>Nästa steg

- Skapa ett virtuellt nätverk och undernät med [PowerShell](powershell-samples.md) -eller [Azure CLI](cli-samples.md) -exempel skript eller med hjälp av Azure [Resource Manager-mallar](template-samples.md)
- Skapa och tilldela [Azure policy definitioner](./policy-reference.md) för virtuella nätverk