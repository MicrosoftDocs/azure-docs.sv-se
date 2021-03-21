---
title: Nätverks översikt – Azure Database for MySQL flexibel Server
description: Läs om anslutnings-och nätverks alternativ i alternativet för flexibel Server distribution för Azure Database for MySQL
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: a8e2d77ff3c7cb2e4352b21cd87d630331e28660
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906156"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Anslutnings-och nätverks koncept för Azure Database for MySQL-flexibel Server (för hands version)

I den här artikeln beskrivs offentliga och privata anslutnings alternativ för servern. Du får lära dig om nätverks koncepten för Azure Database for MySQL flexibel Server för att skapa en server på ett säkert sätt i Azure.

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är i för hands version.

## <a name="choosing-a-networking-option"></a>Välja ett nätverks alternativ
Du har två nätverks alternativ för din Azure Database for MySQL flexibla Server. Alternativen är **privat åtkomst (VNet-integrering)** och **offentlig åtkomst (tillåtna IP-adresser)**. När servern har skapats måste du välja ett alternativ. 

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
* Anslutningar från tillåtna IP-adresser måste autentiseras till MySQL-servern med giltiga autentiseringsuppgifter
* [Anslutnings kryptering](#tls-and-ssl) är tillgängligt för din nätverks trafik
* Servern har ett fullständigt kvalificerat domän namn (FQDN). För egenskapen hostname i anslutnings strängar rekommenderar vi att du använder FQDN i stället för en IP-adress.
* Båda alternativen kontrollerar åtkomst på server nivå, inte på databas-eller tabell nivå. Du använder MySQLs roll egenskaper för att kontrol lera databas, tabell och annan objekt åtkomst.


## <a name="private-access-vnet-integration"></a>Privat åtkomst (VNet-integrering)
Privat åtkomst med Virtual Network (VNet)-integrering tillhandahåller privat och säker kommunikation för din MySQL-flexibla Server.

### <a name="virtual-network-concepts"></a>Koncept för virtuella nätverk
Här följer några begrepp som du bör känna till när du använder virtuella nätverk med MySQL-flexibla servrar.

* **Virtuellt nätverk** – ett Azure-Virtual Network (VNet) innehåller ett privat IP-adressutrymme som har kon figurer ATS för användning. Besök [azure Virtual Network-översikten](../../virtual-network/virtual-networks-overview.md) om du vill veta mer om Azure Virtual Networking.

    Ditt virtuella nätverk måste finnas i samma Azure-region som den flexibla servern.


* **Delegerat undernät** – ett virtuellt nätverk innehåller undernät (under nätverk). Med undernät kan du segmentera ditt virtuella nätverk i mindre adress utrymmen. Azure-resurser distribueras till vissa undernät i ett virtuellt nätverk. 

   Din MySQL-flexibla Server måste finnas i ett undernät som är **delegerat** för enbart MySQL-flexibel Server användning. Delegeringen innebär att endast flexibla Azure Database for MySQL-servrar kan använda det här undernätet. Inga andra Azure-resurstyper kan finnas i det delegerade undernätet. Du delegerar ett undernät genom att tilldela dess delegations egenskap som Microsoft. DBforMySQL/flexibleServers.

* **Nätverks säkerhets grupper (NSG)** Säkerhets regler i nätverks säkerhets grupper gör att du kan filtrera den typ av nätverks trafik som kan flöda in i och ut ur virtuella nätverks under nät och nätverks gränssnitt. Mer information hittar du i [Översikt över nätverks säkerhets gruppen](../../virtual-network/network-security-groups-overview.md) .


### <a name="unsupported-virtual-network-scenarios"></a>Scenarier med virtuella nätverk som inte stöds
* Offentlig slut punkt (eller offentlig IP eller DNS) – en flexibel server som distribueras till ett virtuellt nätverk kan inte ha en offentlig slut punkt
* När den flexibla servern har distribuerats till ett virtuellt nätverk och undernät kan du inte flytta den till ett annat virtuellt nätverk eller undernät. Det går inte att flytta det virtuella nätverket till en annan resurs grupp eller prenumeration.
* Det går inte att öka under näts storleken (adress utrymmen) när resurserna finns i under nätet
* Peering-virtuella nätverk över regioner stöds inte

Lär dig hur du aktiverar privat åtkomst (VNet-integrering) med hjälp av [Azure Portal](how-to-manage-virtual-network-portal.md) eller [Azure CLI](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Om du använder den anpassade DNS-servern måste du använda en DNS-vidarebefordrare för att matcha FQDN för Azure Database for MySQL-flexibel Server. Läs mer i [namn matchning som använder din egen DNS-Server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) .

## <a name="public-access-allowed-ip-addresses"></a>Offentlig åtkomst (tillåtna IP-adresser)
Egenskaperna för den offentliga åtkomst metoden är:
* Endast de IP-adresser som du tillåter har behörighet att komma åt din MySQL-flexibla Server. Inga IP-adresser tillåts som standard. Du kan lägga till IP-adresser när servern skapas eller efteråt.
* MySQL-servern har ett offentligt matchat DNS-namn
* Din flexibla Server finns inte i något av dina virtuella Azure-nätverk
* Nätverks trafik till och från servern går inte via ett privat nätverk. Trafiken använder allmänna Internet vägar.

### <a name="firewall-rules"></a>Brandväggsregler
Att bevilja behörighet till en IP-adress kallas för en brand Väggs regel. Om ett anslutnings försök kommer från en IP-adress som du inte har tillåtit visas ett fel från den ursprungliga klienten.


### <a name="allowing-all-azure-ip-addresses"></a>Tillåta alla Azure IP-adresser
Om en fast utgående IP-adress inte är tillgänglig för din Azure-tjänst kan du överväga att aktivera anslutningar från alla Azure-datacenter-IP-adresser.

> [!IMPORTANT]
> Alternativet **Tillåt offentlig åtkomst från Azure-tjänster och-resurser i Azure** konfigurerar brand väggen för att tillåta alla anslutningar från Azure, inklusive anslutningar från andra kunders prenumerationer. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.

Lär dig hur du aktiverar och hanterar offentlig åtkomst (tillåtna IP-adresser) med hjälp av [Azure Portal](how-to-manage-firewall-portal.md) eller [Azure CLI](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Felsöka problem med offentlig åtkomst
Tänk på följande när åtkomst till Microsoft Azure databasen för MySQL Server-tjänsten inte fungerar som förväntat:

* **Ändringar i listan över tillåtna har inte börjat att fungera än:** Det kan finnas en fördröjning på fem minuter för ändringar i Azure Database for MySQL serverns brand Väggs konfiguration som börjar gälla.

* **Autentiseringen misslyckades:** Om en användare inte har behörighet för Azure Database for MySQL servern eller om lösen ordet som används är felaktigt, nekas anslutningen till Azure Database for MySQL servern. När du skapar en brand Väggs inställning får klienterna en möjlighet att försöka ansluta till servern. Varje klient måste ändå ange nödvändiga säkerhets uppgifter.

* **Dynamisk klient-IP-adress:** Om du har en Internet anslutning med dynamisk IP-adressering och du har problem med att komma åt brand väggen kan du prova någon av följande lösningar:

   * Be Internet leverantören (ISP) om IP-adressintervallet som har tilldelats till de klient datorer som har åtkomst till Azure Database for MySQL-servern och Lägg sedan till IP-adressintervallet som en brand Väggs regel.
   * Hämta statiska IP-adresser i stället för dina klient datorer och Lägg sedan till den statiska IP-adressen som en brand Väggs regel.
  
* **Brand Väggs regeln är inte tillgänglig för IPv6-format:** Brand Väggs reglerna måste vara i IPv4-format. Om du anger brand Väggs regler i IPv6-format visas validerings felet.


## <a name="hostname"></a>Värdnamn
Oavsett vilket nätverks alternativ du väljer rekommenderar vi att du alltid använder ett fullständigt kvalificerat domän namn (FQDN) som värdnamn när du ansluter till din flexibla Server. Serverns IP-adress är inte garanterad för att vara statisk. Genom att använda FQDN kan du undvika att göra ändringar i anslutnings strängen. 

Exempel
* Rekommenderas `hostname = servername.mysql.database.azure.com`
* Undvik, om möjligt, att använda `hostname = 10.0.0.4` (en privat adress) eller `hostname = 40.2.45.67` (en offentlig IP-adress)



## <a name="tls-and-ssl"></a>TLS och SSL
Azure Database for MySQL flexibel Server stöder anslutning av klient program till MySQL-tjänsten med hjälp av Transport Layer Security (TLS). TLS är ett bransch standard protokoll som garanterar krypterade nätverks anslutningar mellan databas servern och klient programmen. TLS är ett uppdaterat protokoll för Secure Sockets Layer (SSL).

Azure Database for MySQL flexibla servern stöder bara krypterade anslutningar med Transport Layer Security (TLS 1,2). Alla inkommande anslutningar med TLS 1.0 och TLS 1.1 kommer att nekas. Du kan inte inaktivera eller ändra TLS-versionen för anslutning till Azure Database for MySQL flexibel Server.


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du aktiverar privat åtkomst (VNet-integrering) med hjälp av [Azure-portalen](how-to-manage-virtual-network-portal.md) eller [Azure CLI](how-to-manage-virtual-network-cli.md)
* Lär dig hur du aktiverar offentlig åtkomst (tillåtna IP-adresser) med hjälp av [Azure-portalen](how-to-manage-firewall-portal.md) eller [Azure CLI](how-to-manage-firewall-cli.md)
* Lär dig hur du [använder TLS](how-to-connect-tls-ssl.md)
