---
title: Vanliga frågor och svar om Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Svar på vanliga frågor om Microsoft Azure virtuella nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2020
ms.author: kumud
ms.openlocfilehash: 2cf28565818f6de4d52b57040a80c21d0e03a76c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218528"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ) om Azure Virtual Network

## <a name="virtual-network-basics"></a>Virtual Network grunderna

### <a name="what-is-an-azure-virtual-network-vnet"></a>Vad är ett Azure-Virtual Network (VNet)?
Ett Azure-Virtual Network (VNet) är en representation av ditt eget nätverk i molnet. Det är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan använda virtuella nätverk för att etablera och hantera virtuella privata nätverk (VPN) i Azure och, om du vill, länka virtuella nätverk med andra virtuella nätverk i Azure eller med din lokala IT-infrastruktur för att skapa hybrid lösningar eller lösningar med flera platser. Varje VNet som du skapar har ett eget CIDR-block och kan kopplas till andra virtuella nätverk och lokala nätverk så länge CIDR-blocken inte överlappar varandra. Du har också kontroll över DNS-serverinställningar för virtuella nätverk och segmentering av VNet i undernät.

Använd virtuella nätverk för att:

* Skapa ett dedikerat privat virtuellt nätverk för endast moln. Ibland behöver du inte någon lokal konfiguration för din lösning. När du skapar ett VNet kan dina tjänster och virtuella datorer i ditt VNet kommunicera direkt och säkert med varandra i molnet. Du kan fortfarande konfigurera slut punkts anslutningar för de virtuella datorer och tjänster som kräver Internet kommunikation som en del av lösningen.

* Utöka ditt data Center på ett säkert sätt. Med virtuella nätverk kan du bygga vanliga VPN för plats-till-plats (S2S) för att på ett säkert sätt skala din data Center kapacitet. S2S VPN använder IPSEC för att tillhandahålla en säker anslutning mellan din företags VPN-gateway och Azure.

* Aktivera scenarier med hybrid moln. Virtuella nätverk ger dig flexibiliteten att stödja ett antal hybrid moln scenarier. Du kan på ett säkert sätt ansluta molnbaserade program till alla typer av lokala system, till exempel stordatorer och UNIX-system.

### <a name="how-do-i-get-started"></a>Hur kommer jag igång?
Gå till [dokumentationen för det virtuella nätverket](./index.yml) för att komma igång. Det här innehållet innehåller översikts-och distributions information för alla VNet-funktioner.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Kan jag använda virtuella nätverk utan anslutning mellan platser?
Ja. Du kan använda ett VNet utan att ansluta det till ditt lokala nätverk. Du kan till exempel köra Microsoft Windows Server Active Directory domänkontrollanter och SharePoint-grupper enbart i ett Azure VNet.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Kan jag utföra en WAN-optimering mellan virtuella nätverk eller ett VNet och mitt lokala data Center?
Ja. Du kan distribuera en [virtuell nätverks installation i WAN Optimization](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) från flera leverantörer via Azure Marketplace.

## <a name="configuration"></a>Konfiguration

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Vilka verktyg använder jag för att skapa ett virtuellt nätverk?
Du kan använda följande verktyg för att skapa eller konfigurera ett VNet:

* Azure Portal
* PowerShell
* Azure CLI
* En nätverks konfigurations fil (netcfg – endast för klassisk virtuella nätverk). Se artikeln [Konfigurera ett VNet med hjälp av en nätverks konfigurations fil](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) .

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Vilka adress intervall kan jag använda i virtuella nätverk?
Vi rekommenderar att du använder de adress intervall som räknas upp i [RFC 1918](https://tools.ietf.org/html/rfc1918), som har tagits ur bruk av IETF för privata, icke-flyttbara adress utrymmen:
* 10.0.0.0-10.255.255.255 (10/8-prefix)
* 172.16.0.0-172.31.255.255 (172.16/12-prefix)
* 192.168.0.0-192.168.255.255 (192.168/16-prefix)

Andra adress utrymmen kan fungera men kan ha oönskade sido effekter.

Dessutom kan du inte lägga till följande adress intervall:
* 224.0.0.0/4 (multicast)
* 255.255.255.255/32 (sändning)
* 127.0.0.0/8 (loopback)
* 169.254.0.0/16 (länk-lokal)
* 168.63.129.16/32 (intern DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Kan jag ha offentliga IP-adresser i min virtuella nätverk?
Ja. Mer information om offentliga IP-adressintervall finns i [skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network). Offentliga IP-adresser är inte direkt tillgängliga från Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Finns det en gräns för antalet undernät i mitt VNet?
Ja. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) . Under näts adress utrymmen får inte överlappa varandra.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Finns det några begränsningar för att använda IP-adresser i dessa undernät?
Ja. Azure reserverar 5 IP-adresser i varje undernät. Detta är x. x. x. 0-x. x. x. 3 och den sista adressen i under nätet. x. x. x. 1-x. x. x är reserverad i varje undernät för Azure-tjänster.   
- x. x. x. 0: nätverks adress
- x. x. x. 1: reserverad av Azure för standard-gatewayen
- x. x. x. 2, x. x. x: reserverad av Azure för att mappa Azure DNS-IP-adresser till VNet-utrymmet
- x. x. x. 255: nätverks-broadcast-adress

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hur liten och hur stor kan virtuella nätverk och undernät vara?
Det minsta IPv4-under nätet som stöds är/29 och det största är/8 (med hjälp av CIDR-underdefinitioner).  IPv6-undernät måste vara exakt/64 i storlek.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Kan jag hämta mina VLAN till Azure med virtuella nätverk?
Nej. Virtuella nätverk är lager-3 överlägg. Azure har inte stöd för några semantik i Layer-2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Kan jag ange anpassade principer för routning på mina virtuella nätverk och undernät?
Ja. Du kan skapa en routningstabell och koppla den till ett undernät. Mer information om routning i Azure finns i [routning – översikt](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Stöder virtuella nätverk multicast eller broadcast?
Nej. Multicast och broadcast stöds inte.

### <a name="what-protocols-can-i-use-within-vnets"></a>Vilka protokoll kan jag använda i virtuella nätverk?
Du kan använda TCP-, UDP- och ICMP TCP/IP-protokoll i virtuella nätverk. Unicast stöds i virtuella nätverk, med undantag för Dynamic Host Configuration Protocol (DHCP) via Unicast (UDO-källport 68 och UDP-målport 67) och UDP-källport 65330 som är reserverad för värden. Multicast-, broadcast-, IP-in-IP-kapslade paket och GRE-paket (Generic Routing Encapsulation) blockeras i virtuella nätverk. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kan jag pinga mina standardrouters i ett virtuellt nätverk?
Nej.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Kan jag använda tracert för att diagnostisera anslutningen?
Nej.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Kan jag lägga till undernät efter att VNet har skapats?
Ja. Undernät kan läggas till i virtuella nätverk när som helst så länge som under nätets adress intervall inte ingår i ett annat undernät och det finns tillgängligt utrymme kvar i det virtuella nätverkets adress intervall.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kan jag ändra storleken på mitt undernät när jag har skapat det?
Ja. Du kan lägga till, ta bort, expandera eller krympa ett undernät om inga virtuella datorer eller tjänster är distribuerade i det.

### <a name="can-i-modify-vnet-after-i-created-them"></a>Kan jag ändra VNet när jag har skapat dem?
Ja. Du kan lägga till, ta bort och ändra CIDR-block som används av ett VNet.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Kan jag ansluta till Internet om jag kör mina tjänster i ett virtuellt nätverk?
Ja. Alla tjänster som distribueras inom ett VNet kan ansluta utgående till Internet. Mer information om utgående Internet anslutningar i Azure finns i [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill ansluta inkommande till en resurs som distribueras via Resource Manager, måste resursen ha en tilldelad offentlig IP-adress. Mer information om offentliga IP-adresser finns i [offentliga IP-adresser](virtual-network-public-ip-address.md). Alla Azure-Molntjänster som distribueras i Azure har en offentligt adresserad VIP som tilldelats till den. Du definierar ingångs slut punkter för PaaS-roller och slut punkter för virtuella datorer så att dessa tjänster kan acceptera anslutningar från Internet.

### <a name="do-vnets-support-ipv6"></a>Stöder virtuella nätverk IPv6?
Ja, virtuella nätverk kan vara enbart IPv4-eller Dual Stack (IPv4 + IPv6).  Mer information finns i [Översikt över IPv6 för virtuella Azure-nätverk](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Kan ett VNet-intervall regioner användas?
Nej. Ett VNet är begränsat till en enda region. Ett virtuellt nätverk gör dock att du kan spänna över tillgänglighets zoner. Mer information om tillgänglighetszoner finns i [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Du kan ansluta virtuella nätverk i olika regioner med peering av virtuella nätverk. Mer information finns i [Översikt över virtuell nätverks-peering](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kan jag ansluta ett VNet till ett annat VNet i Azure?
Ja. Du kan ansluta ett VNet till ett annat VNet med hjälp av något av följande:
- **Peering för virtuellt nätverk**: Mer information finns i [Översikt över VNet-peering](virtual-network-peering-overview.md)
- **En Azure-VPN gateway**: Mer information finns i [Konfigurera en VNet-till-VNET-anslutning](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Namn matchning (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Vilka är mina DNS-alternativ för virtuella nätverk?
Använd besluts tabellen på sidan [namn matchning för virtuella datorer och roll instanser](virtual-networks-name-resolution-for-vms-and-role-instances.md) för att vägleda dig genom alla DNS-alternativ som är tillgängliga.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Kan jag ange DNS-servrar för ett VNet?
Ja. Du kan ange DNS-serverns IP-adresser i VNet-inställningarna. Inställningen tillämpas som standard-DNS-servrar för alla virtuella datorer i VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Hur många DNS-servrar kan jag ange?
Referens för [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Kan jag ändra mina DNS-servrar när jag har skapat nätverket?
Ja. Du kan när som helst ändra listan över DNS-servrar för ditt VNet. Om du ändrar listan över DNS-servrar måste du utföra en förnyelse av DHCP-lån på alla virtuella datorer som påverkas i VNet för att de nya DNS-inställningarna ska börja gälla. För virtuella datorer som kör Windows OS kan du göra detta genom att skriva `ipconfig /renew` direkt på den virtuella datorn. Information om andra operativ system typer finns i dokumentationen för DHCP-lånet för den angivna OS-typen. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Vad är Azure-tillhandahållit DNS och fungerar det med virtuella nätverk?
Azure-tillhandahöll DNS är en DNS-tjänst för flera innehavare som erbjuds av Microsoft. Azure registrerar alla virtuella datorer och moln tjänst roll instanser i den här tjänsten. Den här tjänsten tillhandahåller namn matchning efter värdnamn för virtuella datorer och roll instanser i samma moln tjänst och av FQDN för virtuella datorer och roll instanser i samma VNet. Mer information om DNS finns i [namn matchning för virtuella datorer och Cloud Services roll instanser](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Det finns en begränsning för de första 100 moln tjänsterna i ett VNet för namn matchning mellan klienter med hjälp av Azure-DNS. Om du använder en egen DNS-Server gäller inte den här begränsningen.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Kan jag åsidosätta mina DNS-inställningar per virtuell dator eller moln tjänst?
Ja. Du kan ställa in DNS-servrar per virtuell dator eller moln tjänst för att åsidosätta standardinställningarna för nätverket. Vi rekommenderar dock att du använder DNS i hela nätverket så mycket som möjligt.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kan jag hämta mitt eget DNS-suffix?
Nej. Du kan inte ange ett anpassat DNS-suffix för din virtuella nätverk.

## <a name="connecting-virtual-machines"></a>Ansluta virtuella datorer

### <a name="can-i-deploy-vms-to-a-vnet"></a>Kan jag distribuera virtuella datorer till ett virtuellt nätverk?
Ja. Alla nätverks gränssnitt (NIC) som är anslutna till en virtuell dator som distribueras via Resource Manager-distributions modellen måste vara anslutna till ett VNet. Virtuella datorer som distribueras via den klassiska distributions modellen kan också anslutas till ett VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Vilka är de olika typerna av IP-adresser jag kan tilldela till virtuella datorer?
* **Privat:** Tilldelas varje nätverkskort i varje virtuell dator. Adressen tilldelas antingen med den statiska eller dynamiska metoden. Privata IP-adresser tilldelas från det intervall som du har angett i under näts inställningarna för ditt VNet. Resurser som distribueras via den klassiska distributions modellen tilldelas privata IP-adresser, även om de inte är anslutna till ett VNet. Metoden för allokeringsmetod är olika beroende på om en resurs har distribuerats med Resource Manager eller den klassiska distributions modellen: 

  - **Resource Manager**: en privat IP-adress som tilldelats med den dynamiska eller statiska metoden förblir tilldelad till en virtuell dator (Resource Manager) tills resursen har tagits bort. Skillnaden är att du väljer den adress som ska tilldelas när du använder statisk, och Azure väljer när du använder dynamisk. 
  - **Klassisk**: en privat IP-adress som tilldelats med den dynamiska metoden kan ändras när en virtuell dator (klassisk) startas om efter att ha varit i läget Stoppad (Frigjord). Om du behöver se till att den privata IP-adressen för en resurs som distribueras via den klassiska distributions modellen aldrig ändras, tilldelar du en privat IP-adress med den statiska metoden.

* **Offentlig:** Kan tilldelas till nätverkskort som är anslutna till virtuella datorer som distribueras via Azure Resource Manager distributions modell. Adressen kan tilldelas med metoden för statisk eller dynamisk allokering. Alla virtuella datorer och Cloud Services roll instanser som distribueras via den klassiska distributions modellen finns i en moln tjänst, som tilldelas en *dynamisk*, offentlig virtuell IP-adress (VIP). En offentlig *statisk* IP-adress, som kallas en [reserverad IP-adress](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip), kan alternativt tilldelas som VIP. Du kan tilldela offentliga IP-adresser till enskilda virtuella datorer eller Cloud Services roll instanser som distribueras via den klassiska distributions modellen. Dessa adresser kallas [offentliga IP-adresser på instans nivå (ILPIP-](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) adresser och kan tilldelas dynamiskt).

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kan jag reservera en privat IP-adress för en virtuell dator som jag ska skapa vid ett senare tillfälle?
Nej. Du kan inte reservera en privat IP-adress. Om en privat IP-adress är tillgänglig, tilldelas den en virtuell dator eller roll instans av DHCP-servern. Den virtuella datorn kanske inte är den som du vill att den privata IP-adressen är tilldelad till. Du kan dock ändra den privata IP-adressen för en redan skapad virtuell dator till valfri tillgänglig privat IP-adress.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Ändras privata IP-adresser för virtuella datorer i ett virtuellt nätverk?
Det beror på. Om den virtuella datorn har distribuerats via Resource Manager, oavsett om IP-adressen har tilldelats en statisk eller dynamisk tilldelnings metod. Om den virtuella datorn har distribuerats via den klassiska distributions modellen kan dynamiska IP-adresser ändras när en virtuell dator startas efter att ha varit i läget Stoppad (Frigjord). Adressen släpps från en virtuell dator som distribueras via distributions modellen när den virtuella datorn tas bort.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Kan jag manuellt tilldela IP-adresser till nätverkskort i den virtuella datorns operativ system?
Ja, men det rekommenderas inte om det behövs, till exempel när du tilldelar flera IP-adresser till en virtuell dator. Mer information finns i [lägga till flera IP-adresser till en virtuell dator](virtual-network-multiple-ip-addresses-portal.md#os-config). Om IP-adressen som tilldelats till ett Azure-nätverkskort som är kopplad till en virtuell dator ändras och IP-adressen i den virtuella datorns operativ system är annorlunda förlorar du anslutningen till den virtuella datorn.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Vad händer med mina IP-adresser om jag stoppar en distributions plats för moln tjänster eller stänger av en virtuell dator i operativ systemet?
Ingenstans. IP-adresserna (offentliga VIP, offentliga och privata) förblir tilldelade till moln tjänst distributions facket eller den virtuella datorn.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Kan jag flytta virtuella datorer från ett undernät till ett annat undernät i ett VNet utan att distribuera om?
Ja. Du hittar mer information i så [här flyttar du en virtuell dator eller roll instans till en annan under näts](/previous-versions/azure/virtual-network/virtual-networks-move-vm-role-to-subnet) artikel.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kan jag konfigurera en statisk MAC-adress för min virtuella dator?
Nej. En MAC-adress kan inte konfigureras statiskt.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Är MAC-adressen densamma för min virtuella dator när den har skapats?
Ja, MAC-adressen är densamma för en virtuell dator som distribueras via både resurs hanteraren och klassiska distributions modeller tills den tas bort. Tidigare släpptes MAC-adressen om den virtuella datorn stoppades (frigjordes), men nu behålls MAC-adressen även om den virtuella datorn är i ett friallokerat tillstånd. MAC-adressen är tilldelad till nätverks gränssnittet tills nätverks gränssnittet har tagits bort eller så har den privata IP-adress som tilldelats den primära IP-konfigurationen för det primära nätverks gränssnittet ändrats. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kan jag ansluta till Internet från en virtuell dator i ett virtuellt nätverk?
Ja. Alla virtuella datorer och Cloud Services roll instanser som distribueras i ett VNet kan ansluta till Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-tjänster som ansluter till virtuella nätverk

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Kan jag använda Azure App Service Web Apps med ett VNet?
Ja. Du kan distribuera Web Apps inuti ett VNet med hjälp av en ASE (App Service-miljön), ansluta Server delen till din virtuella nätverk med VNet-integrering och låsa inkommande trafik till din app med tjänst slut punkter. Mer information finns i följande artiklar:

* [App Service nätverksfunktioner](../app-service/networking-features.md)
* [Skapa Web Apps i en App Service-miljön](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrera din app med en Azure-Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Begränsningar för App Service åtkomst](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Kan jag distribuera Cloud Services med Web-och Worker-roller (PaaS) i ett VNet?
Ja. Du kan också (om du vill) distribuera Cloud Services roll instanser i virtuella nätverk. För att göra det anger du VNet-namnet och roll-och under näts mappningarna i avsnittet nätverks konfiguration i tjänst konfigurationen. Du behöver inte uppdatera någon av dina binärfiler.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Kan jag ansluta en skalnings uppsättning för virtuell dator till ett virtuellt nätverk?
Ja. Du måste ansluta en skalnings uppsättning för virtuell dator till ett VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Finns det en fullständig lista över Azure-tjänster som kan jag distribuera resurser från i ett virtuellt nätverk?
Ja, mer information finns i [integrering av virtuella nätverk för Azure-tjänster](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Hur kan jag begränsa åtkomsten till Azure PaaS-resurser från ett virtuellt nätverk?

Resurser som distribueras via vissa Azure PaaS-tjänster (till exempel Azure Storage och Azure SQL Database) kan begränsa nätverks åtkomsten till VNet genom att använda tjänst slut punkter för virtuella nätverk eller Azures privata länk. Mer information finns i Översikt [över tjänst slut punkter för virtuella nätverk](virtual-network-service-endpoints-overview.md), [Översikt över Azure privat länk](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Kan jag flytta mina tjänster in och ut ur virtuella nätverk?
Nej. Du kan inte flytta tjänster in och ut ur virtuella nätverk. Om du vill flytta en resurs till ett annat VNet måste du ta bort och distribuera om resursen.

## <a name="security"></a>Säkerhet

### <a name="what-is-the-security-model-for-vnets"></a>Vad är säkerhets modellen för virtuella nätverk?
Virtuella nätverk isoleras från varandra och andra tjänster som finns i Azure-infrastrukturen. Ett VNet är en förtroende gränser.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Kan jag begränsa inkommande eller utgående trafik flöde till VNet-anslutna resurser?
Ja. Du kan använda [nätverks säkerhets grupper](./network-security-groups-overview.md) för enskilda undernät i ett VNet, nätverkskort som är kopplade till ett VNet eller både och.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Kan jag implementera en brand vägg mellan VNet-anslutna resurser?
Ja. Du kan distribuera en [brand vägg för virtuella brand väggar](https://azure.microsoft.com/marketplace/?term=firewall) från flera leverantörer via Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Finns det någon information om att skydda virtuella nätverk?
Ja. Mer information finns i [Översikt över Azure Network Security](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="do-virtual-networks-store-customer-data"></a>Lagrar virtuella nätverk kund information?
Nej. Virtuella nätverk lagrar ingen kund information. 

## <a name="apis-schemas-and-tools"></a>API: er, scheman och verktyg

### <a name="can-i-manage-vnets-from-code"></a>Kan jag hantera virtuella nätverk från kod?
Ja. Du kan använda REST-API: er för virtuella nätverk i [Azure Resource Manager](/rest/api/virtual-network) och [klassiska](/previous-versions/azure/ee460799(v=azure.100)) distributions modeller.

### <a name="is-there-tooling-support-for-vnets"></a>Finns det stöd för verktyg för virtuella nätverk?
Ja. Lär dig mer om att använda:
- Azure Portal att distribuera virtuella nätverk via [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) och [klassiska](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) distributions modeller.
- PowerShell för att hantera virtuella nätverk som distribueras via [Resource Manager](/powershell/module/az.network) och [klassiska](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0) distributions modeller.
- Kommando rads gränssnittet för Azure (CLI) för att distribuera och hantera virtuella nätverk som distribueras via [Resource Manager](/cli/azure/network/vnet) och [klassiska](/previous-versions/azure/virtual-machines/azure-cli-arm-commands?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) distributions modeller.  

## <a name="vnet-peering"></a>VNet-peering

### <a name="what-is-vnet-peering"></a>Vad är VNet-peering?
Med VNet-peering (eller virtuell nätverks-peering) kan du ansluta virtuella nätverk. Med en VNet-peering-anslutning mellan virtuella nätverk kan du dirigera trafik mellan dem privat via IPv4-adresser. Virtuella datorer i peer-virtuella nätverk kan kommunicera med varandra som om de befinner sig i samma nätverk. De här virtuella nätverken kan finnas i samma region eller i olika regioner (även kallade global VNet-peering). VNet-peering-anslutningar kan också skapas mellan Azure-prenumerationer.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Kan jag skapa en peering-anslutning till ett VNet i en annan region?
Ja. Med global VNet-peering kan du peer-virtuella nätverk i olika regioner. Global VNet-peering är tillgänglig i alla offentliga Azure-regioner, i Kina och i moln regioner i myndigheter. Du kan inte globalt peer-koppla från offentliga Azure-regioner till nationella moln regioner.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Vilka är begränsningarna relaterade till globala VNet-peering och belastningsutjämnare?
Om de två virtuella nätverken i två olika regioner är peer-kopplade via global VNet-peering, kan du inte ansluta till resurser som ligger bakom en grundläggande Load Balancer via klient delens IP-adress för Load Balancer. Den här begränsningen finns inte för en Standard Load Balancer.
Följande resurser kan använda grundläggande belastningsutjämnare, vilket innebär att du inte kan komma åt dem via Load Balancerens klient del IP över global VNet-peering. Du kan dock använda global VNet-peering för att komma åt resurserna direkt via sina privata VNet-IP: er, om det är tillåtet. 
- Virtuella datorer bakom Basic Load Balancer
- Skalnings uppsättningar för virtuella datorer med Basic Load Balancer 
- Redis Cache 
- SKU för Application Gateway (v1)
- Service Fabric
- API Management
- Active Directory-domän tjänst (lägger till)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service Environment

Du kan ansluta till dessa resurser via ExpressRoute eller VNet-till-VNet via VNet-gatewayer.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Kan jag aktivera VNet-peering om mina virtuella nätverk tillhör prenumerationer inom olika Azure Active Directory klienter?
Ja. Det går att etablera VNet-peering (oavsett om den är lokal eller global) om prenumerationerna tillhör olika Azure Active Directory klienter. Du kan göra detta via portalen, PowerShell eller CLI.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Mitt VNet-peering-anslutning har *initierats* , varför kan jag inte ansluta?
Om peering-anslutningen är i ett *initierat* tillstånd innebär det att du bara har skapat en länk. En dubbelriktad länk måste skapas för att en anslutning ska kunna upprättas. Till exempel, för peer-VNet A till VNet B, måste en länk skapas från VNetA till VNetB och från VNetB till VNetA. Om du skapar båda länkarna ändras statusen till *ansluten*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Mitt VNet-peering-anslutning är i *frånkopplat* tillstånd, varför kan jag inte skapa en peering-anslutning?
Om din VNet-peering-anslutning är i *frånkopplat* läge, innebär det att en av de länkar som skapats har tagits bort. För att återupprätta en peering-anslutning måste du ta bort länken och återskapa den.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Kan jag peer-mitt VNet med ett VNet i en annan prenumeration?
Ja. Du kan peer-virtuella nätverk över prenumerationer och i flera regioner.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Kan jag peer-koppla två virtuella nätverk med matchande eller överlappande adress intervall?
Nej. Adress utrymmen får inte överlappa för att aktivera VNet-peering.

### <a name="can-i-peer-a-vnet-to-two-different-vnets-with-the-the-use-remote-gateway-option-enabled-on-both-the-peerings"></a>Kan jag peer-koppla ett VNet till två olika virtuella nätverk med alternativet "Använd fjärran sluten Gateway" aktiverat på båda peer-kopplingarna?
Nej. Du kan bara aktivera alternativet Använd fjärran sluten Gateway för en peering till en av virtuella nätverk.

### <a name="how-much-do-vnet-peering-links-cost"></a>Hur mycket kostar VNet-peering länkar?
Det kostar inget att skapa en VNet-peering-anslutning. Data överföring mellan peering-anslutningar debiteras. [Se här](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Är VNet-peering-trafiken krypterad?
När Azure-trafik flyttas mellan data Center (utanför fysiska gränser som inte styrs av Microsoft eller på uppdrag av Microsoft), används [MACsec data länk skikt kryptering](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) på den underliggande nätverks maskin varan.  Detta gäller för VNet-peering-trafik.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Varför är min peering-anslutning i *frånkopplat* läge?
VNet-peering-anslutningar övergår i *frånkopplat* tillstånd när en VNet-peering-länk tas bort. Du måste ta bort båda länkarna för att återupprätta en lyckad peer kopplings anslutning.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Om jag peer-VNetA till VNetB och I peer-VNetB till VNetC, så innebär det att VNetA och VNetC är peer-datorer?
Nej. Transitiv peering stöds inte. Du måste peer-VNetA och VNetC för att det ska gå att genomföra.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Finns det några bandbredds begränsningar för peering-anslutningar?
Nej. VNet-peering, oavsett om den är lokal eller global, tillämpar inga bandbredds begränsningar. Bandbredd begränsas endast av den virtuella datorn eller beräknings resursen.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Hur kan jag Felsöka problem med VNet-peering?
Här är en [fel söknings guide](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) som du kan prova.

## <a name="virtual-network-tap"></a>Virtual Network TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Vilka Azure-regioner är tillgängliga för virtuella nätverk trycker du på?
För hands versionen av virtuellt nätverk är tillgänglig i alla Azure-regioner. De övervakade nätverks gränssnitten, det virtuella nätverket, trycker på resurs och insamlaren eller analys lösningen måste distribueras i samma region.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>TRYCKER Virtual Network stöd för alla filtrerings funktioner på de speglade paketen?
Filtrerings funktioner stöds inte med det virtuella nätverket. Tryck på Förhandsgranska. När en KNACKNINGs konfiguration läggs till i ett nätverks gränssnitt, strömmas en djupgående kopia av alla ingångs-och utgående trafik i nätverks gränssnittet till tryck målet.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Kan flera tryck på konfigurationer läggas till i ett övervakat nätverks gränssnitt?
Ett övervakat nätverks gränssnitt kan bara ha en enda KNACKNING-konfiguration. Kontrol lera med den enskilda [partner lösningen](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) att kunna strömma flera kopior av trafiktrycks trafiken till de analys verktyg du väljer.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Kan samma virtuella nätverk trycka samman resurs mängds trafik från övervakade nätverks gränssnitt i mer än ett virtuellt nätverk?
Ja. Samma virtuella nätverk tryck på resurs kan användas för att sammanställa speglad trafik från övervakade nätverks gränssnitt i peer-kopplat virtuella nätverk i samma prenumeration eller en annan prenumeration. Det virtuella nätverket trycker på resurs-och mål belastnings Utjämnings-eller mål nätverks gränssnittet måste vara i samma prenumeration. Alla prenumerationer måste vara under samma Azure Active Directory-klient.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Finns det några prestanda överväganden för produktions trafiken om jag aktiverar ett virtuellt nätverk genom att trycka på konfiguration i ett nätverks gränssnitt?

Det virtuella nätverkets tryck är i för hands version. Det finns inget service nivå avtal för för hands versionen. Kapaciteten bör inte användas för produktions arbets belastningar. När en virtuell dators nätverks gränssnitt är aktiverat med en tryck konfiguration, används samma resurser på Azure-värden som tilldelas den virtuella datorn för att skicka produktions trafiken för att utföra speglings funktionen och skicka de speglade paketen. Välj rätt storlek på den virtuella [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -datorn för att säkerställa att tillräckliga resurser är tillgängliga för den virtuella datorn för att skicka produktions trafiken och den speglade trafiken.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Stöds accelererat nätverk för [Linux](create-vm-accelerated-networking-cli.md) eller [Windows](create-vm-accelerated-networking-powershell.md) med virtuellt nätverk?

Du kommer att kunna lägga till en tryck konfiguration på ett nätverks gränssnitt som är kopplat till en virtuell dator som är aktiverat med accelererat nätverk. Men prestandan och svars tiden på den virtuella datorn kommer att påverkas genom att du lägger till tryck på konfiguration eftersom avlastning för spegling av trafik för närvarande inte stöds av Azure accelererat nätverk.

## <a name="virtual-network-service-endpoints"></a>Tjänstslutpunkter för virtuella nätverk

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Vad är rätt åtgärds ordning för att konfigurera tjänst slut punkter till en Azure-tjänst?
Det finns två steg för att skydda en Azure service-resurs via tjänst slut punkter:
1. Aktivera tjänstens slut punkter för Azure-tjänsten.
2. Konfigurera VNet ACL: er för Azure-tjänsten.

Det första steget är en åtgärd på nätverks sidan och det andra steget är en åtgärd på tjänst resurs sidan. Båda stegen kan utföras antingen av samma administratör eller olika administratörer baserat på de Azure RBAC-behörigheter som har tilldelats rollen administratör. Vi rekommenderar att du först aktiverar tjänstens slut punkter för ditt virtuella nätverk innan du konfigurerar VNet ACL: er på Azure-tjänstesidan. Därför måste stegen utföras i den ordning som anges ovan för att konfigurera VNet-tjänstens slut punkter.

>[!NOTE]
> Båda åtgärderna som beskrivs ovan måste slutföras innan du kan begränsa åtkomsten till Azure-tjänsten till det tillåtna VNet och under nätet. Att bara aktivera tjänst slut punkter för Azure-tjänsten på nätverks sidan ger dig inte begränsad åtkomst. Dessutom måste du också konfigurera VNet ACL: er på Azure-tjänstesidan.

Vissa tjänster (till exempel SQL och CosmosDB) tillåter undantag till ovanstående sekvens genom **IgnoreMissingVnetServiceEndpoint** -flaggan. När flaggan har angetts till **True** kan VNet-ACL: er anges på Azure-tjänstesidan innan du konfigurerar tjänstens slut punkter på nätverks sidan. Azure-tjänster tillhandahåller den här flaggan för att hjälpa kunder i de fall där de angivna IP-brandväggarna har kon figurer ATS på Azure-tjänster och aktivering av tjänstens slut punkter på nätverks sidan kan leda till en anslutning på grund av att käll-IP-adressen ändras från en offentlig IPv4-adress till en privat adress. Konfigurera VNet ACL: er på Azure-tjänstesidan innan du ställer in tjänst slut punkter på nätverks sidan kan hjälpa till att ta bort en anslutning.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Finns alla Azure-tjänster i det virtuella Azure-nätverket som tillhandahålls av kunden? Hur fungerar VNet-tjänstens slut punkter med Azure-tjänster?

Nej, alla Azure-tjänster finns inte i kundens virtuella nätverk. Merparten av Azures data tjänster, till exempel Azure Storage, Azure SQL och Azure Cosmos DB, är tjänster för flera innehavare som kan nås via offentliga IP-adresser. Du kan lära dig mer om integrering av virtuella nätverk för Azure-tjänster [här](virtual-network-for-azure-services.md). 

När du använder funktionen för VNet-tjänstens slut punkter (aktiverar VNet-tjänstens slut punkt på nätverks sidan och ställer in lämpliga VNet ACL: er på Azure-tjänstesidan), begränsas åtkomsten till en Azure-tjänst från ett tillåtet VNet och undernät.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Hur ger VNet-tjänstens slut punkt säkerhet?

Slut punkts funktionen för VNet-tjänsten (aktiverar VNet-tjänstens slut punkt på nätverks sidan och ställer in lämpliga VNet-ACL: er på Azure-tjänstesidan) begränsar Azure-tjänstens åtkomst till det tillåtna VNet och under nätet, vilket ger en säkerhet på nätverks nivå och isolering av Azure-tjänstetrafiken. All trafik som använder VNet-tjänstens slut punkter flödar över Microsoft-stamnät, vilket ger ett annat lager av isolering från det offentliga Internet. Dessutom kan kunderna välja att helt ta bort den offentliga Internet åtkomsten till Azure-tjänstens resurser och bara tillåta trafik från sina virtuella nätverk via en kombination av IP-brandvägg och VNet ACL: er, vilket skyddar Azure-tjänsteresurser från obehörig åtkomst.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Vad skyddar VNet-tjänstens slut punkts resurser eller Azure-tjänst?
VNet-tjänstens slut punkter hjälper till att skydda Azure-tjänstens resurser. VNet-resurser skyddas via nätverks säkerhets grupper (NSG: er).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Kostar det någon kostnad för att använda VNet-tjänstens slut punkter?

Nej, det finns ingen ytterligare kostnad för att använda VNet-tjänstens slut punkter.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Kan jag aktivera VNet-tjänstens slut punkter och konfigurera VNet ACL: er om det virtuella nätverket och Azure-tjänstens resurser tillhör olika prenumerationer?

Ja, det kan du. Virtuella nätverk och Azure Service-resurser kan vara antingen i samma eller olika prenumerationer. Det enda kravet är att både det virtuella nätverket och Azures tjänst resurser måste ligga under samma Active Directory-klient (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Kan jag aktivera VNet-tjänstens slut punkter och konfigurera VNet ACL: er om det virtuella nätverket och Azure-tjänstens resurser tillhör olika AD-klienter?
Ja, det är möjligt när du använder tjänstens slut punkter för Azure Storage och Azure Key Vault. För resten av tjänsterna stöds inte VNet-tjänstens slut punkter och VNet-ACL: er i AD-klienter.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Kan en lokal enhets IP-adress som är ansluten via Azure Virtual Network Gateway (VPN) eller ExpressRoute Gateway åtkomst till Azure PaaS-tjänsten via VNet-tjänstens slut punkter?
Som standard kan Azure-tjänstresurser som skyddas på virtuella nätverk inte nås från lokala nätverk. Om du vill tillåta trafik från lokalt måste du också tillåta offentliga (vanligt vis NAT) IP-adresser från din lokala eller ExpressRoute. De här IP-adresserna kan läggas till via IP-brandväggens konfiguration för Azures tjänst resurser.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Kan jag använda funktionen för VNet-tjänstens slut punkt för att skydda Azure-tjänsten till flera undernät i ett virtuellt nätverk eller över flera virtuella nätverk?
Om du vill skydda Azure-tjänster till flera undernät i ett virtuellt nätverk eller över flera virtuella nätverk, aktiverar du tjänstens slut punkter på nätverks sidan på varje undernät oberoende av varandra och skyddar sedan Azure-tjänstens resurser till alla undernät genom att konfigurera lämpliga VNet-ACL: er på Azure-tjänstesidan.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Hur kan jag filtrera utgående trafik från ett virtuellt nätverk till Azure-tjänster och fortfarande använda tjänst slut punkter?
Om du vill granska eller filtrera trafiken mot en Azure-tjänst från ett virtuellt nätverk kan du distribuera en virtuell nätverksinstallation inom det virtuella nätverket. Du kan sedan använda tjänst slut punkter i under nätet där den virtuella nätverks enheten distribueras och säkra Azure-tjänstens resurser enbart till det här under nätet via VNet ACL: er. Det här scenariot kan också vara användbart om du vill begränsa åtkomsten till Azure-tjänsten från det virtuella nätverket endast till vissa Azure-resurser med hjälp av filtrering av virtuella nätverks enheter. Mer information finns i [Utgående trafik med virtuella nätverksinstallationer](/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Vad händer när du ansluter till ett Azure-tjänstkontot som har en åtkomst kontrol lista för virtuella nätverk (ACL) aktive rad utanför VNet?
HTTP 403-eller HTTP 404-fel returneras.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Har undernät i ett virtuellt nätverk som skapats i olika regioner åtkomst till ett Azure-tjänstkonto i en annan region? 
Ja, för de flesta Azure-tjänster kan virtuella nätverk som skapats i olika regioner komma åt Azure-tjänster i en annan region via VNet-tjänstens slut punkter. Om till exempel ett Azure Cosmos DB-konto finns i västra USA, östra USA och virtuella nätverk finns i flera regioner, kan det virtuella nätverket komma åt Azure Cosmos DB. Storage och SQL är undantag och är regionala i natur och både det virtuella nätverket och Azure-tjänsten måste finnas i samma region.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Kan en Azure-tjänst ha både en VNet-ACL och en IP-brandvägg?
Ja, en VNet ACL och en IP-brandvägg kan finnas tillsammans. Båda funktionerna kompletterar varandra för att säkerställa isolering och säkerhet.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Vad händer om du tar bort ett virtuellt nätverk eller undernät med tjänstens slut punkt aktive rad för Azure-tjänsten?
Borttagning av virtuella nätverk och undernät är oberoende åtgärder och stöds även om tjänstens slut punkter aktive ras för Azure-tjänster. I de fall där Azure-tjänsterna har aktiverat VNet ACL: er, för dessa virtuella nätverk och undernät, inaktive ras ACL-informationen för VNet som är kopplad till Azure-tjänsten när ett VNet eller ett undernät där VNet-tjänstens slut punkt är aktiverat tas bort.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Vad händer om ett Azure-tjänstkontot som har en aktive rad VNet-tjänst-slutpunkt är aktiverat tas bort?
Borttagningen av ett Azure-tjänstkonto är en oberoende åtgärd och stöds även om tjänstens slut punkt är aktive rad på nätverks sidan och VNet-ACL: er konfigureras på Azure Service sida. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Vad händer med käll-IP-adressen för en resurs (som en virtuell dator i ett undernät) som har VNet-tjänstens slut punkt aktive rad?
När tjänst slut punkter för virtuella nätverk är aktiverade växlar käll-IP-adresserna för resurserna i det virtuella nätverkets undernät från att använda offentliga IPV4-adresser till det virtuella Azure-nätverkets privata IP-adresser för trafik till Azure-tjänsten. Observera att detta kan orsaka att vissa IP-brandväggar som är inställda på offentlig IPV4-adress tidigare på Azure-tjänsterna inte fungerar. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Har tjänstens slut punkts flöde alltid företräde?
Tjänst slut punkter lägger till en system väg som har företräde framför BGP-vägar och ger optimal routning för tjänst slut punktens trafik. Tjänst slut punkter tar alltid tjänst trafik direkt från ditt virtuella nätverk till tjänsten i Microsoft Azure stamnät nätverket. Mer information om hur Azure väljer en väg finns i [Azures routning för virtuella nätverks trafik](virtual-networks-udr-overview.md).

### <a name="do-service-endpoints-work-with-icmp"></a>Fungerar tjänstens slut punkter med ICMP?
Nej, ICMP-trafik som har ursprung ATS från ett undernät med aktiverade tjänst slut punkter kommer inte att ta tjänstens tunnel Sök väg till önskad slut punkt. Tjänst slut punkter hanterar endast TCP-trafik. Det innebär att om du vill testa svars tiden eller anslutningen till en slut punkt via tjänst slut punkter, visar verktyg som ping och tracert inte den faktiska sökvägen som resurserna i under nätet tar.
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Hur fungerar NSG på ett undernät med tjänst slut punkter?
För att få åtkomst till Azure-tjänsten måste NSG: er tillåta utgående anslutning. Om dina NSG: er har öppnats för all utgående trafik i Internet bör tjänstens slut punkts trafik fungera. Du kan också begränsa utgående trafik till tjänst-IP-adresser endast med hjälp av tjänst taggarna.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Vilka behörigheter behöver jag för att konfigurera tjänst slut punkter?
Tjänst slut punkter kan konfigureras på ett virtuellt nätverk oberoende av en användare med Skriv behörighet till det virtuella nätverket. För att skydda Azure-tjänstens resurser till ett VNet måste användaren ha behörighet **Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/åtgärd** för de undernät som läggs till. Den här behörigheten ingår i den inbyggda rollen tjänst administratör som standard och kan ändras genom att skapa anpassade roller. Lär dig mer om inbyggda roller och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Kan jag filtrera en virtuell nätverks trafik till Azure-tjänster, så att endast vissa Azure-tjänst resurser tillåts, via virtuella nätverk för VNet-tjänster? 

Med tjänst slut punkts principer för virtuella nätverk (VNet) kan du filtrera virtuell nätverks trafik till Azure-tjänster och bara tillåta vissa Azure-tjänsteresurser via tjänst slut punkterna. Slut punkts principer ger detaljerad åtkomst kontroll från den virtuella nätverks trafiken till Azure-tjänsterna. Du kan läsa mer om tjänst slut punkts principerna [här](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Har Azure Active Directory (Azure AD) stöd för VNet-tjänstens slut punkter?

Azure Active Directory (Azure AD) stöder inte tjänstens slut punkter internt. En fullständig lista över Azure-tjänster som stöder VNet-tjänstens slut punkter visas [här](./virtual-network-service-endpoints-overview.md). Observera att taggen "Microsoft. AzureActiveDirectory" som anges under tjänster som stöder tjänst slut punkter används för att stödja tjänst slut punkter till ADLS gen 1. För ADLS gen 1 använder Virtual Network-integrering för Azure Data Lake Storage Gen1 den virtuella nätverks tjänstens slut punkt säkerhet mellan ditt virtuella nätverk och Azure Active Directory (Azure AD) för att generera ytterligare säkerhets anspråk i åtkomsttoken. Dessa anspråk används sedan för att autentisera ditt virtuella nätverk till ditt Data Lake Storage Gen1-konto och tillåta åtkomst. Läs mer om [Azure Data Lake Store generation 1 VNet-integrering](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Finns det några begränsningar för hur många VNet-tjänstens slut punkter jag kan konfigurera från mitt VNet?
Det finns ingen gräns för det totala antalet VNet-slutpunkter i ett virtuellt nätverk. För en Azure-tjänst resurs (till exempel ett Azure Storage konto) kan tjänster upprätthålla gränser för antalet undernät som används för att skydda resursen. I följande tabell visas några exempel begränsningar: 

|Azure-tjänst| Begränsningar för VNet-regler|
|---|---|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure Synapse Analytics|   128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store v1|  100|
 
>[!NOTE]
> Begränsningarna är beroende av ändringar i Azure-tjänstens bedömning. Se respektive tjänst dokumentation för information om tjänster. 




