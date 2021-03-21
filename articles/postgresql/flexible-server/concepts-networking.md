---
title: Nätverks översikt – Azure Database for PostgreSQL – flexibel Server
description: Läs om anslutnings-och nätverks alternativ i alternativet för flexibel Server distribution för Azure Database for PostgreSQL
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/21/2021
ms.openlocfilehash: a6f049670a6860bbc195b92458945d1a53029b4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732810"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>Nätverks översikt – Azure Database for PostgreSQL – flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

I den här artikeln beskrivs anslutnings-och nätverks koncept för Azure Database for PostgreSQL-flexibel Server. 

## <a name="choosing-a-networking-option"></a>Välja ett nätverks alternativ
Du har två nätverks alternativ för din Azure Database for PostgreSQL-flexibla Server. Alternativen är **privat åtkomst (VNet-integrering)** och **offentlig åtkomst (tillåtna IP-adresser)**. När servern har skapats måste du välja ett alternativ. 

> [!NOTE]
> Det går inte att ändra nätverks alternativet när servern har skapats. 

* **Privat åtkomst (VNet-integrering)** – du kan distribuera din flexibla server till [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuella Azure-nätverk tillhandahåller privat och säker nätverkskommunikation. Resurser i ett virtuellt nätverk kan kommunicera via privata IP-adresser.

   Välj alternativet VNet-integrering om du vill ha följande funktioner:
   * Ansluta från Azure-resurser i samma virtuella nätverk till den flexibla servern med hjälp av privata IP-adresser
   * Använd VPN eller ExpressRoute för att ansluta från icke-Azure-resurser till din flexibla Server
   * Den flexibla servern har ingen offentlig slut punkt

* **Offentlig åtkomst (tillåtna IP-adresser)** – din flexibla Server nås via en offentlig slut punkt. Den offentliga slut punkten är en DNS-adress som kan matchas offentligt. Frasen "tillåtna IP-adresser" syftar på ett intervall med IP-adresser som du väljer för att ge åtkomst behörighet till servern. Dessa behörigheter kallas **brand Väggs regler**. 

   Välj metoden för offentlig åtkomst om du vill ha följande funktioner:
   * Anslut från Azure-resurser som inte har stöd för virtuella nätverk
   * Ansluta från resurser utanför en Azure som inte är anslutna via VPN eller ExpressRoute 
   * Den flexibla servern har en offentlig slut punkt

Följande egenskaper gäller om du väljer att använda den privata åtkomsten eller det offentliga åtkomst alternativet:
* Anslutningar från tillåtna IP-adresser måste autentiseras till PostgreSQL-servern med giltiga autentiseringsuppgifter
* [Anslutnings kryptering](#tls-and-ssl) är tillgängligt för din nätverks trafik
* Servern har ett fullständigt kvalificerat domän namn (FQDN). För egenskapen hostname i anslutnings strängar rekommenderar vi att du använder FQDN i stället för en IP-adress.
* Båda alternativen kontrollerar åtkomst på server nivå, inte på databas-eller tabell nivå. Du använder PostgreSQL roll egenskaper för att kontrol lera databas, tabell och annan objekt åtkomst.


## <a name="private-access-vnet-integration"></a>Privat åtkomst (VNet-integrering)
Privat åtkomst med Virtual Network (VNet)-integrering tillhandahåller privat och säker kommunikation för din PostgreSQL-flexibla Server.

### <a name="virtual-network-concepts"></a>Koncept för virtuella nätverk
Här följer några begrepp som du bör känna till när du använder virtuella nätverk med PostgreSQL-flexibla servrar.

* **Virtuellt nätverk** – ett Azure-Virtual Network (VNet) innehåller ett privat IP-adressutrymme som har kon figurer ATS för användning. Besök [azure Virtual Network-översikten](../../virtual-network/virtual-networks-overview.md) om du vill veta mer om Azure Virtual Networking.

    Ditt virtuella nätverk måste finnas i samma Azure-region som den flexibla servern.


* **Delegerat undernät** – ett virtuellt nätverk innehåller undernät (under nätverk). Med undernät kan du segmentera ditt virtuella nätverk i mindre adress utrymmen. Azure-resurser distribueras till vissa undernät i ett virtuellt nätverk. 

   Din PostgreSQL-flexibla Server måste finnas i ett undernät som endast är **delegerat** för postgresql-flexibel Server användning. Den här delegeringen innebär att endast Azure Database for PostgreSQL flexibla servrar kan använda det under nätet. Inga andra Azure-resurstyper kan finnas i det delegerade undernätet. Du delegerar ett undernät genom att tilldela dess delegations egenskap som Microsoft. DBforPostgreSQL/flexibleServers.

   Lägg till i `Microsoft.Storage` tjänst slut punkten för under nätet delegerad till flexibla servrar. 

* **Nätverks säkerhets grupper (NSG)** Säkerhets regler i nätverks säkerhets grupper gör att du kan filtrera den typ av nätverks trafik som kan flöda in i och ut ur virtuella nätverks under nät och nätverks gränssnitt. Mer information hittar du i [Översikt över nätverks säkerhets gruppen](../../virtual-network/network-security-groups-overview.md) .


### <a name="unsupported-virtual-network-scenarios"></a>Scenarier med virtuella nätverk som inte stöds
* Offentlig slut punkt (eller offentlig IP eller DNS) – en flexibel server som distribueras till ett virtuellt nätverk kan inte ha en offentlig slut punkt
* När den flexibla servern har distribuerats till ett virtuellt nätverk och undernät kan du inte flytta den till ett annat virtuellt nätverk eller undernät. Det går inte att flytta det virtuella nätverket till en annan resurs grupp eller prenumeration.
* Det går inte att öka under näts storleken (adress utrymmen) när resurserna finns i under nätet
* Peering-virtuella nätverk över regioner stöds inte

Lär dig hur du skapar en flexibel server med privat åtkomst (VNet-integrering) i [Azure Portal](how-to-manage-virtual-network-portal.md) eller [Azure CLI](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Om du använder den anpassade DNS-servern måste du använda en DNS-vidarebefordrare för att matcha FQDN för Azure Database for PostgreSQL-flexibel Server. Läs mer i [namn matchning som använder din egen DNS-Server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) .

## <a name="public-access-allowed-ip-addresses"></a>Offentlig åtkomst (tillåtna IP-adresser)
Egenskaperna för den offentliga åtkomst metoden är:
* Endast de IP-adresser som du tillåter har behörighet att komma åt din PostgreSQL-flexibla Server. Inga IP-adresser tillåts som standard. Du kan lägga till IP-adresser när servern skapas eller efteråt.
* PostgreSQL-servern har ett offentligt matchat DNS-namn
* Din flexibla Server finns inte i något av dina virtuella Azure-nätverk
* Nätverks trafik till och från servern går inte via ett privat nätverk. Trafiken använder allmänna Internet vägar.

### <a name="firewall-rules"></a>Brandväggsregler
Att bevilja behörighet till en IP-adress kallas för en brand Väggs regel. Om ett anslutnings försök kommer från en IP-adress som du inte har tillåtit visas ett fel från den ursprungliga klienten.

Lär dig hur du skapar en flexibel server med offentlig åtkomst (tillåtna IP-adresser) i [Azure Portal](how-to-manage-firewall-portal.md) eller [Azure CLI](how-to-manage-firewall-cli.md).


### <a name="allowing-all-azure-ip-addresses"></a>Tillåta alla Azure IP-adresser
Om en fast utgående IP-adress inte är tillgänglig för din Azure-tjänst kan du överväga att aktivera anslutningar från alla Azure-datacenter-IP-adresser.

> [!IMPORTANT]
> Alternativet **Tillåt offentlig åtkomst från Azure-tjänster och-resurser i Azure** konfigurerar brand väggen för att tillåta alla anslutningar från Azure, inklusive anslutningar från andra kunders prenumerationer. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.

### <a name="troubleshooting-public-access-issues"></a>Felsöka problem med offentlig åtkomst
Tänk på följande när du förväntar dig åtkomst till Microsoft Azure databasen för PostgreSQL Server-tjänsten:

* **Ändringar i listan över tillåtna har inte börjat att fungera än:** Det kan finnas en fördröjning på fem minuter för ändringar i Azure Database for PostgreSQL serverns brand Väggs konfiguration som börjar gälla.

* **Autentiseringen misslyckades:** Om en användare inte har behörighet för Azure Database for PostgreSQL servern eller om lösen ordet som används är felaktigt, nekas anslutningen till Azure Database for PostgreSQL servern. När du skapar en brand Väggs inställning får klienterna en möjlighet att försöka ansluta till servern. Varje klient måste ändå ange nödvändiga säkerhets uppgifter.

* **Dynamisk klient-IP-adress:** Om du har en Internet anslutning med dynamisk IP-adressering och du har problem med att komma åt brand väggen kan du prova någon av följande lösningar:

   * Be Internet leverantören (ISP) om IP-adressintervallet som har tilldelats till de klient datorer som har åtkomst till Azure Database for PostgreSQL-servern och Lägg sedan till IP-adressintervallet som en brand Väggs regel.
   * Hämta statiska IP-adresser i stället för dina klient datorer och Lägg sedan till den statiska IP-adressen som en brand Väggs regel.

* **Brand Väggs regeln är inte tillgänglig för IPv6-format:** Brand Väggs reglerna måste vara i IPv4-format. Om du anger brand Väggs regler i IPv6-format visas validerings felet.

## <a name="hostname"></a>Värdnamn
Oavsett vilket nätverks alternativ du väljer rekommenderar vi att du alltid använder ett fullständigt kvalificerat domän namn (FQDN) som värdnamn när du ansluter till din flexibla Server. Serverns IP-adress är inte garanterad för att vara statisk. Genom att använda FQDN kan du undvika att göra ändringar i anslutnings strängen. 

Exempel
* Rekommenderas `hostname = servername.postgres.database.azure.com`
* Undvik, om möjligt, att använda `hostname = 10.0.0.4` (en privat adress) eller `hostname = 40.2.45.67` (en offentlig adress)


## <a name="tls-and-ssl"></a>TLS och SSL
Azure Database for PostgreSQL-flexibel Server stöder anslutning av klient program till PostgreSQL-tjänsten med hjälp av Transport Layer Security (TLS). TLS är ett bransch standard protokoll som garanterar krypterade nätverks anslutningar mellan databas servern och klient programmen. TLS är ett uppdaterat protokoll av SSL (Secure Sockets Layer).

Azure Database for PostgreSQL-flexibel Server stöder bara krypterade anslutningar med hjälp av Transport Layer Security. Alla inkommande anslutningar med TLS 1.0 och TLS 1.1 kommer att nekas. 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar en flexibel server med **privat åtkomst (VNet-integrering)** i [Azure Portal](how-to-manage-virtual-network-portal.md) eller [Azure CLI](how-to-manage-virtual-network-cli.md).
* Lär dig hur du skapar en flexibel server med **offentlig åtkomst (tillåtna IP-adresser)** i [Azure Portal](how-to-manage-firewall-portal.md) eller [Azure CLI](how-to-manage-firewall-cli.md).
