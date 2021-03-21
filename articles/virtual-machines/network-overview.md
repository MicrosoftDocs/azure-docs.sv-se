---
title: Virtuella nätverk
description: Lär dig om nätverk som det är kopplat till att skapa virtuella Linux-datorer i Azure.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 20cc67d5c6436d7c0f44071509e13af324a88eea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578865"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Virtuella nätverk och virtuella datorer i Azure 

När du skapar en virtuell Azure-dator (VM) måste du skapa ett [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) (VNet) eller använda ett befintligt VNet. Du måste också bestämma hur dina virtuella datorer är avsedda att användas på VNet. Det är viktigt att [planera innan du skapar resurser](../virtual-network/virtual-network-vnet-plan-design-arm.md) och att säkerställa att du förstår [begränsningarna för nätverksresurser](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

I följande bild visas virtuella datorer som webb- och databasservrar. Varje uppsättning virtuella datorer är tilldelade separata undernät i VNet.

![Azure-virtuellt nätverk](./media/virtual-machines-common-network-overview/vnetoverview.png)

Du kan skapa ett VNet innan du skapar en virtuell dator eller så kan du skapa en virtuell dator. Du skapar dessa resurser för att stödja kommunikation med en virtuell dator:

- Nätverksgränssnitt
- IP-adresser
- Virtuellt nätverk och undernät

Utöver dessa grundläggande resurser bör du även överväga dessa valfria resurser:

- Nätverkssäkerhetsgrupper
- Lastbalanserare 

## <a name="network-interfaces"></a>Nätverksgränssnitt

Ett [nätverks gränssnitt (NIC)](../virtual-network/virtual-network-network-interface.md) är sammanlänkningen mellan en virtuell dator och ett virtuellt nätverk (VNet). En virtuell dator måste ha minst ett NIC, men kan ha flera, beroende på storleken på den virtuella dator som du skapar. Läs om hur många nätverkskort varje VM-storlek stöder, se [VM-storlekar](sizes.md).

Du kan skapa en virtuell dator med flera NIC samt lägga till eller ta bort NIC i hela livscykeln för en virtuell dator. Flera NIC gör det möjligt för en virtuell dator att ansluta till olika undernät och skicka eller ta emot trafik via det mest lämpliga gränssnittet. Virtuella datorer med valfritt antal nätverksgränssnitt kan finnas i samma tillgänglighetsuppsättning, upp till det antal som stöds av VM-storleken. 

Varje NIC som är kopplat till en virtuell dator måste befinna sig på samma plats och prenumeration som den virtuella datorn. Varje NIC måste anslutas till ett VNet som finns på samma Azure-plats och i samma Azure-prenumeration som denna NIC. Du kan ändra det undernät som en virtuell dator är ansluten till efter att den har skapats, men du kan inte ändra det virtuella nätverket. Varje NIC som är kopplad till en virtuell dator tilldelas en MAC-adress som inte ändras förrän den virtuella datorn tas bort.

Den här tabellen anger de metoder som du kan använda för att skapa ett nätverksgränssnitt.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal | När du skapar en virtuell dator i Azure Portal skapas automatiskt ett nätverksgränssnitt åt dig (du kan inte använda ett NIC som du skapar separat). Portalen skapar en virtuell dator med bara ett NIC. Om du vill skapa en virtuell dator med mer än ett NIC måste du skapa det med en annan metod. |
| [Azure PowerShell](./windows/multiple-nics.md) | Använd [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) med parametern **-PublicIpAddressId** för att ange identifieraren för den offentliga IP-adress som du skapade tidigare. |
| [Azure CLI](./linux/multiple-nics.md) | Om du vill ange identifieraren för den offentliga IP-adress som du skapade tidigare använder du [AZ Network NIC Create](/cli/azure/network/nic) med parametern **--Public-IP-Address** . |
| [Mall](../virtual-network/template-samples.md) | Använd [Nätverksgränssnitt i ett virtuellt nätverk med offentliga IP-adresser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) som en vägledning för att distribuera ett nätverksgränssnitt med en mall. |

## <a name="ip-addresses"></a>IP-adresser 

Du kan tilldela dessa typer av [IP-adresser](../virtual-network/public-ip-addresses.md) till ett NIC i Azure:

- **Offentliga IP-adresser** – Används för att kommunicera inkommande och utgående (utan nätverksadressöversättning (NAT)) med Internet och andra Azure-resurser som inte är anslutna till ett VNet. Tilldelning av en offentlig IP-adress till ett NIC är valfritt. Offentliga IP-adresser har en nominell avgift och det finns ett högsta antal som kan användas per prenumeration.
- **Privata IP-adresser** – Används för kommunikation inom ett VNet, dina lokala nätverk och Internet (med NAT). Du måste tilldela minst en privat IP-adress till en virtuell Dator. Mer information om NAT i Azure finns i [Förstå utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md).

Du kan tilldela offentliga IP-adresser till virtuella datorer eller internetuppkopplade lastbalanserare. Du kan tilldela privata IP-adresser till virtuella datorer och interna lastbalanserare. Du tilldelar IP-adresser till en virtuell dator med hjälp av ett nätverksgränssnitt.

En IP-adress kan tilldelas till en resurs med två metoder: dynamisk eller statisk allokering. Standardallokeringsmetoden är dynamisk, där IP-adressen inte tilldelas när den skapas. I stället tilldelas IP-adressen när du skapar en virtuell dator eller startar en stoppad virtuell dator. IP-adressen frisläpps när du stoppar eller tar bort den virtuella datorn. 

Om du inte vill att IP-adressen för den virtuella datorn ska ändras kan du uttryckligen ange allokeringsmetoden till statisk. I så fall tilldelas en IP-adress direkt. Den frisläpps bara om du ta bort den virtuella datorn eller om du ändrar dess allokeringsmetod till dynamisk.
    
Den här tabellen anger de metoder som du kan använda för att skapa en IP-adress.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure-portalen](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Som standard är offentliga IP-adresser dynamiska och den adress som är associerad med dem kan ändras när den virtuella datorn har stoppats eller tagits bort. För att garantera att den virtuella datorn alltid använder samma offentliga IP-adress skapar du en statisk offentlig IP-adress. Som standard tilldelar portalen en dynamisk privat IP-adress till ett NIC när du skapar en virtuell dator. Du kan ändra den här IP-adressen till statisk när den virtuella datorn har skapats.|
| [Azure PowerShell](../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Du använder [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) med parametern **-AllocationMethod** som dynamisk eller statisk. |
| [Azure CLI](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Du använder [az network public-ip create](/cli/azure/network/public-ip) med parametern **--allocation-method** som dynamisk eller statisk. |
| [Mall](../virtual-network/template-samples.md) | Använd [Nätverksgränssnitt i ett virtuellt nätverk med offentliga IP-adresser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) som en vägledning för att distribuera en offentlig IP-adress med en mall. |

När du skapat en offentlig IP-adress kan du associera den med en virtuell dator genom att tilldela den ett NIC.

## <a name="virtual-network-and-subnets"></a>Virtuellt nätverk och undernät

Ett undernät är ett intervall med IP-adresser i VNet. Du kan dela upp ett VNet i flera undernät av organisations- och säkerhetsskäl. Varje NIC i en virtuell Dator är ansluten till ett undernät i ett VNet. NICs som är anslutna till undernät (samma eller olika) inom ett VNet, kan kommunicera med varandra utan övrig konfiguration.

När du installerar ett VNet anger du topologin, inklusive tillgängliga adressutrymmen och undernät. Om VNet ska anslutas till andra VNets eller lokala nätverk måste du välja adressintervall som inte överlappar varandra. IP-adresserna är privata och kan inte nås från Internet, vilket endast var sant för icke-flyttbara IP-adresser som 10.0.0.0/8, 172.16.0.0/12 eller 192.168.0.0/16. Azure behandlar nu alla adressintervall som en del av det privata VNet:ets IP-adressutrymme som endast kan nås inom VNet, inom sammankopplade VNets och från din lokala plats. 

Om du arbetar inom en organisation där någon annan ansvarar för interna nätverk ska du tala med denna person innan du väljer ditt adressutrymme. Se till att det inte finns några överlappande och att de kan ta reda på vilket utrymme du vill använda så att de inte försöker använda samma IP-adressintervall. 

Som standard finns ingen säkerhetsgräns mellan undernät, så virtuella datorer i vart och ett av dessa undernät kan kommunicera med varandra. Du kan dock konfigurera nätverkssäkerhetsgrupper (NSGs), så att du kan styra trafikflödet till och från undernät och till och från virtuella datorer. 

Den här tabellen anger de metoder som du kan använda för att skapa ett VNet och undernät.    

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure-portalen](../virtual-network/quick-create-portal.md) | Om du låter Azure skapa ett VNet när du skapar en virtuell dator är namnet en kombination av resursgruppens namn som innehåller VNet och **-vnet**. Adressutrymmet är 10.0.0.0/24, det nödvändiga undernätets namn är **standard**, och adressintervallet för undernätet är 10.0.0.0/24. |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | Du använder [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) och [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) för att skapa ett undernät och ett VNet. Du kan också använda [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) för att lägga till ett undernät i ett befintligt VNet. |
| [Azure CLI](../virtual-network/quick-create-cli.md) | Undernät och VNet skapas på samma gång. Ange en **--subnet-name**-parameter till [az network vnet create](/cli/azure/network/vnet) med namnet på undernätet. |
| Mall | Det enklaste sättet att skapa ett VNet och undernät är att hämta en befintlig mall, till exempel [Virtual Network med två undernät](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), och ändra det efter dina behov. |

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En [Nätverkssäkerhetsgrupp (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till undernät, NICs eller både och. NSG:er kan antingen associeras med undernät eller individuella NICs anslutna till ett undernät. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla virtuella datorer i det undernätet. Dessutom kan trafik till en enskild NIC begränsas genom att koppla en NSG direkt till en NIC.

NSG:er innehåller två regeluppsättningar: inkommande och utgående. En regels prioritet måste vara unik inom varje uppsättning. Varje regel har egenskaperna för protokoll, källa och målportintervall, adressprefix, trafikriktning, prioritet och behörighetstyp. 

Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar. 

När du kopplar en NSG till ett nätverkskort, tillämpas nätverksåtkomstreglerna för den NSG:n bara på det nätverkskortet. Om en NSG används för ett enda NIC på en virtuell multi-NIC-dator påverkas inte trafiken till andra NICs. Du kan koppla olika NSG:er till ett NIC (eller virtuell dator, beroende på distributionsmodell) och de undernät som ett NIC eller en virtuell dator är bunden till. Prioritering beror på trafikriktningen.

Se till att [planera](../virtual-network/virtual-network-vnet-plan-design-arm.md) dina NSGs när du planerar din virtuella datorer och VNet.

Den här tabellen anger de metoder som du kan använda för att skapa en nätverkssäkerhetsgrupp.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure-portalen](../virtual-network/tutorial-filter-network-traffic.md) | När du skapar en virtuell dator i Azure-portalen skapas en NSG automatiskt och kopplas till det NIC som portalen skapar. Namnet på denna NSG är en kombination av namnet på den virtuella datorn och **-nsg**. Denna NSG innehåller en inkommande regel med prioritet 1000, tjänst inställd på RDP, protokoll inställt på TCP, port inställd på 3389 och åtgärd inställd på Tillåt. Om du vill tillåta annan inkommande trafik till den virtuella datorn måste du lägga till ytterligare regler i NSG. |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | Använd [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) och ange den regel information som krävs. Använd [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) för att skapa NSG. Använd [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) för att konfigurera NSG för under nätet. Använd [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) för att lägga till NSG i VNet. |
| [Azure CLI](../virtual-network/tutorial-filter-network-traffic-cli.md) | Använd [az network nsg create](/cli/azure/network/nsg) för att först skapa NSG. Använd [az network nsg rule create](/cli/azure/network/nsg/rule) för att lägga till regler i NSG. Använd [az network vnet subnet update](/cli/azure/network/vnet/subnet) för att lägga till NSG i undernätet. |
| [Mall](../virtual-network/template-samples.md) | Använd [Skapa en nätverkssäkerhetsgrupp ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) som en vägledning för att distribuera en säkerhetsgrupp i nätverket med hjälp av en mall. |

## <a name="load-balancers"></a>Lastbalanserare

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) levererar hög tillgänglighet och nätverksprestanda till dina program. En lastbalanserare kan konfigureras för att [balansera inkommande internettrafik](../load-balancer/components.md#frontend-ip-configurations) till virtuella datorer eller [balansera trafik mellan virtuella datorer i ett VNet](../load-balancer/components.md#frontend-ip-configurations). En lastbalanserare kan även balansera trafik mellan lokala datorer och virtuella datorer mellan lokala nätverk eller vidarebefordra extern trafik till en specifik virtuell dator.

Lastbalanseraren mappar inkommande och utgående trafik mellan den offentlig IP-adressen och porten på lastbalanseraren och den privata IP-adressen och porten för den virtuella datorn.

Du måste också beakta dessa konfigurationselement när du skapar en lastbalanserare:

- **IP-konfiguration på klient** sidan – en belastningsutjämnare kan innehålla en eller flera klient dels-IP-adresser. Dessa IP-adresser fungerar som ingresser för trafiken.
- **Backend adresspool** – IP-adresser som är associerade med det NIC till vilket belastningen distribueras.
- **[Vidarebefordran av port](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** – definierar hur inkommande trafik flödar genom klient delens IP och distribueras till backend-IP-användningen som använder inkommande NAT-regler.
- **Lastbalanseringsregler** – Mappar en given frontend-IP och portkombination till en uppsättning med backend-IP-adresser och portkombinationer. En enskild lastbalanserare kan ha flera regler för lastbalansering. Varje regel är en kombination av en frontend-IP och port och backend-IP och port som associeras med virtuella datorer.
- **[Probes](../load-balancer/load-balancer-custom-probe-overview.md)** – Övervakar hälsotillståndet för virtuella datorer. När en avsökning inte svarar slutar lastbalanseraren att skicka nya anslutningar till den ohälsosamma virtuella datorn. Befintliga anslutningar påverkas inte och nya anslutningar skickas till felfria virtuella datorer.
- **[Utgående regler](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** – en utgående regel konfigurerar utgående NAT (Network Address Translation) för alla virtuella datorer eller instanser som identifieras av backend-poolen för standard Load Balancer som ska översättas till klient delen.

Den här tabellen anger de metoder som du kan använda för att skapa en internetuppkopplad lastbalanserare.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal |  Du kan [belastningsutjämna Internet trafik till virtuella datorer med hjälp av Azure Portal](../load-balancer/quickstart-load-balancer-standard-public-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | Om du vill ange identifieraren för den offentliga IP-adress som du skapade tidigare använder du [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) med parametern **-PublicIpAddress** . Använd [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) för att skapa konfigurationen för backend-adresspoolen. Använd [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) för att skapa inkommande NAT-regler som är associerade med den frontend-IP-konfiguration som du har skapat. Använd [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) för att skapa de avsökningar som du behöver. Använd [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) för att skapa belastnings Utjämnings konfigurationen. Använd [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) för att skapa belastningsutjämnaren.|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | Använd [az network lb create](/cli/azure/network/lb) för att skapa den första konfigurationen för lastbalanseraren. Använd [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) för att lägga till de offentliga IP-adresser som du skapade tidigare. Använd [az network lb address-pool create](/cli/azure/network/lb/address-pool) att lägga till konfigurationen av backend-adresspoolen. Använd [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) för att lägga till NAT-regler. Använd [az network lb rule create](/cli/azure/network/lb/rule) för att lägga till regler för lastbalanseraren. Använd [az network lb probe create](/cli/azure/network/lb/probe) för att lägga till avsökningar. |
| [Mall](../load-balancer/quickstart-load-balancer-standard-public-template.md) | Använd [tre virtuella datorer i en Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create) som vägledning för att distribuera en belastningsutjämnare med hjälp av en mall. |
    
Den här tabellen anger de metoder som du kan använda för att skapa en intern lastbalanserare.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal | Du kan [utjämna belastningen på intern trafik med en belastningsutjämnare i Azure Portal](../load-balancer/quickstart-load-balancer-standard-internal-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Om du vill ange en privat IP-adress i nätverks under nätet använder du [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) med parametern **-PrivateIpAddress** . Använd [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) för att skapa konfigurationen för backend-adresspoolen. Använd [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) för att skapa inkommande NAT-regler som är associerade med den frontend-IP-konfiguration som du har skapat. Använd [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) för att skapa de avsökningar som du behöver. Använd [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) för att skapa belastnings Utjämnings konfigurationen. Använd [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) för att skapa belastningsutjämnaren.|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | Använd kommandot [az network lb create](/cli/azure/network/lb) för att skapa den första konfigurationen för lastbalanseraren. För att definiera den privata IP-adressen använder du [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) med parametern **--private-ip-address** parameter. Använd [az network lb address-pool create](/cli/azure/network/lb/address-pool) att lägga till konfigurationen av backend-adresspoolen. Använd [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) för att lägga till NAT-regler. Använd [az network lb rule create](/cli/azure/network/lb/rule) för att lägga till regler för lastbalanseraren. Använd [az network lb probe create](/cli/azure/network/lb/probe) för att lägga till avsökningar.|
| [Mall](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | Använd [två virtuella datorer i en Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) som vägledning för att distribuera en belastningsutjämnare med hjälp av en mall. |

### <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för virtuella datorer

Mer information om belastningsutjämnare och skalnings uppsättningar för virtuella datorer finns i [nätverk för skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

## <a name="vms"></a>Virtuella datorer

Virtuella datorer kan skapas i samma VNet och de kan ansluta till varandra med privata IP-adresser. De kan ansluta även om de finns i olika undernät utan att en gateway måste konfigureras eller offentliga IP-adresser användas. För att placera virtuella datorer i ett VNet skapar du detta VNet, och sedan när du skapar varje virtuell dator tilldelar du den till VNet och undernät. Virtuella datorer får sina nätverksinställningar under distributionen eller starten.  

Virtuella datorer tilldelats en IP-adress när de distribueras. Om du distribuerar flera virtuella datorer i ett VNet eller ett undernät, tilldelas de IP-adresser när de startas. Du kan också allokera en statisk IP-adress till en virtuell dator. Om du allokerar en statisk IP-adress bör du överväga att använda ett speciellt undernät för att undvika att oavsiktligt återanvända en statisk IP-adress för en annan virtuell dator.  

Om du skapar en virtuell dator och vill migrera den till ett VNet är detta inte någon enkel konfigurationsändring. Du måste distribuera om den virtuella datorn till VNet. Det enklaste sättet att distribuera om är att ta bort den virtuella datorn, men inte eventuella diskar som är anslutna till den, och därefter skapa om den virtuella datorn i VNet med hjälp av de ursprungliga diskarna. 

Den här tabellen anger de metoder som du kan använda för att skapa en virtuell dator i ett VNet.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure-portalen](./windows/quick-create-portal.md) | Använder standardinställningarna för nätverk som nämndes tidigare för att skapa en virtuell dator med ett enskilt NIC. Om du vill skapa en virtuell dator med flera NICs måste du använda en annan metod. |
| [Azure PowerShell](./windows/tutorial-manage-vm.md) | Inkluderar användningen av [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) för att lägga till det nätverkskort som du skapade tidigare i VM-konfigurationen. |
| [Azure CLI](./linux/create-cli-complete.md) | Skapa och Anslut en virtuell dator till ett VNet, undernät och NIC som bygger på enskilda steg. |
| [Mall](./windows/ps-template.md) | Använd [Mycket enkel distribution av virtuella Windows-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) som en vägledning för att distribuera en virtuell dator med hjälp av en mall. |

## <a name="next-steps"></a>Nästa steg
Detaljerade anvisningar om hur du hanterar virtuella Azure-nätverk för virtuella datorer finns i [Windows](../virtual-machines/windows/tutorial-virtual-network.md) -eller [Linux](../virtual-machines/linux/tutorial-virtual-network.md) -självstudierna.

Det finns också självstudier om hur du kan belastningsutjämna virtuella datorer och skapa program med hög tillgänglighet för [Windows](../virtual-machines/windows/tutorial-load-balancer.md) eller [Linux](../virtual-machines/linux/tutorial-load-balancer.md).

- Läs mer om konfigurering av [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md). 
- Läs mer om hur du konfigurerar [VNet-till-VNet-anslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Läs mer om [Felsökningsvägar](../virtual-network/diagnose-network-routing-problem.md).
- Läs mer om [bandbredd för virtuella dator nätverk](../virtual-network/virtual-machine-network-throughput.md).