---
title: Tjänstslutpunkter för virtuellt nätverk i Azure
titlesuffix: Azure Virtual Network
description: Lär dig hur du aktiverar direktåtkomst till Azure-resurser från ett virtuellt nätverk med tjänstslutpunkter.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 5e97a35a272684a99e1ca21cbec9c8f3edef253c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097430"
---
# <a name="virtual-network-service-endpoints"></a>Tjänstslutpunkter för virtuellt nätverk

Virtual Network tjänst slut punkten för virtuella nätverk ger säker och direkt anslutning till Azure-tjänster via en optimerad väg över Azures stamnät nätverk. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Med tjänstens slut punkter kan privata IP-adresser i VNet komma åt slut punkten för en Azure-tjänst utan att behöva en offentlig IP-adress på det virtuella nätverket.

Den här funktionen är tillgänglig för följande Azure-tjänster och-regioner. *Microsoft. \** Resource är i parentes. Aktivera den här resursen från under näts sidan när du konfigurerar tjänstens slut punkter för tjänsten:

**Allmänt tillgänglig**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft. Storage*): allmänt tillgänglig i alla Azure-regioner.
- **[Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): allmänt tillgängliga i alla Azure-regioner.
- **[Azure Synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): allmänt tillgänglig i alla Azure-regioner för dedikerade SQL-pooler (tidigare SQL DW).
- **[Azure Database for postgresql server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): allmänt tillgänglig i Azure-regioner där databas tjänsten är tillgänglig.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): allmänt tillgänglig i Azure-regioner där databas tjänsten är tillgänglig.
- **[Azure Database for MariaDB](../mariadb/concepts-data-access-security-vnet.md)** (*Microsoft. SQL*): allmänt tillgänglig i Azure-regioner där databas tjänsten är tillgänglig.
- **[Azure Cosmos DB](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureCosmosDB*): allmänt tillgänglig i alla Azure-regioner.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft. nyckel valv*): allmänt tillgänglig i alla Azure-regioner.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. Service Bus*): allmänt tillgänglig i alla Azure-regioner.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. EventHub*): allmänt tillgängliga i alla Azure-regioner.
- **[Azure Data Lake Store gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureActiveDirectory*): allmänt tillgänglig i alla Azure-regioner där ADLS gen1 är tillgängligt.
- **[Azure App Service](../app-service/app-service-ip-restrictions.md)** (*Microsoft. Web*): allmänt tillgänglig i alla Azure-regioner där App Service är tillgängligt.
- **[Azure Cognitive Services](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** (*Microsoft. CognitiveServices*): allmänt tillgänglig i alla Azure-regioner där kognitiva tjänster är tillgängliga.

**Offentlig för hands version**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft. ContainerRegistry*): för hands version tillgänglig i begränsade Azure-regioner där Azure Container Registry är tillgängligt.

De mest uppdaterade meddelandena finns på sidan för [Azure Virtual Network-uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Viktiga fördelar

Tjänstslutpunkter har följande fördelar:

- **Förbättrad säkerhet för dina Azure-tjänst resurser**: privata VNet-adressutrymme kan överlappa varandra. Du kan inte använda överlappande blank steg för att unikt identifiera trafik som kommer från ditt VNet. Med tjänst slut punkter kan du skydda Azures tjänst resurser i ditt virtuella nätverk genom att utöka VNet-identiteten till tjänsten. När du har aktiverat tjänstens slut punkter i ditt virtuella nätverk kan du lägga till en regel för virtuella nätverk för att skydda Azure-tjänstens resurser i det virtuella nätverket. Regeln addition ger förbättrad säkerhet genom att helt ta bort offentlig Internet åtkomst till resurser och bara tillåta trafik från det virtuella nätverket.
- **Optimal routning för Azure Service-trafik från det virtuella nätverket**: idag, alla vägar i ditt virtuella nätverk som tvingar Internet trafik till dina lokala och/eller virtuella enheter, tvingar också Azure-tjänstetrafik att ta samma väg som internet trafiken. Med tjänstslutpunkter får du optimal routning för Azure-trafiken. 

  Slutpunkter tar alltid tjänsttrafiken direkt från ditt virtuella nätverk till tjänsten i Microsoft Azure-stamnätverket. Om du behåller trafiken i Azure-stamnätverket kan du fortsätta att granska och övervaka den utgående internettrafiken från dina virtuella nätverk, via tvingad tunneltrafik, utan att tjänsttrafiken påverkas. Mer information om användardefinierade vägar och Tvingad tunnel trafik finns i [Azure Virtual Network trafikroutning](virtual-networks-udr-overview.md).
- **Enkelt att konfigurera och lägre administrationskostnader**: du behöver inte längre reserverade, offentliga IP-adresser i ditt virtuella nätverk för att skydda Azure-resurser genom IP-brandväggen. Det krävs inga NAT (Network Address Translation) eller gateway-enheter för att konfigurera tjänst slut punkterna. Du kan konfigurera tjänstens slut punkter via ett enkelt klick på ett undernät. Det finns inga ytterligare kostnader för att underhålla slut punkterna.

## <a name="limitations"></a>Begränsningar

- Funktionen är bara tillgänglig för virtuella nätverk som distribuerats med Azure Resource Manager-distributionsmodellen.
- Slutpunkter aktiveras i undernät som konfigurerats i virtuella Azure-nätverk. Slut punkter kan inte användas för trafik från dina lokaler till Azure-tjänster. Mer information finns i [Skydda åtkomsten till Azure-tjänsten från lokala platser](#secure-azure-services-to-virtual-networks)
- För Azure SQL gäller en tjänstslutpunkt bara för Azure-tjänsttrafik i regionen för det virtuella nätverket. För Azure Storage utökas slut punkterna till att inkludera kopplade regioner där du distribuerar det virtuella nätverket så att det stöder Read-Access Geo-Redundant Storage (RA-GRS) och Geo-Redundant lagrings trafik (GRS). Mer information finns i [Azure-kopplade regioner](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- För Azure Data Lake Storage (ADLS) gen 1 är funktionerna för VNet-integrering endast tillgängliga för virtuella nätverk inom samma region. Observera också att integrering av virtuella nätverk för ADLS Gen1 använder den virtuella nätverks tjänstens slut punkt säkerhet mellan ditt virtuella nätverk och Azure Active Directory (Azure AD) för att generera ytterligare säkerhets anspråk i åtkomsttoken. Dessa anspråk används sedan för att autentisera ditt virtuella nätverk till ditt Data Lake Storage Gen1-konto och tillåta åtkomst. *Microsoft. AzureActiveDirectory* -taggen som anges under tjänster som stöder tjänst slut punkter används endast för stöd av tjänst slut punkter till ADLS gen 1. Azure AD har inte stöd för tjänst slut punkter internt. Mer information om Azure Data Lake Store generation 1 VNet-integrering finns [i nätverks säkerhet i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Skydda Azure-tjänster på virtuella nätverk

- En tjänstslutpunkt för virtuellt nätverk anger det virtuella nätverkets identitet för Azure-tjänsten. När du har aktiverat tjänstens slut punkter i ditt virtuella nätverk kan du lägga till en regel för virtuella nätverk för att skydda Azure-tjänstens resurser i det virtuella nätverket.
- Idag används offentliga IP-adresser som IP-källadresser för Azure-tjänsttrafiken från ett virtuellt nätverk. Med tjänstslutpunkter använder tjänsttrafiken istället privata adresser i det virtuella nätverket som IP-källadresser vid åtkomst till Azure-tjänsten från det virtuella nätverket. Med det här bytet kan du komma åt tjänsterna utan att behöva reserverade, offentliga IP-adresser som används i brandväggar.

   >[!NOTE]
   > Med tjänstslutpunkter växlar källans IP-adresser för virtuella datorer i undernätet för tjänsttrafik från att använda offentliga IPv4-adresser till att istället använda privata. Befintliga brandväggsregler för Azure-tjänsten med hjälp av offentliga IP-adresser i Azure slutar att fungera via den här ändringen. Kontrollera att brandväggsreglerna för Azure-tjänsten tillåter den här växeln innan du konfigurerar tjänstslutpunkter. Det kan också uppstå tillfälligt avbrott för tjänsttrafik från det här undernätet när du konfigurerar tjänstslutpunkter. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Skydda åtkomsten till Azure-tjänsten lokalt

  Som standard går det inte att komma åt Azure Service-resurser som är säkrade för virtuella nätverk från lokala nätverk. Om du vill tillåta trafik från lokalt måste du också tillåta offentliga (vanligt vis NAT) IP-adresser från din lokala eller ExpressRoute. Du kan lägga till dessa IP-adresser via IP-brandväggens konfiguration för Azure Service-resurser.

  ExpressRoute: om du använder [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för offentlig peering eller Microsoft-peering från dina lokaler måste du identifiera de NAT-IP-adresser som du använder. För offentlig peering använder varje ExpressRoute-krets två NAT-IP-adresser som standard som tillämpas på Azure Service-trafik när trafiken går in Microsoft Azure nätverkets stamnät. För Microsoft-peering tillhandahålls NAT-IP-adresserna antingen av kunden eller tillhandahålls av tjänst leverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Mer information om NAT för ExpressRoute offentlig och Microsoft-peering finns i [EXPRESSROUTE NAT-krav](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Skydda Azure-tjänster i virtuella nätverk](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguration

- Konfigurera tjänst slut punkter i ett undernät i ett virtuellt nätverk. Slutpunkter fungerar med valfri typ av beräkningsinstans som körs inom det undernätet.
- Du kan konfigurera flera tjänst slut punkter för alla Azure-tjänster som stöds (Azure Storage eller Azure SQL Database till exempel) i ett undernät.
- För Azure SQL Database måste virtuella nätverk finnas i samma region som Azure-tjänstresursen. Om du använder GRS- och RA-GRS-konton för Azure Storage måste det primära kontot finnas i samma region som det virtuella nätverket. För alla andra tjänster kan du skydda Azures tjänst resurser till virtuella nätverk i vilken region som helst. 
- Det virtuella nätverket där slutpunkten konfigureras kan vara i samma prenumeration som Azure-tjänstresursen eller i en annan. Mer information om vilka behörigheter som krävs för att konfigurera slutpunkter och skydda Azure-tjänster finns under [Etablering](#provisioning).
- För tjänster som stöds kan du skydda nya eller befintliga resurser i virtuella nätverk som använder tjänstslutpunkter.

### <a name="considerations"></a>Överväganden

- När du har aktiverat en tjänst slut punkt växlar Källans IP-adresser från att använda offentliga IPv4-adresser till att använda sin privata IPv4-adress när de kommunicerar med tjänsten från det under nätet. Befintliga öppna TCP-anslutningar till tjänsten stängs under bytet. Kontrollera att inga kritiska uppgifter körs när du aktiverar eller inaktiverar en tjänstslutpunkt för en tjänst för ett undernät. Kontrollera även att dina program ansluter automatiskt till Azure-tjänster efter IP-adressbytet.

  IP-adressbytet påverkar bara tjänsttrafiken från ditt virtuella nätverk. Det påverkar inte någon annan trafik som är adresserad till eller från offentliga IPv4-adresser som är tilldelade till dina virtuella datorer. För Azure-tjänster gäller att befintliga brandväggsregler där offentliga Azure-IP-adresser används slutar att fungera vid bytet till privata adresser i det virtuella nätverket.
- Med tjänst slut punkter förblir DNS-posterna för Azure-tjänster redan idag och fortsätter att matcha till offentliga IP-adresser som tilldelats till Azure-tjänsten.

- Nätverkssäkerhetsgrupper (NSG) med tjänstslutpunkter:
  - Som standard tillåter NSG: er utgående Internet trafik och tillåter även trafik från ditt VNet till Azure-tjänster. Trafiken fortsätter att fungera med tjänstens slut punkter som är. 
  - Om du vill neka all utgående Internet trafik och bara tillåta trafik till vissa Azure-tjänster kan du göra det med hjälp av [service märken](./network-security-groups-overview.md#service-tags) i din NSG: er. Du kan ange vilka Azure-tjänster som stöds som mål i dina NSG-regler och Azure tillhandahåller även underhåll av IP-adresser som är underliggande för varje tagg. Mer information finns i [Azure-tjänsttaggar för NSG:er](./network-security-groups-overview.md#service-tags). 

### <a name="scenarios"></a>Scenarier

- **Peerkopplade, anslutna eller flera virtuella nätverk**: om du vill skydda Azure-tjänster i flera undernät inom ett virtuellt nätverk eller i flera virtuella nätverk kan du aktivera tjänstslutpunkter i vart och ett av dessa undernät separat och skydda Azure-tjänstresurser i samtliga undernät.
- **Filtrera utgående trafik från ett virtuellt nätverk till Azure-tjänster**: om du vill granska eller filtrera trafiken som skickas till en Azure-tjänst från ett virtuellt nätverk kan du distribuera en virtuell nätverks installation i det virtuella nätverket. Du kan sedan använda tjänstslutpunkter för undernätet där den virtuella nätverksinstallationen är distribuerad och endast skydda Azure-tjänstresurser i det här undernätet. Det här scenariot kan vara användbart om du vill använda filtrering av virtuella nätverk för att begränsa åtkomsten till Azure-tjänsten från det virtuella nätverket endast till vissa Azure-resurser. Mer information finns i [Utgående trafik med virtuella nätverksinstallationer](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Skydda Azure-resurser till tjänster som distribueras direkt till virtuella nätverk**: du kan distribuera olika Azure-tjänster direkt i vissa undernät i ett virtuellt nätverk. Du kan skydda Azure-tjänstresurser i undernät med [hanterade tjänster](virtual-network-for-azure-services.md) genom att konfigurera en tjänstslutpunkt i undernätet med hanterade tjänster.
- **Disk trafik från en virtuell Azure-dator**: den virtuella datorns disk trafik för hanterade och ohanterade diskar påverkas inte av tjänst slut punkter som dirigerar ändringar för Azure Storage. Trafiken omfattar både diskIO och montering och demontering. Du kan begränsa REST-åtkomsten till sid-blobar för att välja nätverk via tjänst slut punkter och [Azure Storage nätverks regler](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Loggning och felsökning

När du har konfigurerat tjänstens slut punkter till en tjänst kontrollerar du att tjänstens slut punkts flöde gäller för: 
 
- Verifiera IP-källadressen för alla tjänstbegäran i tjänstdiagnostiken. Alla nya begäranden med tjänstslutpunkter visar IP-källadressen för begäran som det virtuella nätverkets privata adress, som tilldelats till klienten som gör begäran från ditt virtuella nätverk. Utan slutpunkten är denna adress en offentlig IP-adress för Azure.
- Visa effektiva vägar i alla nätverksgränssnitt i ett undernät. Vägen till tjänsten:
  - Visar en mer specifik standardväg till adressprefixområden för varje tjänst
  - Har *VirtualNetworkServiceEndpoint* som nextHopType
  - Anger att en mer direkt anslutning till tjänsten gäller jämfört med eventuella tvingande tunnel vägar

>[!NOTE]
> Vägar för tjänstslutpunkter åsidosätter BGP- eller UDR-vägar vid matchning av adressprefix för en Azure-tjänst. Mer information finns i [fel sökning med effektiva vägar](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Etablering

Tjänst slut punkter kan konfigureras på virtuella nätverk oberoende av en användare med Skriv behörighet till ett virtuellt nätverk. För att skydda Azure-tjänstens resurser till ett VNet måste användaren ha behörighet till *Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/Action* för de tillagda under näten. De inbyggda tjänst administratörs rollerna inkluderar denna behörighet som standard. Du kan ändra behörigheten genom att skapa anpassade roller.

Mer information om inbyggda roller finns i [inbyggda roller i Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Mer information om hur du tilldelar vissa behörigheter till anpassade roller finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om det virtuella nätverket och Azure-tjänstresurser finns i olika prenumerationer måste resurserna finnas under samma AD-klientorganisation (Active Directory). 

## <a name="pricing-and-limits"></a>Priser och begränsningar

Det kostar inget extra att använda tjänst slut punkter. Den aktuella pris sättnings modellen för Azure-tjänster (Azure Storage, Azure SQL Database osv.) gäller i dag.

Det finns ingen gräns för det totala antalet tjänst slut punkter i ett virtuellt nätverk.

Vissa Azure-tjänster, till exempel Azure Storage-konton, kan genomdriva gränser för antalet undernät som används för att skydda resursen. Mer information finns i dokumentationen för olika tjänster i avsnittet [Nästa steg](#next-steps) .

## <a name="vnet-service-endpoint-policies"></a>Slut punkts principer för VNet-tjänst 

Med VNet-tjänstens slut punkts principer kan du filtrera virtuell nätverks trafik till Azure-tjänster. Det här filtret tillåter endast vissa Azure-tjänst resurser via tjänst slut punkter. Principer för tjänstslutpunkt ger detaljerad åtkomstkontroll för trafik i virtuella nätverk till Azure-tjänster. Mer information finns i [Virtual Network tjänst slut punkts principer](./virtual-network-service-endpoint-policies-overview.md).

## <a name="faqs"></a>Vanliga frågor och svar

Vanliga frågor och svar [Virtual Network finns i vanliga frågor och svar om service Endpoint](./virtual-networks-faq.md#virtual-network-service-endpoints).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera tjänst slut punkter för virtuella nätverk](tutorial-restrict-network-access-to-resources.md)
- [Skydda ett Azure Storage-konto till ett virtuellt nätverk](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skydda en Azure SQL Database till ett virtuellt nätverk](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skydda en Azure Synapse-analys till ett virtuellt nätverk](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure Service integration i virtuella nätverk](virtual-network-for-azure-services.md)
- [Virtual Network tjänst slut punkts principer](./virtual-network-service-endpoint-policies-overview.md)
- [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)