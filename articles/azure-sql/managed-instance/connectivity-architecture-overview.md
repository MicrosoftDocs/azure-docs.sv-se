---
title: Anslutningsarkitektur
titleSuffix: Azure SQL Managed Instance
description: Lär dig mer om kommunikation och anslutnings arkitektur för Azure SQL-hanterad instans samt hur komponenterna dirigerar trafik till en hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: c91b0231271c6cbcf9ec92c7ad6d25f1bc0f9136
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960477"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Anslutningsarkitektur för Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln förklarar kommunikationen i Azure SQL-hanterad instans. Den beskriver också anslutnings arkitekturen och hur komponenterna dirigerar trafik till en hanterad instans.  

SQL-hanterad instans placeras inuti det virtuella Azure-nätverket och under nätet som är dedikerat till hanterade instanser. Den här distributionen ger:

- En säker privat IP-adress.
- Möjlighet att ansluta ett lokalt nätverk till SQL-hanterad instans.
- Möjligheten att ansluta SQL-hanterad instans till en länkad server eller till ett annat lokalt data lager.
- Möjlighet att ansluta SQL-hanterad instans till Azure-resurser.

## <a name="communication-overview"></a>Översikt över kommunikation

Följande diagram visar entiteter som ansluter till SQL-hanterad instans. Den visar även de resurser som måste kommunicera med en hanterad instans. Kommunikations processen längst ned i diagrammet representerar kund program och verktyg som ansluter till SQL-hanterad instans som data källor.  

![Entiteter i anslutnings arkitektur](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL-hanterad instans är ett PaaS-erbjudande (Platform as a Service). Azure använder automatiserade agenter (hantering, distribution och underhåll) för att hantera den här tjänsten utifrån telemetridata data strömmar. Eftersom Azure ansvarar för hantering, kan kunderna inte komma åt de virtuella SQL-hanterade instansernas virtuella kluster datorer via Remote Desktop Protocol (RDP).

Vissa åtgärder som startas av slutanvändare eller program kan kräva SQL-hanterad instans för att interagera med plattformen. Ett fall är att skapa en SQL-hanterad instans databas. Den här resursen exponeras via Azure Portal, PowerShell, Azure CLI och REST API.

SQL-hanterad instans beror på Azure-tjänster som Azure Storage för säkerhets kopiering, Azure Event Hubs for telemetri, Azure Active Directory (Azure AD) för autentisering, Azure Key Vault för transparent datakryptering (TDE) och ett par Azure Platform-tjänster som tillhandahåller funktioner för säkerhet och support. SQL-hanterad instans gör anslutningar till de här tjänsterna.

All kommunikation krypteras och signeras med hjälp av certifikat. SQL-hanterad instans verifierar de här certifikaten ständigt med listor över återkallade certifikat för att kontrol lera trovärdigheten hos kommunicerande parter. Om certifikaten återkallas stänger SQL Managed instance anslutningar för att skydda data.

## <a name="high-level-connectivity-architecture"></a>Arkitektur med hög nivå anslutning

SQL-hanterad instans är en uppsättning tjänst komponenter på en hög nivå. Dessa komponenter finns på en dedikerad uppsättning isolerade virtuella datorer som körs i kundens virtuella nätverks undernät. De här datorerna utgör ett virtuellt kluster.

Ett virtuellt kluster kan vara värd för flera hanterade instanser. Vid behov expanderar klustret automatiskt eller kontrakt när kunden ändrar antalet etablerade instanser i under nätet.

Kund program kan ansluta till SQL-hanterad instans och kan fråga efter och uppdatera databaser inuti det virtuella nätverket, peer-kopplade virtuella nätverk eller nätverk som är anslutna via VPN eller Azure ExpressRoute. Det här nätverket måste använda en slut punkt och en privat IP-adress.  

![Diagram över anslutnings arkitektur](./media/connectivity-architecture-overview/connectivityarch002.png)

Azures hanterings-och distributions tjänster körs utanför det virtuella nätverket. SQL-hanterad instans och Azure-tjänster ansluter via de slut punkter som har offentliga IP-adresser. När SQL-hanterad instans skapar en utgående anslutning på den mottagande slut nätverks adress översättningen (NAT) ser anslutningen ut som den kommer från den här offentliga IP-adressen.

Hanterings trafik flödar genom kundens virtuella nätverk. Det innebär att element i det virtuella nätverkets infrastruktur kan skada hanterings trafiken genom att göra instansen misslyckad och bli otillgänglig.

> [!IMPORTANT]
> För att förbättra kund upplevelsen och tjänstens tillgänglighet tillämpar Azure en princip för nätverks avsikt på infrastruktur element i Azure Virtual Network. Principen kan påverka hur SQL-hanterad instans fungerar. Den här plattforms mekanismen kommunicerar transparent nätverks krav till användare. Principens huvud mål är att förhindra felaktig nätverks konfiguration och se till att vanliga SQL-hanterade instans åtgärder säkerställs. När du tar bort en hanterad instans tas även principen för nätverks avsikt bort.

## <a name="virtual-cluster-connectivity-architecture"></a>Arkitektur för virtuella kluster anslutningar

Låt oss ta en djupare titt på anslutnings arkitekturen för SQL-hanterad instans. Följande diagram visar den konceptuella layouten för det virtuella klustret.

![Anslutnings arkitektur för det virtuella klustret](./media/connectivity-architecture-overview/connectivityarch003.png)

Klienter ansluter till SQL-hanterad instans med hjälp av ett värdnamn som har formuläret `<mi_name>.<dns_zone>.database.windows.net` . Det här värd namnet matchar en privat IP-adress, men den registreras i en offentlig Domain Name System (DNS)-zon och kan matchas offentligt. Skapas `zone-id` automatiskt när du skapar klustret. Om ett nytt kluster är värd för en sekundär hanterad instans, delar den sitt zon-ID med det primära klustret. Mer information finns i [använda grupper för automatisk redundans för att aktivera transparent och koordinerad redundansväxling av flera databaser](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Den här privata IP-adressen tillhör den interna belastningsutjämnaren för SQL-hanterad instans. Belastningsutjämnaren dirigerar trafik till den SQL-hanterade instans-gatewayen. Eftersom flera hanterade instanser kan köras i samma kluster använder gatewayen SQL-hanterad instans värd namnet för att dirigera om trafik till rätt SQL-motortjänster.

Hanterings-och distributions tjänster ansluter till SQL-hanterad instans med hjälp av en [hanterings slut punkt](#management-endpoint) som mappar till en extern belastningsutjämnare. Trafiken dirigeras till noderna endast om den tas emot på en fördefinierad uppsättning portar som bara hanterings komponenterna i SQL-hanterad instans använder. En inbyggd brand vägg på noderna har kon figurer ATS för att tillåta trafik enbart från Microsoft IP-intervall. Certifikat autentiserar all kommunikation mellan hanterings komponenter och hanterings planet gemensamt.

## <a name="management-endpoint"></a>Hanteringsslutpunkt

Azure hanterar SQL-hanterad instans med hjälp av en hanterings slut punkt. Den här slut punkten finns inuti en instanss virtuella kluster. Hanterings slut punkten skyddas av en inbyggd brand vägg på nätverks nivå. På program nivå skyddas den av ömsesidig certifikat verifiering. Information om hur du hittar slut punktens IP-adress finns i [bestämma hanterings slut punktens IP-adress](management-endpoint-find-ip-address.md).

När anslutningar startar i SQL-hanterad instans (som säkerhets kopiering och gransknings loggar), verkar trafiken starta från hanterings slut punktens offentliga IP-adress. Du kan begränsa åtkomsten till offentliga tjänster från SQL-hanterad instans genom att ange brand Väggs regler så att endast IP-adressen för SQL-hanterad instans anges. Mer information finns i [kontrol lera den inbyggda brand väggen för SQL-hanterad instans](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Trafik som går till Azure-tjänster som är inuti SQL-hanterad instans region är optimerad och därför inte NATed till den offentliga IP-adressen för hanterings slut punkten. Av den anledningen måste tjänsten vara i en annan region än SQL-hanterad instans om du behöver använda IP-baserade brand Väggs regler, oftast för lagring.

## <a name="service-aided-subnet-configuration"></a>Undernätskonfiguration med tjänststöd

SQL-hanterad instans övergår från manuell till tjänstens konfiguration för att hantera kundernas säkerhets-och hanterings krav.

Med konfiguration av tjänstestyrt undernät har användaren full kontroll över data trafik (TDS), medan SQL-hanterad instans tar ansvar för att säkerställa oavbrutet flöde av hanterings trafik för att uppfylla ett service avtal.

Konfiguration av tjänstens konfiguration för under nätet bygger ovanpå funktionen [delegering](../../virtual-network/subnet-delegation-overview.md) av virtuella nätverk för att tillhandahålla automatisk hantering av nätverks konfiguration och aktivera tjänstens slut punkter. 

Tjänst slut punkter kan användas för att konfigurera brand Väggs regler för virtuella nätverk på lagrings konton som upprätthåller säkerhets kopierings-och gransknings loggar. Även om tjänstens slut punkter är aktiverade, uppmuntras kunderna att använda [privat länk](../../private-link/private-link-overview.md) som ger ytterligare säkerhet jämfört med tjänstens slut punkter.

> [!IMPORTANT]
> På grund av konfigurations information för kontroll plan skulle konfiguration av tjänstens under näts undernät inte aktivera tjänstens slut punkter i nationella moln. 

### <a name="network-requirements"></a>Nätverkskrav

Distribuera SQL-hanterad instans i ett dedikerat undernät i det virtuella nätverket. Undernätet måste ha följande egenskaper:

- **Dedikerat undernät:** Under nätet för SQL-hanterad instans får inte innehålla någon annan moln tjänst som är kopplad till den, och det får inte vara ett Gateway-undernät. Under nätet får inte innehålla någon resurs, men SQL-hanterad instans, och du kan inte senare lägga till andra typer av resurser i under nätet.
- **Under näts delegering:** Det SQL-hanterade instans under nätet måste delegeras till `Microsoft.Sql/managedInstances` resurs leverantören.
- **Nätverks säkerhets grupp (NSG):** En NSG måste vara kopplad till under nätet för SQL-hanterad instans. Du kan använda en NSG för att styra åtkomsten till data slut punkten för SQL-hanterad instans genom att filtrera trafik på port 1433 och portar 11000-11999 när SQL-hanterad instans har kon figurer ATS för att omdirigera anslutningar. Tjänsten etablerar och behåller automatiskt de aktuella [reglerna](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) för att tillåta oavbrutet flöde av hanterings trafik.
- **Användardefinierad routningstabell (UDR):** En UDR-tabell måste vara kopplad till under nätet för SQL-hanterad instans. Du kan lägga till poster i routningstabellen för att dirigera trafik som har lokala privata IP-adressintervall som mål via den virtuella nätverksgatewayen eller den virtuella nätverksapparaten (NVA). Tjänsten etablerar automatiskt och upprätthåller de aktuella [poster](#user-defined-routes-with-service-aided-subnet-configuration) som krävs för att tillåta oavbruten hanteringstrafik.
- **Tillräckligt med IP-adresser:** Under nätet för SQL-hanterad instans måste ha minst 32 IP-adresser. Mer information finns i [bestämma storleken på under nätet för SQL-hanterad instans](vnet-subnet-determine-size.md). Du kan distribuera hanterade instanser i [det befintliga nätverket](vnet-existing-add-subnet.md) när du har konfigurerat det för att uppfylla [nätverks kraven för SQL-hanterad instans](#network-requirements). Annars skapar du ett [nytt nätverk och undernät](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> När du skapar en hanterad instans tillämpas en princip för nätverks avsikt i under nätet för att förhindra inkompatibla ändringar av nätverks konfigurationen. När den sista instansen har tagits bort från under nätet tas även principen för nätverks avsikt bort. Reglerna nedan används endast i informations syfte och du bör inte distribuera dem med hjälp av ARM-mallen/PowerShell/CLI. Om du vill använda den senaste officiella mallen kan du alltid [Hämta den från portalen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Obligatoriska inkommande säkerhets regler med konfiguration för tjänstens under näts undernät

| Namn       |Port                        |Protokoll|Källa           |Mål|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI-UNDERNÄT  |Tillåt |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI-UNDERNÄT  |Tillåt |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI-UNDERNÄT  |Tillåt |
|mi_subnet   |Valfri                         |Valfri     |MI-UNDERNÄT        |MI-UNDERNÄT  |Tillåt |
|health_probe|Valfri                         |Valfri     |AzureLoadBalancer|MI-UNDERNÄT  |Tillåt |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Obligatoriska utgående säkerhets regler med konfiguration för tjänstens under näts undernät

| Namn       |Port          |Protokoll|Källa           |Mål|Action|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI-UNDERNÄT        |AzureCloud |Tillåt |
|mi_subnet   |Valfri           |Valfri     |MI-UNDERNÄT        |MI-UNDERNÄT  |Tillåt |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Användardefinierade vägar med konfiguration av tjänstestyrt undernät

|Name|Adressprefix|Nästa hopp|
|----|--------------|-------|
|undernät-till-vnetlocal|MI-UNDERNÄT|Virtuellt nätverk|
|mi-azurecloud-REGION – Internet|AzureCloud. REGION|Internet|
|mi-azurecloud-REGION_PAIR-Internet|AzureCloud.REGION_PAIR|Internet|
|mi-azuremonitor-Internet|AzureMonitor|Internet|
|mi-corpnetpublic-Internet|CorpNetPublic|Internet|
|mi-corpnetsaw-Internet|CorpNetSaw|Internet|
|mi-eventhub-REGION – Internet|EventHub. REGION|Internet|
|mi-eventhub-REGION_PAIR-Internet|EventHub.REGION_PAIR|Internet|
|mi-sqlmanagement-Internet|SqlManagement|Internet|
|mi-Storage – Internet|Storage|Internet|
|mi-Storage-REGION – Internet|Lagring. REGION|Internet|
|mi-Storage-REGION_PAIR-Internet|Storage.REGION_PAIR|Internet|
||||

\* MI-UNDERNÄT syftar på IP-adressintervallet för under nätet i formatet x. x. x/y. Du hittar den här informationen i Azure Portal i under näts egenskaper.

\** Om mål adressen är för en av Azures tjänster dirigerar Azure trafiken direkt till tjänsten via Azures stamnät nätverk, i stället för att dirigera trafiken till Internet. Trafik mellan Azure-tjänster sker inte via Internet, oavsett vilken Azure-region det virtuella nätverket finns i eller vilken Azure-region en instans av Azure-tjänsten har distribuerats i. Mer information finns i [dokumentations sidan för UDR](../../virtual-network/virtual-networks-udr-overview.md).

Dessutom kan du lägga till poster i routningstabellen för att dirigera trafik som har lokala privata IP-adressintervall som mål via den virtuella Nätverksgatewayen eller Virtual Network-apparaten (NVA).

Om det virtuella nätverket innehåller en anpassad DNS-server måste den anpassade DNS-servern kunna matcha offentliga DNS-poster. Om du använder fler funktioner som Azure AD-autentisering kan det krävas att ytterligare FQDN-namn matchas. Mer information finns i [Konfigurera en anpassad DNS](custom-dns-configure.md).

### <a name="networking-constraints"></a>Nätverks begränsningar

**TLS 1,2 tillämpas på utgående anslutningar**: i januari 2020 Microsoft genomdriven TLS 1,2 för trafik inom tjänster i alla Azure-tjänster. För Azure SQL-hanterad instans ledde detta till att TLS 1,2 tillämpas på utgående anslutningar som används för replikering och länkade Server anslutningar till SQL Server. Om du använder versioner av SQL Server äldre än 2016 med SQL-hanterad instans kontrollerar du att [TLS 1,2 vissa uppdateringar](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) har tillämpats.

Följande funktioner för virtuella nätverk stöds för närvarande *inte* med SQL-hanterad instans:

- **Microsoft-peering**: aktivering av [Microsoft-peering](../../expressroute/expressroute-faqs.md#microsoft-peering) på ExpressRoute-kretsar som är direkt eller transitivt med ett virtuellt nätverk där SQL-hanterad instans finns påverkar trafikflöde mellan SQL-hanterade instans komponenter i det virtuella nätverket och de tjänster som den är beroende av, vilket orsakar tillgänglighets problem. SQL-hanterade instans distributioner till virtuella nätverk med Microsoft-peering som redan är aktiverade förväntas fungera.
- **Global virtuell nätverks-peering**: anslutning till [virtuellt nätverk](../../virtual-network/virtual-network-peering-overview.md) i Azure-regioner fungerar inte för SQL-hanterade instanser som placerats i undernät som skapats före 9/22/2020.
- **AzurePlatformDNS**: att använda AzurePlatformDNS- [tjänst tag gen](../../virtual-network/service-tags-overview.md) för att blockera DNS-matchning av plattformar skulle resultera i SQL-hanterad instans inte tillgänglig. Även om SQL-hanterad instans stöder kunddefinierad DNS för DNS-matchning i motorn, finns det ett beroende på plattforms-DNS för plattforms åtgärder.
- **NAT-gateway**: med hjälp av [Azure Virtual Network NAT](../../virtual-network/nat-overview.md) för att kontrol lera utgående anslutningar med en speciell offentlig IP-adress skulle SQL-hanterad instans renderas otillgängligt. SQL Managed instance service är för närvarande begränsad till användning av Basic Load Balancer som inte tillhandahåller samtidiga inkommande och utgående flöden med Virtual Network NAT.
- **IPv6 för Azure Virtual Network**: distribution av SQL-hanterad instans till [dubbla stack IPv4/IPv6-virtuella nätverk](../../virtual-network/ipv6-overview.md) förväntas fungera inte. Om du associerar nätverks säkerhets gruppen (NSG) eller routningstabellen (UDR) som innehåller IPv6-adressprefix till SQL Managed instance-undernätet eller lägger till IPv6-adressprefix till NSG eller UDR som redan är kopplad till under nätet för hanterade instanser skulle rendering av SQL-hanterad instans renderas. SQL-hanterad instans distributioner till ett undernät med NSG och UDR som redan har IPv6-prefix förväntas inte fungera.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- Lär dig hur du [konfigurerar ett nytt virtuellt Azure-nätverk](virtual-network-subnet-create-arm-template.md) eller ett [befintligt virtuellt Azure-nätverk](vnet-existing-add-subnet.md) där du kan distribuera SQL-hanterad instans.
- [Beräkna storleken på det undernät](vnet-subnet-determine-size.md) där du vill distribuera SQL-hanterad instans.
- Lär dig hur du skapar en hanterad instans:
  - Från [Azure Portal](instance-create-quickstart.md).
  - Med hjälp av [PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Med hjälp av [en Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Genom att använda [en Azure Resource Manager mall (med hjälp av hopp, med SSMS ingår)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
