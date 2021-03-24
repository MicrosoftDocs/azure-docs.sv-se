---
title: Privat länk – Azure Database for MySQL
description: Lär dig hur en privat länk fungerar för Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 438ef806f6c59c6f23877a3d3110f22f08ca8713
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863572"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Private Link för Azure Database for MySQL

Med Private Link kan du ansluta till olika PaaS-tjänster i Azure via en privat slutpunkt. Azure Private Link ger dig tillgång till Azure-tjänster i ditt privata virtuella nätverk (VNet). PaaS-resurser kan nås med hjälp av den privata IP-adressen precis som vilken annan resurs som helst i VNet.

Om du vill ha en lista över PaaS Services som stöder funktionen för privat länk läser du [dokumentationen](../private-link/index.yml)om den privata länken. En privat slut punkt är en privat IP-adress inom ett särskilt [VNet](../virtual-network/virtual-networks-overview.md) och undernät.

> [!NOTE]
> Funktionen privat länk är bara tillgänglig för Azure Database for MySQL servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att databas servern är på någon av dessa pris nivåer.

## <a name="data-exfiltration-prevention"></a>Dataexfiltreringsskydd

Data vid filtrering i Azure Database for MySQL är när en behörig användare, till exempel en databas administratör, kan extrahera data från ett system och flytta det till en annan plats eller ett system utanför organisationen. Användaren kan till exempel flytta data till ett lagrings konto som ägs av en tredje part.

Överväg ett scenario med en användare som kör MySQL Workbench i en virtuell Azure-dator (VM) som ansluter till en Azure Database for MySQL server som har skapats i västra USA. Exemplet nedan visar hur du begränsar åtkomsten med offentliga slut punkter på Azure Database for MySQL med hjälp av nätverks åtkomst kontroller.

* Inaktivera all Azure Service-trafik för att Azure Database for MySQL via den offentliga slut punkten genom att ställa in *Tillåt att Azure-tjänster* stängs av. Se till att inga IP-adresser eller intervall får åtkomst till servern antingen via [brand Väggs regler](./concepts-firewall-rules.md) eller [tjänst slut punkter för virtuella nätverk](./concepts-data-access-and-security-vnet.md).

* Tillåt endast trafik till Azure Database for MySQL med den virtuella datorns privata IP-adress. Mer information finns i artikeln om [service Endpoint](concepts-data-access-and-security-vnet.md) och [VNet brand Väggs regler](howto-manage-vnet-using-portal.md).

* På den virtuella Azure-datorn begränsar du omfattningen av utgående anslutning genom att använda nätverks säkerhets grupper (NSG: er) och service märken enligt följande

    * Ange en NSG-regel för att tillåta trafik för *service tag = SQL. Västra* USA – endast tillåta anslutning till Azure Database for mysql i västra USA
    * Ange en NSG-regel (med en högre prioritet) för att neka trafik för *service tag = SQL* -neka anslutningar att uppdatera till Azure Database for mysql i alla regioner</br></br>

I slutet av den här installationen kan den virtuella Azure-datorn bara ansluta till Azure Database for MySQL i regionen USA, västra. Anslutningen är dock inte begränsad till en enda Azure Database for MySQL. Den virtuella datorn kan fortfarande ansluta till en Azure Database for MySQL i regionen USA, västra, inklusive de databaser som inte ingår i prenumerationen. Vi har minskat omfattningen av data exfiltrering i scenariot ovan till en speciell region, men vi har inte eliminerat det helt.</br>

Med privat länk kan du nu konfigurera nätverks åtkomst kontroller som NSG: er för att begränsa åtkomsten till den privata slut punkten. Enskilda Azure PaaS-resurser mappas sedan till specifika privata slut punkter. En skadlig Insider kan bara komma åt den mappade PaaS-resursen (till exempel en Azure Database for MySQL) och ingen annan resurs.

## <a name="on-premises-connectivity-over-private-peering"></a>Lokal anslutning via privat peering

När du ansluter till den offentliga slut punkten från lokala datorer måste du lägga till din IP-adress i den IP-baserade brand väggen med hjälp av en brand Väggs regel på server nivå. Även om den här modellen fungerar bra för att tillåta åtkomst till enskilda datorer för utveckling eller testning av arbets belastningar, är det svårt att hantera i en produktions miljö.

Med privat länk kan du aktivera åtkomst mellan platser till den privata slut punkten med hjälp av [Express Route](https://azure.microsoft.com/services/expressroute/) (er), privat peering eller [VPN-tunnel](../vpn-gateway/index.yml). De kan sedan inaktivera all åtkomst via offentlig slut punkt och inte använda den IP-baserade brand väggen.

> [!NOTE]
> I vissa fall finns Azure Database for MySQL och VNet-under nätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
> - Se till att båda prenumerations resurs leverantören för **Microsoft. DBforMySQL** är registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Konfigurera privat länk för Azure Database for MySQL

### <a name="creation-process"></a>Skapande process

Privata slut punkter krävs för att aktivera privat länk. Detta kan göras med hjälp av följande instruktions guider.

* [Azure-portalen](./howto-configure-privatelink-portal.md)
* [CLI](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Godkännande process
När nätverks administratören skapar den privata slut punkten (PE) kan MySQL-administratören hantera den privata slut punkts anslutningen (PEC) för att Azure Database for MySQL. Denna uppdelning av uppgifter mellan nätverks administratören och DBA är användbar för hantering av den Azure Database for MySQL anslutningen. 

* Navigera till Azure Database for MySQL server-resursen i Azure Portal. 
    * Välj anslutningar för privata slut punkter i det vänstra fönstret
    * Visar en lista över alla anslutningar för privata slut punkter (PECs)
    * Motsvarande privata slut punkt (PE) har skapats

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="Välj den privata slut punkts portalen":::

* Välj en enskild PEC från listan genom att markera den.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="Välj den privata slut punkten som väntar på godkännande":::

* MySQL-serverns administratör kan välja att godkänna eller avvisa en PEC och eventuellt lägga till ett kort text svar.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="Välj meddelandet privat slut punkt":::

* Efter godkännande eller avvisande visar listan lämpligt tillstånd tillsammans med svars texten

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="Välj slut tillstånd för privat slut punkt":::

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Använd fall av privat länk för Azure Database for MySQL

Klienter kan ansluta till den privata slut punkten från samma VNet, [peer](../virtual-network/virtual-network-peering-overview.md) -kopplat VNet i samma region eller mellan regioner, eller via [VNet-till-VNET-anslutning](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) mellan regioner. Dessutom kan klienter ansluta lokalt med ExpressRoute, privat peering eller VPN-tunnlar. Nedan visas ett förenklat diagram som visar vanliga användnings fall.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="Välj översikt över privat slut punkt":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ansluta från en virtuell Azure-dator i peer-Virtual Network (VNet)
Konfigurera [VNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) för att upprätta anslutning till Azure Database for MySQL från en virtuell Azure-dator i ett peer-kopplat VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ansluta från en virtuell Azure-dator i VNet-till-VNet-miljö
Konfigurera [VPN gateway-anslutning mellan virtuella](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) nätverk för att upprätta anslutning till en Azure Database for MySQL från en virtuell Azure-dator i en annan region eller prenumeration.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ansluta från en lokal miljö via VPN
Om du vill upprätta en anslutning från en lokal miljö till Azure Database for MySQL väljer du och implementerar något av alternativen:

* [Punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Plats-till-plats-anslutning via VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Privat länk kombinerat med brand Väggs regler

Följande situationer och resultat är möjliga när du använder en privat länk i kombination med brand Väggs regler:

* Om du inte konfigurerar några brand Väggs regler kommer ingen trafik att kunna komma åt Azure Database for MySQL.

* Om du konfigurerar offentlig trafik eller en tjänst slut punkt och skapar privata slut punkter, auktoriseras olika typer av inkommande trafik av motsvarande typ av brand Väggs regel.

* Om du inte konfigurerar någon offentlig trafik eller tjänst slut punkt och du skapar privata slut punkter kan Azure Database for MySQL endast nås via de privata slut punkterna. Om du inte konfigurerar offentlig trafik eller en tjänst slut punkt efter att alla godkända privata slut punkter har avvisats eller tagits bort, kommer ingen trafik att kunna komma åt Azure Database for MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Neka offentlig åtkomst för Azure Database for MySQL

Om du bara vill använda privata slut punkter för att komma åt sina Azure Database for MySQL, kan du inaktivera inställningen alla offentliga slut punkter (t. ex. [brand Väggs regler](concepts-firewall-rules.md) och [VNet-slutpunkter](concepts-data-access-and-security-vnet.md)) genom att ange den **nekade konfigurationen för offentlig nätverks åtkomst** på databas servern. 

När den här inställningen är inställd på *Ja*, tillåts bara anslutningar via privata slut punkter till din Azure Database for MySQL. När den här inställningen är inställd på *Nej* kan klienter ansluta till din Azure Database for MySQL baserat på inställningarna för brand väggen eller VNet-tjänstens slut punkt. När värdet för åtkomst till privat nätverk har angetts kan kunder dessutom inte lägga till och/eller uppdatera befintliga brand Väggs regler och VNet-tjänstens slut punkts regler.

> [!Note]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database for PostgreSQL-enskild server stöder Generell användning och minnesoptimerade pris nivåer.
>
> Den här inställningen påverkar inte SSL-och TLS-konfigurationer för Azure Database for MySQL.

Information om hur du ställer in **neka offentlig nätverks åtkomst** för din Azure Database for MySQL från Azure Portal finns i [så här konfigurerar du neka offentlig nätverks åtkomst](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Database for MySQL säkerhetsfunktioner finns i följande artiklar:

* Information om hur du konfigurerar en brand vägg för Azure Database for MySQL finns i [brand Väggs stöd](./concepts-firewall-rules.md).

* Information om hur du konfigurerar en tjänst slut punkt för ett virtuellt nätverk för Azure Database for MySQL finns i [Konfigurera åtkomst från virtuella nätverk](./concepts-data-access-and-security-vnet.md).

* En översikt över Azure Database for MySQL-anslutningen finns i [Azure Database for MySQL anslutnings arkitektur](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
