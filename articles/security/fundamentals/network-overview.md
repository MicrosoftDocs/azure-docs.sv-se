---
title: Begrepp och krav för nätverks säkerhet i Azure | Microsoft Docs
description: Den här artikeln innehåller grundläggande förklaringar om grundläggande begrepp och krav för nätverks säkerhet samt information om vad Azure erbjuder i vart och ett av dessa områden.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 4fb4981925593a22ed65df9d3ed0da5f45062f23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578931"
---
# <a name="azure-network-security-overview"></a>Översikt över nätverkssäkerhet i Azure

Nätverks säkerhet kan definieras som processen att skydda resurser från obehörig åtkomst eller attack genom att tillämpa kontroller på nätverks trafiken. Målet är att säkerställa att endast legitim trafik tillåts. Azure innehåller en robust nätverks infrastruktur som stöder dina anslutnings krav för program och tjänster. Nätverks anslutningen är möjlig mellan resurser som finns i Azure, mellan lokala och Azure-värdbaserade resurser och till och från Internet och Azure.

Den här artikeln beskriver några av de alternativ som Azure erbjuder i avsnittet om nätverks säkerhet. Du kan lära dig mer om:

* Azure-nätverk
* Åtkomstkontroll för nätverk
* Azure Firewall
* Säker fjärråtkomst och anslutning mellan platser
* Tillgänglighet
* Namnmatchning
* DMZ-arkitektur (perimeter Network)
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Övervakning och hot identifiering

## <a name="azure-networking"></a>Azure-nätverk

Azure kräver att virtuella datorer är anslutna till en Azure-Virtual Network. Ett virtuellt nätverk är en logisk konstruktion som byggts ovanpå den fysiska Azures nätverks infrastruktur resurs. Varje virtuellt nätverk är isolerat från alla andra virtuella nätverk. Detta säkerställer att nätverks trafiken i dina distributioner inte är tillgänglig för andra Azure-kunder.

Läs mer:

* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Åtkomstkontroll för nätverk

Nätverks åtkomst kontroll är en begränsning av anslutningen till och från vissa enheter eller undernät i ett virtuellt nätverk. Målet med nätverks åtkomst kontroll är att begränsa åtkomsten till dina virtuella datorer och tjänster till godkända användare och enheter. Åtkomst kontroller baseras på beslut för att tillåta eller neka anslutningar till och från din virtuella dator eller tjänst.

Azure har stöd för flera typer av nätverks åtkomst kontroll, t. ex.:

* Nätverks lager kontroll
* Flödes kontroll och Tvingad tunnel trafik
* Säkerhets enheter för virtuella nätverk

### <a name="network-layer-control"></a>Nätverks lager kontroll

En säker distribution kräver vissa mått för nätverks åtkomst kontroll. Målet med nätverks åtkomst kontroll är att begränsa kommunikation mellan virtuella datorer och de system som behövs. Andra kommunikations försök blockeras.

> [!NOTE]
> Lagrings brand väggar beskrivs i artikeln [Översikt över Azure Storage-säkerhet](../../storage/blobs/security-recommendations.md)

#### <a name="network-security-rules-nsgs"></a>Nätverks säkerhets regler (NSG: er)

Om du behöver grundläggande åtkomst kontroll på nätverks nivå (baserat på IP-adress och TCP-eller UDP-protokoll) kan du använda nätverks säkerhets grupper (NSG: er). En NSG är en grundläggande, tillstånds känslig brand vägg för paket filtrering och gör det möjligt att styra åtkomsten baserat på en [5-tupel](https://www.techopedia.com/definition/28190/5-tuple). NSG: er innehåller funktioner för att förenkla hanteringen och minska sannolikheten för konfigurations misstag:

* **Förstärkta säkerhets regler** FÖREN klar NSG regel definitionen och gör att du kan skapa komplexa regler i stället för att skapa flera enkla regler för att uppnå samma resultat.
* **Service märken** är Microsoft-etiketter som representerar en grupp med IP-adresser. De uppdaterar dynamiskt för att inkludera IP-intervall som uppfyller de villkor som definierar inkluderingen i etiketten. Om du till exempel vill skapa en regel som gäller för alla Azure Storage i regionen öst kan du använda Storage. öster
* Med **program säkerhets grupper** kan du distribuera resurser till program grupper och kontrol lera åtkomsten till dessa resurser genom att skapa regler som använder dessa program grupper. Om du till exempel har webb server som har distribuerats till program gruppen "webservers" kan du skapa en regel som använder en NSG som tillåter 443 trafik från Internet till alla system i program gruppen "webservers".

NSG: er tillhandahåller inte kontroll av program lager eller autentiserade åtkomst kontroller.

Läs mer:

* [Nätverkssäkerhetsgrupper](../../virtual-network/network-security-groups-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC precis i tid för VM-åtkomst

[Azure Security Center](../../security-center/security-center-introduction.md) kan hantera NSG: er på virtuella datorer och låsa åtkomsten till den virtuella datorn tills en användare med [rätt Azure-rollbaserad åtkomst kontroll begär](../../role-based-access-control/overview.md) åtkomst. När användaren har behörigheten ASC gör ändringar i NSG: er att tillåta åtkomst till valda portar under den angivna tiden. När tiden går ut återställs NSG: er till tidigare skyddat tillstånd.

Läs mer:

* [Azure Security Center just-in-Time-åtkomst](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Tjänstslutpunkter

Tjänst slut punkter är ett annat sätt att tillämpa kontrollen över trafiken. Du kan begränsa kommunikationen med tjänster som stöds till just din virtuella nätverk över en direkt anslutning. Trafik från ditt VNet till den angivna Azure-tjänsten finns kvar på Microsoft Azure stamnät nätverket.  

Läs mer:

* [Tjänstslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Flödes kontroll och Tvingad tunnel trafik

Möjligheten att styra routnings beteendet i dina virtuella nätverk är kritisk. Om routning är felaktigt konfigurerat kan program och tjänster som finns på den virtuella datorn ansluta till oauktoriserade enheter, inklusive system som ägs och drivs av potentiella angripare.

Azure-nätverk stöder möjligheten att anpassa routnings beteendet för nätverks trafik i dina virtuella nätverk. På så sätt kan du ändra standard posterna i routningstabellen i det virtuella nätverket. Kontroll över routnings beteende hjälper dig att se till att all trafik från en viss enhet eller grupp av enheter går in eller lämnar ditt virtuella nätverk via en specifik plats.

Du kan till exempel ha en virtuell nätverks säkerhets installation på det virtuella nätverket. Du vill se till att all trafik till och från ditt virtuella nätverk går genom den virtuella säkerhets installationen. Du kan göra detta genom att konfigurera [användardefinierade vägar](../../virtual-network/virtual-networks-udr-overview.md) (UDR) i Azure.

[Tvingad tunnel trafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att se till att dina tjänster inte har tillåtelse att initiera en anslutning till enheter på Internet. Observera att detta skiljer sig från att acceptera inkommande anslutningar och sedan svara på dem. Frontend-webbservrar måste svara på begär Anden från Internet värdar, så att trafik via Internet tillåts inkommande till dessa webb servrar och webb servrarna kan svara.

Det du inte vill tillåta är en front webb server som initierar en utgående begäran. Sådana förfrågningar kan utgöra en säkerhets risk eftersom dessa anslutningar kan användas för att hämta skadlig kod. Även om du vill att dessa frontend-servrar ska initiera utgående begär anden till Internet, kanske du vill tvinga dem att gå igenom dina lokala Webbproxyservrar. På så sätt kan du dra nytta av URL-filtrering och loggning.

I stället vill du använda Tvingad tunnel trafik för att förhindra detta. När du aktiverar Tvingad tunnel trafik tvingas alla anslutningar till Internet via din lokala gateway. Du kan konfigurera Tvingad tunnel trafik genom att dra nytta av UDR.

Läs mer:

* [Vad är användardefinierade vägar och IP-vidarebefordring](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Säkerhets enheter för virtuella nätverk

Även om NSG: er, UDR och Tvingad tunnel trafik ger dig en säkerhets nivå på Nätverks-och transport lager i [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model), kanske du också vill aktivera säkerhet på högre nivåer än nätverket.

Dina säkerhets krav kan till exempel vara:

* Autentisering och auktorisering innan du tillåter åtkomst till ditt program
* Intrångs identifiering och intrångs svar
* Kontroll av program lager för protokoll på hög nivå
* URL-filtrering
* Antivirus program på nätverks nivå och skadlig kod
* Skydd mot bot
* Åtkomst kontroll för program
* Ytterligare DDoS-skydd (ovanför det DDoS-skydd som tillhandahålls av själva Azure-infrastrukturen)

Du kan komma åt dessa förbättrade funktioner för nätverks säkerhet med hjälp av en Azure-partner lösning. Du hittar de mest aktuella Azure Partner Network Security-lösningarna genom att gå till [Azure Marketplace](https://azure.microsoft.com/marketplace/)och söka efter "säkerhet" och "nätverks säkerhet".

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en helt tillståndskänslig brandvägg som en tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. Vissa funktioner är:

* Hög tillgänglighet
* Molnets skalbarhet
* Programmets FQDN-filtreringsregler
* Regler för filtrering av nätverkstrafik

Läs mer:

* [Översikt över Azure Firewall](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Säker fjärråtkomst och anslutning mellan platser

Installation, konfiguration och hantering av dina Azure-resurser måste göras via en fjärr anslutning. Dessutom kanske du vill distribuera [hybrid IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) -lösningar som har komponenter lokalt och i det offentliga Azure-molnet. De här scenarierna kräver säker fjärråtkomst.

Azure-nätverk stöder följande scenarier för säker fjärråtkomst:

* Ansluta enskilda arbets stationer till ett virtuellt nätverk
* Ansluta ditt lokala nätverk till ett virtuellt nätverk med en VPN
* Ansluta ditt lokala nätverk till ett virtuellt nätverk med en särskild WAN-länk
* Ansluta virtuella nätverk till varandra

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Ansluta enskilda arbets stationer till ett virtuellt nätverk

Du kanske vill aktivera enskilda utvecklare eller drift personal för att hantera virtuella datorer och tjänster i Azure. Anta till exempel att du behöver åtkomst till en virtuell dator i ett virtuellt nätverk. Men säkerhets principen tillåter inte RDP-eller SSH-fjärråtkomst till enskilda virtuella datorer. I det här fallet kan du använda en [punkt-till-plats-VPN-](../../vpn-gateway/point-to-site-about.md) anslutning.

Med punkt-till-plats-VPN-anslutningen kan du konfigurera en privat och säker anslutning mellan användaren och det virtuella nätverket. När VPN-anslutningen har upprättats, kan användaren använda RDP eller SSH via VPN-länken till en virtuell dator i det virtuella nätverket. (Detta förutsätter att användaren kan autentisera och auktoriseras.) Punkt-till-plats-VPN stöder:

* SSTP (Secure Socket Tunneling Protocol), ett patentskyddat SSL-baserat VPN-protokoll. En SSL VPN-lösning kan tränga in brand väggar, eftersom de flesta brand väggar öppnar TCP-port 443, som TLS/SSL använder. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Läs mer:

* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Ansluta ditt lokala nätverk till ett virtuellt nätverk med en VPN

Du kanske vill ansluta hela företags nätverket eller delar av det till ett virtuellt nätverk. Detta är vanligt i hybrid IT-scenarier där organisationer [utökar sitt lokala data Center till Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). I många fall är organisationer värd delar av en tjänst i Azure och delar lokalt. De kan till exempel göra det när en lösning innehåller front webb servrar i Azure och backend-databaser lokalt. De här typerna av "Cross-premises"-anslutningar gör hanteringen av Azure-baserade resurser säkrare och aktiverar scenarier som att utöka Active Directory domänkontrollanter till Azure.

Ett sätt att åstadkomma detta är att använda en [VPN för plats-till-plats](https://www.techopedia.com/definition/30747/site-to-site-vpn). Skillnaden mellan en plats-till-plats-VPN-anslutning och en punkt-till-plats-VPN är att den senare ansluter en enda enhet till ett virtuellt nätverk. En plats-till-plats-VPN ansluter ett helt nätverk (till exempel ditt lokala nätverk) till ett virtuellt nätverk. VPN för plats-till-plats-VPN till ett virtuellt nätverk använder det mycket säkra IPsec tunnel läge VPN-protokollet.

Läs mer:

* [Skapa ett virtuellt Resource Manager-nätverk med en VPN-anslutning från plats till plats med hjälp av Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Om VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Ansluta ditt lokala nätverk till ett virtuellt nätverk med en särskild WAN-länk

Punkt-till-plats-och plats-till-plats-VPN-anslutningar är effektiva för att aktivera anslutningar mellan platser. Vissa organisationer anser dock att de har följande nack delar:

* VPN-anslutningar flyttar data via Internet. Detta visar de här anslutningarna till potentiella säkerhets problem som rör flyttning av data i ett offentligt nätverk. Dessutom kan inte tillförlitlighet och tillgänglighet för Internet anslutningar garanteras.
* VPN-anslutningar till virtuella nätverk kanske inte har bandbredd för vissa program och syfte, eftersom de maximala gränsen är cirka 200 Mbit/s.

Organisationer som behöver den högsta säkerhets nivån och tillgänglighet för sina anslutningar mellan olika platser använder vanligt vis dedikerade WAN-länkar för att ansluta till fjärranslutna platser. Azure ger dig möjlighet att använda en särskild WAN-länk som du kan använda för att ansluta ditt lokala nätverk till ett virtuellt nätverk. Azure ExpressRoute, Express Route Direct och Express Route Global Reach aktiverar detta.

Läs mer:

* [Teknisk översikt för ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute Direct](../../expressroute/expressroute-erdirect-about.md)
* [Snabb routning för global räckvidd](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Ansluta virtuella nätverk till varandra

Det går att använda många virtuella nätverk för dina distributioner. Det finns olika orsaker till varför du kan göra detta. Du kanske vill förenkla hanteringen, eller så kanske du vill ha ökad säkerhet. Oavsett motiveringen för att lägga till resurser i olika virtuella nätverk kan det finnas tillfällen när du vill att resurser i vart och ett av nätverken ska anslutas till varandra.

Ett alternativ är för tjänster i ett virtuellt nätverk för att ansluta till tjänster i ett annat virtuellt nätverk, med "loopa tillbaka" via Internet. Anslutningen startar på ett virtuellt nätverk, går via Internet och kommer sedan tillbaka till det virtuella mål nätverket. Det här alternativet visar anslutningen till de säkerhets problem som finns i en Internetbaserad kommunikation.

Ett bättre alternativ kan vara att skapa en plats-till-plats-VPN som ansluter mellan två virtuella nätverk. Den här metoden använder samma [IPSec tunnel mode](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) -protokoll som den plats-till-plats-VPN-anslutning som anges ovan.

Fördelen med den här metoden är att VPN-anslutningen upprättas via Azures nätverks infrastruktur resurs, i stället för att ansluta via Internet. Detta ger dig ett extra säkerhets lager, jämfört med plats-till-plats-VPN som ansluter via Internet.

Läs mer:

* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ett annat sätt att ansluta till virtuella nätverk är  [VNet-peering](../../virtual-network/virtual-network-peering-overview.md). Med den här funktionen kan du ansluta två Azure-nätverk så att kommunikationen mellan dem sker över Microsofts stamnät infrastruktur utan att det någonsin går via Internet. VNET-peering kan ansluta två virtuella nätverk inom samma region eller två virtuella nätverk i Azure-regioner. NSG: er kan användas för att begränsa anslutningen mellan olika undernät eller system.

## <a name="availability"></a>Tillgänglighet

Tillgänglighet är en viktig komponent i alla säkerhets program. Om dina användare och system inte kan komma åt vad de behöver för att komma åt nätverket, kan tjänsten betraktas som komprometterad. Azure har nätverks tekniker som stöder följande mekanismer med hög tillgänglighet:

* HTTP-baserad belastnings utjämning
* Belastnings utjämning på nätverks nivå
* Global belastnings utjämning

Belastnings utjämning är en mekanism som har utformats för att fördela anslutningar mellan flera enheter. Målen med belastnings utjämning är:

* För att öka tillgängligheten. När du belastningsutjämna anslutningar över flera enheter kan en eller flera av enheterna bli otillgängliga utan att kompromissa med tjänsten. Tjänsterna som körs på de återstående online enheterna kan fortsätta att betjäna innehållet från tjänsten.
* För att öka prestandan. När du läser in balans anslutningar över flera enheter behöver en enskild enhet inte hantera all bearbetning. I stället sprids bearbetnings-och minnes kraven för att hantera innehållet över flera enheter.

### <a name="http-based-load-balancing"></a>HTTP-baserad belastnings utjämning

Organisationer som kör webbaserade tjänster vill ofta ha en HTTP-baserad belastningsutjämnare framför dessa webb tjänster. Detta säkerställer tillräcklig prestanda och hög tillgänglighet. Traditionella, nätverksbaserade belastningsutjämnare förlitar sig på Nätverks-och transport nivå protokoll. HTTP-baserade belastningsutjämnare, å andra sidan, fatta beslut baserat på egenskaperna hos HTTP-protokollet.

Azure Application Gateway tillhandahåller HTTP-baserad belastnings utjämning för dina webbaserade tjänster. Application Gateway stöder:

* Cookie-baserad session tillhörighet. Den här funktionen säkerställer att anslutningar som upprättats till en av servrarna bakom denna belastningsutjämnare förblir intakta mellan klienten och servern. Detta säkerställer stabiliteten för transaktioner.
* TLS-avlastning. När en klient ansluter till belastningsutjämnaren krypteras sessionen med hjälp av HTTPS-protokollet (TLS). För att öka prestanda kan du dock använda HTTP-protokollet (unkrypterat) för att ansluta mellan belastningsutjämnaren och webb servern bakom belastningsutjämnaren. Detta kallas för "TLS-avlastning", eftersom webb servrarna bakom belastningsutjämnaren inte förväntar sig den processor kraft som ingår i krypteringen. Webb servrarna kan därför snabbare betjäna tjänsten.
* URL-baserad innehålls dirigering. Den här funktionen gör det möjligt för belastningsutjämnaren att fatta beslut om vart anslutningar ska vidarebefordras baserat på mål-URL: en. Detta ger stor flexibilitet än lösningar som fattar belastnings Utjämnings beslut baserat på IP-adresser.

Läs mer:

* [Översikt över Application Gateway](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>Belastnings utjämning på nätverks nivå

I motsats till HTTP-baserad belastnings utjämning, fattar belastnings utjämning på nätverks nivå beslut baserat på IP-adress och port (TCP eller UDP).
Du kan få fördelarna med belastnings utjämning på nätverks nivå i Azure med hjälp av Azure Load Balancer. Några viktiga egenskaper för Load Balancer är:

* Belastnings utjämning på nätverks nivå baserat på IP-adress och port nummer.
* Stöd för alla program skikts protokoll.
* Belastnings utjämning till Azure Virtual Machines och Cloud Services-roll instanser.
* Kan användas för både Internet-riktade (extern belastnings utjämning) och program som inte är från Internet (interna belastnings utjämning) och virtuella datorer.
* Slut punkts övervakning, som används för att avgöra om någon av tjänsterna bakom belastningsutjämnaren har blivit otillgänglig.

Läs mer:

* [Belastningsutjämnare mot Internet mellan flera virtuella datorer eller tjänster](../../load-balancer/load-balancer-overview.md)
* [Översikt över intern belastnings utjämning](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>Global belastnings utjämning

Vissa organisationer vill ha den högsta möjliga tillgänglighets nivån. Ett sätt att uppnå det här målet är att vara värd för program i globalt distribuerade Data Center. När ett program finns i Data Center som finns i hela världen, är det möjligt att en hel politisk region blir otillgänglig och fortfarande har programmet igång.

Denna belastnings Utjämnings strategi kan också ge prestanda förmåner. Du kan dirigera begär Anden om tjänsten till data centret som ligger närmast enheten som gör begäran.

I Azure kan du få fördelarna med global belastnings utjämning med hjälp av Azure Traffic Manager.

Läs mer:

* [Vad är Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Namnmatchning

Namn matchning är en kritisk funktion för alla tjänster som du är värd för i Azure. Från ett säkerhets perspektiv kan komprometterandet av namn matchnings funktionen leda till att en angripare omdirigerar begär Anden från dina platser till en angripares webbplats. Säker namn matchning är ett krav för alla värdbaserade tjänster i molnet.

Det finns två typer av namn matchning som du behöver åtgärda:

* Intern namn matchning. Detta används av tjänster i dina virtuella nätverk, i dina lokala nätverk eller både och. Namn som används för intern namn matchning är inte tillgängliga via Internet. För optimal säkerhet är det viktigt att ditt interna namn matchnings schema inte är tillgängligt för externa användare.
* Extern namn matchning. Detta används av personer och enheter utanför dina lokala nätverk och virtuella nätverk. Detta är namnen som är synliga för Internet och används för att dirigera anslutningen till dina molnbaserade tjänster.

För intern namn matchning har du två alternativ:

* En virtuell nätverks-DNS-server. När du skapar ett nytt virtuellt nätverk skapas en DNS-server åt dig. Den här DNS-servern kan matcha namnen på de datorer som finns i det virtuella nätverket. Den här DNS-servern kan inte konfigureras, hanteras av Azure Fabric Manager och kan därför hjälpa dig att skydda din lösning för namn matchning.
* Ta med din egen DNS-server. Du har möjlighet att lägga till en DNS-server som du väljer i det virtuella nätverket. Den här DNS-servern kan vara en Active Directory integrerad DNS-server, eller en dedikerad DNS-server-lösning som tillhandahålls av en Azure-partner, som du kan hämta från Azure Marketplace.

Läs mer:

* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)
* [Hantera DNS-servrar som används av ett virtuellt nätverk](../../virtual-network/manage-virtual-network.md#change-dns-servers)

För extern namn matchning har du två alternativ:

* Vara värd för din egen externa DNS-server lokalt.
* Var värd för din egna externa DNS-server hos en tjänst leverantör.

Många stora organisationer är värdar för sina egna DNS-servrar lokalt. De kan göra detta eftersom de har expert kunskaper om nätverk och global närvaro för detta.

I de flesta fall är det bättre att vara värd för DNS-namnmatchning hos en tjänst leverantör. Dessa leverantörer har nätverks kunskaper och global närvaro för att säkerställa mycket hög tillgänglighet för dina namn matchnings tjänster. Tillgängligheten är nödvändig för DNS-tjänster, eftersom det inte går att komma åt dina Internet tjänster om dina namn matchnings tjänster Miss lyckas.

Azure ger dig en hög tillgänglig och högpresterande extern DNS-lösning i form av Azure DNS. Den här lösningen för extern namn matchning drar nytta av den globala Azure DNSs infrastrukturen. Det gör att du kan vara värd för din domän i Azure med samma autentiseringsuppgifter, API: er, verktyg och fakturering som dina andra Azure-tjänster. Som en del av Azure ärver den också de starka säkerhets kontroller som är inbyggda i plattformen.

Läs mer:

* [Översikt över Azure DNS](../../dns/dns-overview.md)
* [Azure DNS privata zoner](../../dns/private-dns-overview.md) kan du konfigurera privata DNS-namn för Azure-resurser i stället för automatiskt tilldelade namn utan att behöva lägga till en anpassad DNS-lösning.

## <a name="perimeter-network-architecture"></a>Nätverks arkitektur för perimeter

Många stora organisationer använder perimeternätverk för att segmentera sina nätverk och skapa en buffertzon mellan Internet och deras tjänster. Perimeternätverket i nätverket betraktas som en låg säkerhets zon och inga höga värde till gångar placeras i det nätverks segmentet. Du kan vanligt vis se nätverks säkerhets enheter som har ett nätverks gränssnitt i perimeternätverket. Ett annat nätverks gränssnitt är anslutet till ett nätverk som har virtuella datorer och tjänster som accepterar inkommande anslutningar från Internet.

Du kan utforma perimeter-nätverk på ett antal olika sätt. Beslutet att distribuera ett perimeternätverk, och sedan vilken typ av perimeternätverk som ska användas om du bestämmer dig för att använda ett, beror på dina nätverks säkerhets krav.

Läs mer:

* [Microsoft Cloud tjänster och nätverks säkerhet](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Distribuerade överbelastningsattacker (DDoS) är några av de största tillgänglighets- och säkerhetsproblemen för kunder som flyttar sina program till molnet. Ett DDoS-angrepp försöker att belasta ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet.
Microsoft tillhandahåller DDoS-skydd som kallas **Basic** som en del av Azure-plattformen. Detta kostar ingen kostnad och omfattar alltid övervakning och real tids minskning av vanliga attacker på nätverks nivå. Förutom de skydd som ingår i DDoS Protection **Basic** kan du aktivera **standard** alternativet. DDoS Protection standard funktionerna är:

* **Inbyggd plattforms integrering:** Inbyggt i Azure. Inkluderar konfiguration via Azure Portal. DDoS Protection standard förstår dina resurser och resurs konfigurationen.
* **Skydd mot nyckel:** Förenklad konfiguration skyddar omedelbart alla resurser i ett virtuellt nätverk så snart DDoS Protection standard har Aktiver ATS. Ingen åtgärd eller användar definition krävs. DDoS Protection standard och minimerar risken automatiskt när den har identifierats.
* **Övervaka alltid trafik:** Dina program trafik mönster övervakas 24 timmar per dag, 7 dagar i veckan, och letar efter indikatorer för DDoS-attacker. Minskning utförs när skydds principerna överskrids.
* **Rapporter om attack minskning** Rapporter om attack minskning använder aggregerade nätverks flödes data för att tillhandahålla detaljerad information om attacker riktade mot dina resurser.
* **Flödes loggar för attack minskning** Med anslutnings åtgärder för attackering kan du granska tappad trafik, vidarebefordrad trafik och andra attack data i nära real tid under en aktiv DDoS-attack.
* **Anpassningsbar justering:** Intelligent trafik profilering lär sig programmets trafik över tid och väljer och uppdaterar den profil som är lämplig för din tjänst. Profilen justeras när trafiken förändras över tid. Skikt 3 till Layer 7-skydd: ger fullständig stack-DDoS skydd när det används med en brand vägg för webbaserade program.
* **Omfattande minsknings skala:** Över 60 olika typer av attacker kan begränsas, med global kapacitet, för att skydda mot de största kända DDoS-attacker.
* **Angrepps mått:** Sammanfattade mått från varje attack är tillgängliga via Azure Monitor.
* **Attack avisering:** Aviseringar kan konfigureras vid start och stopp av ett angrepp och under angriparens varaktighet, med hjälp av inbyggda angrepps mått. Aviseringar integreras i din operativa program vara, t. ex. Microsoft Azure övervaka loggar, Splunk, Azure Storage, e-post och Azure Portal.
* **Kostnads garanti:**  Service krediter för data överföring och skalbarhet för program för dokumenterade DDoS-attacker.
* **DDoS snabb svars** tid DDoS Protection standard kunder har nu till gång till Rapid Response-teamet under en aktiv attack. DRR kan hjälpa till med angrepps utredning, anpassade åtgärder under ett angrepp och analys efter angrepp.


Läs mer:

* [Översikt över DDOS-skydd](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Med Azures frontend-tjänst kan du definiera, hantera och övervaka den globala routningen av din webb trafik. Det optimerar trafikens routning för bästa prestanda och hög tillgänglighet. Med Azure Front Door kan du skapa anpassade regler för brandväggen för webbaserade program (WAF) vid åtkomstkontroll, för att skydda din HTTP/HTTPS-arbetsbelastning från utnyttjande som baseras på klienternas IP-adresser, landskod och HTTP-parametrar. Dessutom kan du med frontend-dörren också skapa regler för begränsning av skadlig bot-trafik, däribland TLS-avlastning och per HTTP/HTTPS-begäran, bearbetning av program lager.

Själva Front Door-plattformen är skyddad av Azure DDoS Protection Basic. Om du behöver mer skydd kan Azure DDoS Protection Standard aktiveras på dina virtuella nätverk och skydda resurser från nätverkslagerattacker (TCP/UDP) med hjälp av automatiska justeringar och åtgärder. Front dörren är en omvänd proxy för Layer 7, men den tillåter bara att webb trafik passerar till Server dels servrar och blockerar andra typer av trafik som standard.

Läs mer:

* Mer information om hela uppsättningen med funktioner i Azure-frontend finns i [Översikt över Azures front dörr](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Azure Traffic Manager är en lastbalanserare för DNS-baserad trafik som hjälper dig att distribuera trafiken optimalt till tjänster i globala Azure-regioner, med hög tillgänglighet och korta svarstider. Traffic Manager använder DNS för att dirigera klientbegäranden till den lämpligaste tjänstslutpunkten baserat på en trafikdirigeringsmetod och slutpunkternas hälsostatus. En slutpunkt är en Internetansluten tjänst i eller utanför Azure. Traffic Manager övervakar slut punkterna och dirigerar inte trafik till några slut punkter som inte är tillgängliga.

Läs mer:

* [Översikt över Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Övervakning och hot identifiering

Azure innehåller funktioner som hjälper dig i det här nyckelområdet med tidig identifiering, övervakning och insamling och hantering av nätverks trafik.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher kan hjälpa dig att felsöka och innehåller en helt ny uppsättning verktyg som hjälper dig att identifiera säkerhets problem.

[Vyn säkerhets grupp](../../network-watcher/network-watcher-security-group-view-overview.md) hjälper till med gransknings-och säkerhetskompatibilitet för Virtual Machines. Använd den här funktionen för att utföra program granskningar och jämföra de bas linje principer som definierats av din organisation för att skapa effektiva regler för var och en av dina virtuella datorer. Detta kan hjälpa dig att identifiera konfigurations avvikelser.

Med [paket fångst](../../network-watcher/network-watcher-packet-capture-overview.md) kan du samla in nätverks trafik till och från den virtuella datorn. Du kan samla in nätverks statistik och felsöka program problem, vilket kan vara värdefullt vid undersökningen av nätverks intrång. Du kan också använda den här funktionen tillsammans med Azure Functions för att starta Nätverks avbildningar som svar på vissa Azure-aviseringar.

Mer information om Network Watcher och hur du börjar testa några av funktionerna i dina labb finns i [Översikt över Azure Network Watcher Monitoring](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Om du vill ha de senaste meddelandena om tillgänglighet och status för den här tjänsten kontrollerar du [sidan Azure-uppdateringar](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center hjälper dig att förhindra, identifiera och svara på hot, och ger dig ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Den ger integrerad säkerhetsövervakning och princip hantering över dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå vidare och fungerar med en stor uppsättning säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka nätverks säkerhet genom att:

* Tillhandahålla nätverks säkerhets rekommendationer.
* Övervakning av nätverks säkerhets konfigurationens tillstånd.
* Aviserar dig om nätverksbaserade hot, både i slut punkts-och nätverks nivåerna.

Läs mer:

* [Introduktion till Azure Security Center](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>Virtual Network trycka på

Med hjälp av Azure Virtual Network-TRYCKNINGen (Terminal Access Point) kan du kontinuerligt strömma din virtuella dators nätverks trafik till en insamlare eller ett analys verktyg för nätverks paket. Insamlings-eller analys verktyget tillhandahålls av en virtuell nätverks utrustnings partner. Du kan använda samma virtuella nätverk för att aggregera trafik från flera nätverks gränssnitt i samma eller olika prenumerationer.

Läs mer:

* [Virtual Network TAP](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Loggning

Loggning på en nätverks nivå är en nyckel funktion för alla nätverks säkerhets scenarier. I Azure kan du logga information som hämtats för NSG: er för att hämta information om loggning på nätverks nivå. Med NSG-loggning får du information från:

* [Aktivitets loggar](../../azure-monitor/essentials/platform-logs-overview.md). Använd dessa loggar om du vill visa alla åtgärder som skickats till dina Azure-prenumerationer. Loggarna är aktiverade som standard och kan användas i Azure Portal. De kallades tidigare för gransknings-eller drift loggar.
* Händelse loggar. Dessa loggar innehåller information om vilka NSG-regler som tillämpades.
* Räknar loggar. Med dessa loggar kan du se hur många gånger varje regel för NSG har tillämpats för att neka eller tillåta trafik.

Du kan också använda [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), ett kraftfullt verktyg för data visualisering, för att visa och analysera dessa loggar.
Läs mer:

* [Azure Monitor loggar för nätverks säkerhets grupper (NSG: er)](../../virtual-network/virtual-network-nsg-manage-log.md)