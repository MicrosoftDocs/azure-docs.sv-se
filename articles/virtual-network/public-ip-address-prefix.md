---
title: Prefix för offentliga Azure-IP-adresser
description: Lär dig mer om vad ett prefix för Azures offentliga IP-adress är och hur det kan hjälpa dig att tilldela förutsägbara offentliga IP-adresser till dina resurser.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 874cb283f0076905393aa529e4ff9e1bad3ae047
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419685"
---
# <a name="public-ip-address-prefix"></a>Prefix för offentliga IP-adresser

Ett offentligt IP-adressprefix är ett reserverat intervall med IP-adresser i Azure. Azure ger ett sammanhängande intervall med adresser till din prenumeration utifrån hur många du anger. 

Om du inte är bekant med offentliga adresser, se [offentliga IP-adresser.](./public-ip-addresses.md#public-ip-addresses)

Offentliga IP-adresser tilldelas från en adresspool med adresser i varje Azure-region. Du kan [Ladda ned](https://www.microsoft.com/download/details.aspx?id=56519) listan över intervall som Azure använder för varje region. Till exempel är 40.121.0.0/16 en av över 100 intervall som Azure använder i regionen USA, östra. Intervallet omfattar de användbara adresserna för 40.121.0.1-40.121.255.254.

Du skapar ett offentligt IP-adressprefix i en Azure-region och-prenumeration genom att ange ett namn och hur många adresser du vill att prefixet ska innehålla. 

Offentliga IP-adressintervall tilldelas med ett prefix som du väljer. Om du skapar ett prefix på/28 ger Azure 16 IP-adresser från ett av dess intervall.

Du vet inte vilket intervall Azure ska tilldela förrän du skapar intervallet, men adresserna är sammanhängande. 

Prefix för offentliga IP-adresser har en avgift. mer information finns i [pris information om offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Varför ska du skapa ett offentligt IP-adressprefix?

När du skapar offentliga IP-adressresurser tilldelar Azure en tillgänglig offentlig IP-adress från något av de intervall som används i den regionen. 

Innan Azure tilldelar IP-adressen, vet du inte den exakta IP-adressen. Den här processen kan vara problematisk när du skapar brand Väggs regler som tillåter vissa IP-adresser. För varje tillagd IP-adress måste en motsvarande brand Väggs regel läggas till.

När du tilldelar adresser till resurser från ett offentligt IP-adressprefix krävs inte uppdateringar av brand Väggs regler. Hela intervallet läggs till i regeln.

## <a name="benefits"></a>Fördelar

- Skapa offentliga IP-adressresurser från ett känt intervall.
- Konfiguration av brand Väggs regler med intervall som innehåller offentliga IP-adresser som du har tilldelat och adresser som du inte har tilldelat ännu. Den här konfigurationen eliminerar behovet av att ändra brand Väggs regler när du tilldelar IP-adresser till nya resurser.
- Standard storleken för ett intervall du kan skapa är/28 eller 16 IP-adresser.
- Det finns inga begränsningar för hur många intervall du kan skapa. Det finns gränser för det högsta antalet statiska offentliga IP-adresser som du kan ha i en Azure-prenumeration. Antalet intervall som du skapar kan inte omfatta fler statiska offentliga IP-adresser än du kan ha i din prenumeration. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- De adresser som du skapar med hjälp av adresser från prefixet kan tilldelas till alla Azure-resurser som du kan tilldela en offentlig IP-adress till.
- Du kan enkelt se vilka IP-adresser som anges och inte anges inom intervallet.

## <a name="scenarios"></a>Scenarier
Du kan koppla följande resurser till en statisk offentlig IP-adress från ett prefix:

|Resurs|Scenario|Steg|
|---|---|---|
|Virtuella datorer| Att associera offentliga IP-adresser från ett prefix till dina virtuella datorer i Azure minskar hanterings kostnaderna när du lägger till IP-adresser i en lista över tillåtna i brand väggen. Du kan lägga till ett helt prefix med en enda brand Väggs regel. När du skalar med virtuella datorer i Azure kan du koppla IP-adresser från samma prefix sparar kostnad, tid och hanterings kostnader.| Koppla IP-adresser från ett prefix till den virtuella datorn: </br> 1. [skapa ett prefix.](manage-public-ip-address-prefix.md) </br> 2. [skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) </br> 3. [koppla IP-adressen till den virtuella datorns nätverks gränssnitt.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> Du kan också [associera IP-adresserna till en skalnings uppsättning för virtuella datorer](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standard belastnings utjämning | Att associera offentliga IP-adresser från ett prefix till klient delens IP-konfiguration eller utgående regel för en belastningsutjämnare säkerställer förenkling av ditt offentliga IP-adressutrymme i Azure. Förenkla ditt scenario genom att rensa utgående anslutningar från en rad sammanhängande IP-adresser. | Koppla IP-adresser från ett prefix till belastningsutjämnaren: </br> 1. [skapa ett prefix.](manage-public-ip-address-prefix.md) </br> 2. [skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) </br> 3. När du skapar belastningsutjämnaren väljer eller uppdaterar du den IP-adress som skapades i steg 2 ovan som klient delens IP-adress för belastningsutjämnaren. |
| Azure Firewall | Du kan använda en offentlig IP-adress från ett prefix för utgående SNAT. All utgående virtuell nätverks trafik översätts till den offentliga IP-adressen i [Azure-brandväggen](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . | Koppla en IP-adress från ett prefix till din brand vägg: </br> 1. [skapa ett prefix.](manage-public-ip-address-prefix.md) </br> 2. [skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) </br> 3. När du [distribuerar Azure-brandväggen](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)måste du se till att välja den IP-adress som du tidigare har fått från prefixet.|
| VPN Gateway (AZ SKU) eller Application Gateway v2 | Du kan använda en offentlig IP-adress från ett prefix för zonens redundant VPN eller Application Gateway v2. | Koppla en IP-adress från ett prefix till din Gateway: </br> 1. [skapa ett prefix.](manage-public-ip-address-prefix.md) </br> 2. [skapa en IP-adress från prefixet.](manage-public-ip-address-prefix.md) </br> 3. När du distribuerar [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/tutorial-create-gateway-portal) eller [Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway)måste du välja den IP-adress som du tidigare har fått från prefixet.|

## <a name="constraints"></a>Villkor

- Du kan inte ange IP-adresser för prefixet. Azure ger prefixets IP-adresser baserat på den storlek som du anger.
- Du kan skapa ett prefix med upp till 16 IP-adresser eller a/28 som standard. Granska [nätverks gränser öka förfrågningar](../azure-portal/supportability/networking-quota-requests.md) och [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för mer information.
- Du kan inte ändra intervallet när du har skapat prefixet.
- Endast statiska offentliga IP-adresser som skapats med standard-SKU: n kan tilldelas från prefixets intervall. Mer information om offentliga IP-adresser finns i [offentlig IP-adress](./public-ip-addresses.md#public-ip-addresses).
- Adresser från intervallet kan bara tilldelas Azure Resource Manager resurser. Adresser kan inte tilldelas till resurser i den klassiska distributions modellen.
- Alla offentliga IP-adresser som skapas från prefixet måste finnas i samma Azure-region och prenumeration som prefixet. Adresser måste tilldelas till resurser i samma region och prenumeration.
- Du kan inte ta bort ett prefix om några adresser i det har tilldelats offentliga IP-adressresurser som är kopplade till en resurs. Koppla bort alla offentliga IP-adressresurser som tilldelas IP-adresser från prefixet först.


## <a name="next-steps"></a>Nästa steg

- [Skapa](manage-public-ip-address-prefix.md) ett offentligt IP-adressprefix