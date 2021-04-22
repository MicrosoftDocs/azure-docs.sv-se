---
title: Hantera offentliga IP-adresser | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Hantera offentliga IP-adresser.  Lär dig också hur en offentlig IP-adress är en resurs med egna konfigurerbara inställningar.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 3277d5836d85f1071b7aa169cc83896934a2f63d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872451"
---
# <a name="manage-public-ip-addresses"></a>Hantera offentliga IP-adresser

Lär dig mer om en offentlig IP-adress och hur du skapar, ändrar och tar bort en. En offentlig IP-adress är en resurs med egna konfigurerbara inställningar. Genom att tilldela en offentlig IP-adress till en Azure-resurs som stöder offentliga IP-adresser kan du göra följande:
- Inkommande kommunikation från Internet till resursen, till exempel Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateway och andra. Du kan fortfarande kommunicera med vissa resurser, till exempel virtuella datorer, från Internet, om en virtuell dator inte har tilldelats en offentlig IP-adress, så länge den virtuella datorn är en del av en lastbalanserares backend-pool och lastbalanseraren tilldelas en offentlig IP-adress. Information om huruvida en resurs för en specifik Azure-tjänst kan tilldelas en offentlig IP-adress eller om den kan kommuniceras med via den offentliga IP-adressen för en annan Azure-resurs finns i dokumentationen för tjänsten.
- Utgående anslutning till Internet med en förutsägbar IP-adress. En virtuell dator kan till exempel kommunicera utgående till Internet utan en tilldelad offentlig IP-adress, men dess adress är nätverksadress som översätts av Azure till en oförutsägbar offentlig adress som standard. Genom att tilldela en offentlig IP-adress till en resurs kan du veta vilken IP-adress som används för den utgående anslutningen. Även om det är förutsägbart kan adressen ändras, beroende på vilken tilldelningsmetod som valts. Mer information finns i Skapa [en offentlig IP-adress.](#create-a-public-ip-address) Mer information om utgående anslutningar från Azure-resurser finns i [Förstå utgående anslutningar.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande uppgifter innan du slutför stegen i något avsnitt i den här artikeln:

- Om du inte redan har ett Azure-konto kan du registrera dig för ett kostnadsfritt [utvärderingskonto.](https://azure.microsoft.com/free)
- Om du använder portalen öppnar du https://portal.azure.com och loggar in med ditt Azure-konto.
- Om du använder PowerShell-kommandon för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona [i Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.
- Om du använder Azure CLI-kommandon (Kommandoradsgränssnitt) för att slutföra uppgifter i den här artikeln kan du antingen köra kommandona i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra CLI från datorn. Den här självstudien kräver Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också köra för `az login` att skapa en anslutning till Azure.

Det konto som du loggar in på, eller [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ansluter till Azure [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) med, måste tilldelas till nätverksdeltagarerollen eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i [Behörigheter](#permissions).

Offentliga IP-adresser har en nominell avgift. Om du vill visa prissättningen läser du [prissättningssidan för IP-adresser.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Anvisningar om hur du skapar offentliga IP-adresser med hjälp av portalen, PowerShell eller CLI finns på följande sidor:

 * [Skapa offentliga IP-adresser – portalen](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Skapa offentliga IP-adresser – PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Skapa offentliga IP-adresser – Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Även om portalen ger möjlighet att skapa två offentliga IP-adressresurser (en IPv4 och en IPv6), skapar PowerShell- och CLI-kommandona en resurs med en adress för en ELLER flera IP-versioner. Om du vill ha två offentliga IP-adressresurser, en för varje IP-version, måste du köra kommandot två gånger och ange olika namn och IP-versioner för de offentliga IP-adressresurserna.

Mer information om specifika attribut för en offentlig IP-adress när den skapas finns i tabellen nedan.

   |Inställning|Obligatoriskt?|Information|
   |---|---|---|
   |IP-version|Yes| Välj IPv4, IPv6 eller Båda. Om du väljer Båda skapas 2 offentliga IP-adresser– 1 IPv4-adress och 1 IPv6-adress. Läs mer om [IPv6 i Azure VNET.](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
   |SKU|Yes|Alla offentliga IP-adresser som skapats innan SKU:er introducerades är **offentliga IP-adresser** för Basic SKU. Du kan inte ändra SKU:n när den offentliga IP-adressen har skapats. En fristående virtuell dator, virtuella datorer i en tillgänglighetsuppsättning eller VM-skalningsuppsättningar kan använda Basic- eller Standard-SKU:er. Det är inte tillåtet att blanda SKU:er mellan virtuella datorer i tillgänglighetsuppsättningar eller skalningsuppsättningar eller fristående virtuella datorer. **Grundläggande** SKU: Om du skapar en offentlig IP-adress i  en region som stöder tillgänglighetszoner är inställningen Tillgänglighetszon inställd på *Ingen* som standard. Offentliga IP-adresser för grundläggande har inte stöd för tillgänglighetszoner. **Standard** SKU: En offentlig IP-adress för standard-SKU kan associeras med en virtuell dator eller en lastbalanserare på frontsidan. Om du skapar en offentlig IP-adress i en  region som stöder tillgänglighetszoner är inställningen Tillgänglighetszon inställd på *Zonredundant* som standard. Mer information om tillgänglighetszoner finns i inställningen **Tillgänglighetszon.** Standard-SKU:n krävs om du associerar adressen med en standardlastbalanserare. Mer information om standardlastbalanserare finns i [Standard-SKU för Azure Load Balancer.](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](./network-security-groups-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.|
   |Nivå|Yes|Anger om IP-adressen är associerad med en region (**Regional**) eller är "anycast" från flera regioner (**Global**). *Observera att en "global nivå"-IP är* förhandsversionsfunktioner för standard-IP-adresser och för närvarande endast används för Load Balancer .|
   |Name|Yes|Namnet måste vara unikt inom den resursgrupp som du väljer.|
   |Tilldelning av IP-adress|Yes|**Dynamisk:** Dynamiska adresser tilldelas endast efter att en offentlig IP-adress har associerats med en Azure-resurs och resursen startas för första gången. Dynamiska adresser kan ändras om de är tilldelade till en resurs, till exempel en virtuell dator, och den virtuella datorn stoppas (frisallokeras) och sedan startas om. Adressen förblir densamma om en virtuell dator startas om eller stoppas (men inte frisallokeras). Dynamiska adresser släpps när en offentlig IP-adressresurs kopplas bort från en resurs som den är associerad med. **Statisk:** Statiska adresser tilldelas när en offentlig IP-adress skapas. Statiska adresser frisläpps inte förrän en offentlig IP-adressresurs tas bort. Om adressen inte är associerad med en resurs kan du ändra tilldelningsmetoden när adressen har skapats. Om adressen är associerad med en resurs kanske du inte kan ändra tilldelningsmetoden. Om du väljer *IPv6* som **IP-version** måste tilldelningsmetoden vara *Dynamisk för* Grundläggande SKU.  Standard-SKU-adresser *är statiska* för både IPv4 och IPv6. |
   |Tidsgräns för inaktivitet (minuter)|No|Hur många minuter en TCP- eller HTTP-anslutning ska vara öppen utan att klienterna behöver skicka keep-alive-meddelanden. Om du väljer IPv6 **som IP-version** kan det här värdet inte ändras. |
   |DNS-namnetikett|No|Måste vara unikt på den Azure-plats som du skapar namnet i (för alla prenumerationer och alla kunder). Azure registrerar automatiskt namn och IP-adress i dess DNS så att du kan ansluta till en resurs med namnet. Azure lägger till ett standardundernät, till exempel *location.cloudapp.azure.com* (där platsen är den plats du väljer) till det namn du anger för att skapa det fullständigt kvalificerade DNS-namnet. Om du väljer att skapa båda adressversionerna tilldelas samma DNS-namn till både IPv4- och IPv6-adresserna. Azures standard-DNS innehåller både IPv4 A- och IPv6 AAAA-namnposter och svarar med båda posterna när DNS-namnet har sökts upp. Klienten väljer vilken adress (IPv4 eller IPv6) som ska kommunicera med. Istället för att använda DNS-namnetiketten med standardsuffixet, eller som ett tillägg till det, kan du använda Azure DNS-tjänsten för att konfigurera ett DNS-namn med ett anpassat suffix som motsvarar den offentliga IP-adressen. Mer information finns i [Use Azure DNS with an Azure public IP address](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) (Använda Azure DNS med en offentlig IP-adress för Azure).|
   |Namn (visas endast om du väljer IP-version för **båda**)|Ja, om du väljer IP-version för **båda**|Namnet måste vara ett annat än det namn som du anger för det första **namnet i** den här listan. Om du väljer att skapa både en IPv4- och en IPv6-adress skapar portalen två separata offentliga IP-adressresurser, en med varje tilldelad IP-adressversion.|
   |IP-adresstilldelning (visas endast om du väljer IP-version av **båda**)|Ja, om du väljer IP-version av **båda**|Samma begränsningar som IP-adresstilldelning ovan|
   |Prenumeration|Yes|Måste finnas i samma [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) som den resurs som du ska associera de offentliga IP-adresserna med.|
   |Resursgrupp|Yes|Kan finnas i samma eller en annan [resursgrupp som](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) den resurs som du associerar de offentliga IP-adresserna med.|
   |Plats|Ja|Måste finnas på samma [plats](https://azure.microsoft.com/regions), kallas även region, som den resurs som du ska associera de offentliga IP-adresserna med.|
   |Tillgänglighetszon| No | Den här inställningen visas bara om du väljer en plats som stöds. En lista över platser som stöds finns i Översikt [över tillgänglighetszoner.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Om du  har valt Basic-SKU:n *väljs* Ingen automatiskt åt dig. Om du föredrar att garantera en specifik zon kan du välja en specifik zon. Endera alternativet är inte zonredundant. Om du  har valt Standard-SKU: Zonredundant väljs automatiskt åt dig och gör din datasökväg motståndskraftig mot zonfel. Om du föredrar att garantera en specifik zon, som inte är motståndskraftig mot zonfel, kan du välja en specifik zon.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Visa, ändra inställningar för eller ta bort en offentlig IP-adress

   - **Visa/lista:** Om du vill granska inställningarna för en offentlig IP-adress, inklusive SKU, adress, alla tillämpliga associationer (t.ex. NIC för virtuell dator, Load Balancer-serversidan).
   - **Ändra**: Ändra inställningarna med hjälp av informationen i steg 4 i skapa en offentlig [IP-adress,](#create-a-public-ip-address)till exempel tidsgräns för inaktivitet, DNS-namnetikett eller tilldelningsmetod.  (En fullständig process för att uppgradera en offentlig IP-SKU från Basic till Standard finns i [Uppgradera offentliga IP-adresser i Azure.)](./virtual-network-public-ip-address-upgrade.md)
   >[!WARNING]
   >Om du vill ändra tilldelningen för en offentlig IP-adress från statisk till dynamisk måste du först ta bort adressen från alla tillämpliga IP-konfigurationer (se **avsnittet Ta** bort).  Observera också att när du ändrar tilldelningsmetoden från statisk till dynamisk förlorar du IP-adressen som tilldelades till den offentliga IP-adressen. Azures offentliga DNS-servrar har en mappning mellan statiska eller dynamiska adresser och dns-namnetiketter (om du har definierat en sådan), men en dynamisk IP-adress kan ändras när den virtuella datorn startas efter att ha varit i stoppat (frisallokerat) tillstånd. Om du vill förhindra att adressen ändras tilldelar du en statisk IP-adress.
   
|Åtgärd|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Visa | I avsnittet **Översikt** för en offentlig IP-adress |[Get-AzPublicIpAddress för](/powershell/module/az.network/get-azpublicipaddress) att hämta ett offentligt IP-adressobjekt och visa dess inställningar| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) to show settings|
|Lista | Under kategorin **Offentliga IP-adresser** |[Get-AzPublicIpAddress för](/powershell/module/az.network/get-azpublicipaddress) att hämta ett eller flera offentliga IP-adressobjekt och visa dess inställningar|[az network public-ip list to](/cli/azure/network/public-ip#az_network_public_ip_list) list public IP addresses|
|Ändra | För en IP-adress som är  fråncierad väljer du Konfiguration för att ändra tidsgränsen för inaktivitet, DNS-namnetikett eller ändra tilldelningen av grundläggande IP-adress från Statisk till Dynamisk  |[Set-AzPublicIpAddress för](/powershell/module/az.network/set-azpublicipaddress) att uppdatera inställningarna |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) to update |

   - **Ta** bort: Borttagning av offentliga IP-adresser kräver att det offentliga IP-objektet inte är associerat med någon IP-konfiguration eller nätverkskort för virtuell dator. Se tabellen nedan för mer information.

|Resurs|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Virtuell dator](./remove-public-ip-address-vm.md)|Välj **Ta bort om du** vill ta bort IP-adressen från nätverkskortkonfigurationen och välj sedan Ta **bort.**|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) för att ta bort IP-adressen från NIC-konfigurationen. [Remove-AzPublicIpAddress att](/powershell/module/az.network/remove-azpublicipaddress) ta bort|[az network public-ip update --remove](/cli/azure/network/public-ip#az_network_public_ip_update) för att ta bort IP-adressen från NIC-konfigurationen; [az network public-ip delete to](/cli/azure/network/public-ip#az_network_public_ip_delete) delete |
|Load Balancer-frontend | Gå till en oanvänd  offentlig IP-adress och välj Associera och välj Load Balancer med relevant frontend-IP-konfiguration för att ersätta den (sedan kan den gamla IP-adressen tas bort med samma metod som för den virtuella datorn)  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) för att associera ny IP-konfiguration för frontend med offentliga Load Balancer; [Remove-AzPublicIpAddress att](/powershell/module/az.network/remove-azpublicipaddress) ta bort; kan också använda [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) för att ta bort IP-konfiguration för frontend om det finns fler än en |[az network lb frontend-ip update to](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) associate new Frontend IP config with Public Load Balancer; [Remove-AzPublicIpAddress att](/powershell/module/az.network/remove-azpublicipaddress) ta bort; kan också använda [az network lb frontend-ip delete för](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) att ta bort IP-konfiguration för frontend om det finns fler än en|
|Brandvägg|Ej tillämpligt| [Deallocate() för](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) att frisöka brandväggen och ta bort alla IP-konfigurationer | [az network firewall ip-config delete](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) to remove IP (but must use PowerShell to deallocate first)|

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

När du använder en VM-skalningsuppsättning med offentliga IP-adresser finns det inte separata offentliga IP-objekt som är associerade med enskilda instanser av virtuella datorer. Ett offentligt IP-prefixobjekt kan [dock användas för att generera instans-IP-adresserna](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Om du vill visa en lista över offentliga IP-adresser på en VM-skalningsuppsättning kan du använda PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName)](/powershell/module/az.network/get-azpublicipaddress)eller CLI ([az vmss list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips)).

Mer information finns i [Nätverk för skalningsuppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Tilldela en offentlig IP-adress

Lär dig hur du tilldelar en offentlig IP-adress till följande resurser:

- En [virtuell Windows-](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [eller Linux-dator](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (när den skapas) eller till en befintlig [virtuell dator](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Offentliga Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Plats-till-plats-anslutning med en VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Vm-skalningsuppsättning](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Behörigheter

Om du vill utföra uppgifter på offentliga IP-adresser måste ditt [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) konto tilldelas till nätverksdeltagarerollen eller till en anpassad roll som har tilldelats lämpliga åtgärder som anges i följande tabell: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

| Action                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Läsa en offentlig IP-adress                                          |
| Microsoft.Network/publicIPAddresses/write                          | Skapa eller uppdatera en offentlig IP-adress                           |
| Microsoft.Network/publicIPAddresses/delete                         | Ta bort en offentlig IP-adress                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associera en offentlig IP-adress med en resurs                    |

## <a name="next-steps"></a>Nästa steg

- Skapa en offentlig IP-adress med [powershell-](powershell-samples.md) eller [Azure CLI-exempelskript](cli-samples.md) eller med hjälp av Azure [Resource Manager mallar](template-samples.md)
- Skapa och tilldela [Azure Policy för](./policy-reference.md) offentliga IP-adresser
