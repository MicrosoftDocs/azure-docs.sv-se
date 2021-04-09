---
title: Regler för virtuella nätverk – Azure Database for PostgreSQL-enskild server
description: Lär dig hur du använder tjänst slut punkter för virtuella nätverk (VNet) för att ansluta till Azure Database for PostgreSQL-enskild server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: b875936e13edfe0eff12f253836b093796951308
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98876334"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Använd Virtual Network tjänst slut punkter och regler för Azure Database for PostgreSQL-enskild server

*Regler för virtuella nätverk* är en brand Väggs säkerhetsfunktion som styr om din Azure Database for postgresql server accepterar kommunikation som skickas från vissa undernät i virtuella nätverk. I den här artikeln förklaras varför funktionen för regel för virtuella nätverk ibland det bästa alternativet för att på ett säkert sätt tillåta kommunikation till din Azure Database for PostgreSQL-Server.

Om du vill skapa en regel för virtuellt nätverk måste du först vara ett [virtuellt nätverk][vm-virtual-network-overview] (VNet) och en [tjänst slut punkt för virtuellt nätverk][vm-virtual-network-service-endpoints-overview-649d] för regeln som ska refereras. Följande bild illustrerar hur en Virtual Network tjänst slut punkt fungerar med Azure Database for PostgreSQL:

:::image type="content" source="media/concepts-data-access-and-security-vnet/vnet-concept.png" alt-text="Exempel på hur en VNet-tjänst slut punkt fungerar":::

> [!NOTE]
> Den här funktionen är tillgänglig i alla regioner i det offentliga Azure-molnet där Azure Database for PostgreSQL distribueras för Generell användning och minnesoptimerade servrar.
> Om det gäller VNet-peering, om trafik flödar genom en gemensam VNet-Gateway med tjänst slut punkter och ska flöda till motparten, skapar du en ACL/VNet-regel för att tillåta Azure Virtual Machines i gatewayens VNet att komma åt Azure Database for PostgreSQL-servern.

Du kan också överväga att använda [privat länk](concepts-data-access-and-security-private-link.md) för anslutningar. Privat länk tillhandahåller en privat IP-adress i ditt VNet för Azure Database for PostgreSQL-servern.

<a name="anch-terminology-and-description-82f"></a>
## <a name="terminology-and-description"></a>Terminologi och beskrivning

**Virtuellt nätverk:** Du kan ha virtuella nätverk kopplade till din Azure-prenumeration.

**Undernät:** Ett virtuellt nätverk innehåller **undernät**. Alla virtuella Azure-datorer (VM) i det virtuella nätverket tilldelas till ett undernät. Ett undernät kan innehålla flera virtuella datorer och/eller andra Compute-noder. Compute-noder utanför det virtuella nätverket kan inte komma åt ditt virtuella nätverk om du inte konfigurerar din säkerhet att tillåta åtkomst.

**Virtual Network tjänst slut punkt:** En [Virtual Network tjänst slut punkt][vm-virtual-network-service-endpoints-overview-649d] är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typ. I den här artikeln är vi intresserade av typ namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database. Den här tjänst tag gen gäller även för Azure Database for PostgreSQL-och MySQL-tjänster. Det är viktigt att du noterar när du använder service tag-koden för **Microsoft. SQL** på en slut punkt för VNet-tjänsten som konfigurerar tjänst slut punkts trafik för Azure Database Services: SQL Database, Azure Synapse Analytics, Azure Database for PostgreSQL och Azure Database for MySQL servrar i under nätet. 

**Regel för virtuellt nätverk:** En regel för virtuella nätverk för din Azure Database for PostgreSQL-Server är ett undernät som listas i åtkomst kontrol listan (ACL) för din Azure Database for PostgreSQL-Server. För att finnas i ACL: en för din Azure Database for PostgreSQL-Server måste under nätet innehålla namnet **Microsoft. SQL** -typ.

En regel för virtuella nätverk instruerar Azure Database for PostgreSQL servern att acceptera kommunikation från varje nod som finns på under nätet.

<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="benefits-of-a-virtual-network-rule"></a>Fördelar med en virtuell nätverks regel

De virtuella datorerna i under näten kan inte kommunicera med din Azure Database for PostgreSQL-Server förrän du vidtar åtgärder. En åtgärd som upprättar kommunikationen är att skapa en regel för virtuella nätverk. Anledningen till att du väljer regel metoden för VNet kräver en jämförelse-och-kontrast-diskussion som involverar de konkurrerande säkerhets alternativ som erbjuds av brand väggen.

### <a name="allow-access-to-azure-services"></a>Tillåt åtkomst till Azure-tjänster

Fönstret anslutnings säkerhet har en **på/av-** knapp med etiketten **Tillåt åtkomst till Azure-tjänster**. Inställningen **on** tillåter kommunikation från alla Azure IP-adresser och alla Azure-undernät. Dessa Azure IP-adresser eller undernät kanske inte ägs av dig. Den **här** inställningen är förmodligen mer öppen än du vill att din Azure Database for PostgreSQL-databas ska vara. Funktionen för regel för virtuella nätverk ger en mycket noggrannare detaljerad kontroll.

### <a name="ip-rules"></a>IP-regler

Med Azure Database for PostgreSQL brand väggen kan du ange IP-adressintervall från vilka kommunikationen godkänns i Azure Database for PostgreSQL-databasen. Den här metoden är bra för stabila IP-adresser som ligger utanför Azures privata nätverk. Men många noder i det privata Azure-nätverket har kon figurer ATS med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras, till exempel när den virtuella datorn startas om. Det skulle vara Folly att ange en dynamisk IP-adress i en brand Väggs regel i en produktions miljö.

Du kan hämta IP-alternativet genom att skaffa en *statisk* IP-adress för den virtuella datorn. Mer information finns i [Konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Den statiska IP-metoden kan dock bli svår att hantera, och den är kostsam när den görs i stor skala. Det är enklare att upprätta och hantera virtuella nätverks regler.


<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="details-about-virtual-network-rules"></a>Information om regler för virtuella nätverk

I det här avsnittet beskrivs flera Detaljer om regler för virtuella nätverk.

### <a name="only-one-geographic-region"></a>Endast en geografisk region

Varje Virtual Network tjänst slut punkt gäller endast en Azure-region. Slut punkten tillåter inte att andra regioner accepterar kommunikation från under nätet.

Alla virtuella nätverks regler är begränsade till den region som den underliggande slut punkten gäller för.

### <a name="server-level-not-database-level"></a>Server nivå, inte databas nivå

Varje virtuell nätverks regel gäller hela Azure Database for PostgreSQL-servern, inte bara till en viss databas på servern. Med andra ord gäller regel för virtuella nätverk på server nivå, inte på databas nivå.

#### <a name="security-administration-roles"></a>Säkerhets administrations roller

Det finns en separation av säkerhets roller i administration av Virtual Network tjänstens slut punkter. Åtgärd krävs från var och en av följande roller:

- **Nätverks administratör:** &nbsp; Aktivera slut punkten.
- **Databas administratör:** &nbsp; Uppdatera åtkomst kontrol listan (ACL) för att lägga till angivet undernät till Azure Database for PostgreSQL servern.

*Alternativ för Azure RBAC:*

Rollerna för nätverks administratör och databas administratörer har fler funktioner än vad som krävs för att hantera virtuella nätverks regler. Endast en delmängd av deras funktioner krävs.

Du kan välja att använda [Azures rollbaserad åtkomst kontroll (Azure RBAC)][rbac-what-is-813s] i Azure för att skapa en enskild anpassad roll som bara har de funktioner som krävs. Den anpassade rollen kan användas i stället för att involvera antingen nätverks administratören eller databas administratören. Arean av din säkerhets exponering är lägre om du lägger till en användare i en anpassad roll och lägger till användaren till de andra två större administratörs rollerna.

> [!NOTE]
> I vissa fall finns Azure Database for PostgreSQL och VNet-under nätet i olika prenumerationer. I dessa fall måste du se till att följande konfigurationer:
> - Båda prenumerationerna måste vara i samma Azure Active Directory-klient.
> - Användaren har de behörigheter som krävs för att initiera åtgärder, till exempel aktivera tjänst slut punkter och lägga till ett VNet-undernät till den aktuella servern.
> - Se till att både prenumerationen **Microsoft. SQL** och **Microsoft. DBforPostgreSQL** Resource provider har registrerats. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

## <a name="limitations"></a>Begränsningar

För Azure Database for PostgreSQL har funktionen regler för virtuellt nätverk följande begränsningar:

- En webbapp kan mappas till en privat IP-adress i ett VNet/undernät. Även om tjänstens slut punkter är påslagna från det virtuella nätverket/under nätet kommer anslutningar från webbappen till-servern att ha en offentlig Azure-IP-källa, inte en källa för VNet/undernät. Om du vill aktivera anslutning från en webbapp till en server som har VNet brand Väggs regler måste du ge Azure-tjänster åtkomst till servern på servern.

- I brand väggen för din Azure Database for PostgreSQL refererar varje virtuell nätverks regel till ett undernät. Alla dessa refererade undernät måste ligga inom samma geografiska region som är värd för Azure Database for PostgreSQL.

- Varje Azure Database for PostgreSQL Server kan ha upp till 128 ACL-poster för ett angivet virtuellt nätverk.

- Regler för virtuella nätverk gäller endast för Azure Resource Manager virtuella nätverk; och inte till [klassiska nätverk för distributions modeller][arm-deployment-model-568f] .

- När du aktiverar tjänst slut punkter för virtuella nätverk för att Azure Database for PostgreSQL med hjälp av service tag gen för **Microsoft. SQL** kan du också aktivera slut punkter för alla Azure Database-tjänster: Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database och Azure Synapse Analytics.

- Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.

- Om **Microsoft. SQL** har Aktiver ATS i ett undernät anger det att du bara vill använda VNet-regler för att ansluta. Det går inte att använda [brand Väggs regler för icke-VNet](concepts-firewall-rules.md) i det under nätet.

- I brand väggen gäller IP-adressintervall för följande nätverks objekt, men regler för virtuella nätverk gör inte följande:
    - [Virtuellt privat nätverk (VPN) för plats-till-plats (S2S)][vpn-gateway-indexmd-608y]
    - Lokalt via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Om nätverket är anslutet till Azure-nätverket med hjälp av [ExpressRoute][expressroute-indexmd-744v]konfigureras varje krets med två offentliga IP-adresser på Microsoft Edge. De två IP-adresserna används för att ansluta till Microsoft-tjänster, till exempel för att Azure Storage med hjälp av Azures offentliga peering.

Om du vill tillåta kommunikation från din krets till Azure Database for PostgreSQL måste du skapa IP-nätverksnummer för dina kretsars offentliga IP-adresser. För att hitta den offentliga IP-adressen för din ExpressRoute-krets öppnar du ett support ärende med ExpressRoute med hjälp av Azure Portal.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Lägga till en brand Väggs regel för VNET på servern utan att aktivera VNET-tjänstens slut punkter

Att bara ange en brand Väggs regel för VNet skyddar inte servern till VNet. Du måste också aktivera VNet-tjänstens slut **punkter för att** säkerheten ska börja gälla. När du aktiverar tjänstens slut punkter **aktive** ras stillestånds tiden för VNet-undernät tills den slutför över **gången från till** **på**. Detta gäller särskilt i samband med stora virtuella nätverk. Du kan använda flaggan **IgnoreMissingServiceEndpoint** för att minska eller eliminera stillestånds tiden under över gången.

Du kan ställa in flaggan **IgnoreMissingServiceEndpoint** med hjälp av Azure CLI eller portalen.

## <a name="related-articles"></a>Relaterade artiklar
- [Virtuella Azure-nätverk][vm-virtual-network-overview]
- [Tjänstslutpunkter för virtuellt nätverk i Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar VNet-regler finns i:
- [Skapa och hantera Azure Database for PostgreSQL VNet-regler med hjälp av Azure Portal](howto-manage-vnet-using-portal.md)
- [Skapa och hantera Azure Database for PostgreSQL VNet-regler med hjälp av Azure CLI](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../role-based-access-control/overview.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
