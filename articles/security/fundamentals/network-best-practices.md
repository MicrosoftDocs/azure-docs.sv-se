---
title: Metod tips för nätverks säkerhet – Microsoft Azure
description: Den här artikeln innehåller en uppsättning metod tips för nätverks säkerhet med inbyggda Azure-funktioner.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 68f3b9f9cfa8ea74a8240fab6e47bf737788081f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094332"
---
# <a name="azure-best-practices-for-network-security"></a>Bästa praxis i Azure för nätverkssäkerhet
I den här artikeln beskrivs en samling Azure Best Practices för att förbättra nätverks säkerheten. Dessa bästa metoder är härledda från vår erfarenhet av Azure-nätverk och våra kunders upplevelser som du själv har.

För varje bästa praxis förklarar den här artikeln:

* Det bästa tillvägagångs sättet är
* Varför du vill aktivera den bästa metoden
* Detta kan bero på att du inte kan aktivera bästa praxis
* Möjliga alternativ till bästa praxis
* Hur du kan lära dig att aktivera bästa praxis

De här metod tipsen bygger på ett samförstånds yttrande och Azures plattforms funktioner och funktions uppsättningar, eftersom de fanns när den här artikeln skrevs. Åsikter och tekniker förändras med tiden och den här artikeln uppdateras regelbundet för att avspegla ändringarna.

## <a name="use-strong-network-controls"></a>Använd starka nätverks kontroller
Du kan ansluta [virtuella Azure-datorer (VM)](https://azure.microsoft.com/services/virtual-machines/) och-enheter till andra nätverksanslutna enheter genom att placera dem på [virtuella Azure-nätverk](../../virtual-network/index.yml). Det innebär att du kan ansluta virtuella nätverks gränssnitts kort till ett virtuellt nätverk för att tillåta TCP/IP-baserad kommunikation mellan nätverks aktiverade enheter. Virtuella datorer som är anslutna till ett virtuellt Azure-nätverk kan ansluta till enheter i samma virtuella nätverk, olika virtuella nätverk, Internet eller dina egna lokala nätverk.

När du planerar nätverket och säkerheten i nätverket rekommenderar vi att du centraliserar:

- Hantering av kärn nätverks funktioner som ExpressRoute, Virtual Network och Subnet etablering och IP-adressering.
- Styrning av nätverks säkerhets element, till exempel virtuella nätverks installationer fungerar som ExpressRoute, Virtual Network och Subnet etablering och IP-adressering.

Om du använder en gemensam uppsättning hanterings verktyg för att övervaka nätverket och säkerheten i nätverket får du tydlig insyn i båda. En enkel, enhetlig säkerhets strategi minskar fel eftersom det ökar mänsklig förståelse och tillförlitligheten för Automation.

## <a name="logically-segment-subnets"></a>Logiskt logiskt segment undernät
Virtuella Azure-nätverk liknar lokala nätverk i det lokala nätverket. Idén bakom ett virtuellt Azure-nätverk är att du skapar ett nätverk, baserat på ett enda privat IP-adressutrymme, där du kan placera alla dina virtuella Azure-datorer. De privata IP-adress utrymmena som är tillgängliga finns i klass A (10.0.0.0/8), klass B (172.16.0.0/12) och klass C-intervall (192.168.0.0/16).

Metod tips för logiskt segmenterade undernät är:

**Bästa praxis**: Tilldela inte tillåtna regler med breda intervall (till exempel Tillåt 0.0.0.0 till och med 255.255.255.255).  
**Information**: se till att fel söknings procedurer förhindrar eller förhindrar att de här typerna av regler anges. De här reglerna leder till en falsk säkerhets idé och används ofta och utnyttjas av röda team.

**Bästa praxis**: segmentera det större adress utrymmet i undernät.   
**Information**: Använd [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-baserade undernät för undernät för att skapa undernät.

**Bästa praxis**: skapa nätverks åtkomst kontroller mellan undernät. Routning mellan undernät sker automatiskt och du behöver inte konfigurera vägvals tabeller manuellt. Som standard finns det inga nätverks åtkomst kontroller mellan de undernät som du skapar i ett virtuellt Azure-nätverk.   
**Information**: Använd en [nätverks säkerhets grupp](../../virtual-network/virtual-network-vnet-plan-design-arm.md) för att skydda mot oombedd trafik i Azure-undernät. Nätverks säkerhets grupper är enkla, tillstånds känsliga paket gransknings enheter som använder 5-tupel-metoden (käll-IP, källport, mål-IP, målport och nivå 4-protokoll) för att skapa regler för att tillåta/neka för nätverks trafik. Du tillåter eller nekar trafik till och från en enskild IP-adress, till och från flera IP-adresser, eller till och från hela undernät.

När du använder nätverks säkerhets grupper för nätverks åtkomst kontroll mellan undernät kan du ange resurser som tillhör samma säkerhets zon eller roll i sina egna undernät.

**Bästa praxis**: Undvik små virtuella nätverk och undernät så att det blir enklare och flexibelt.   
**Information**: de flesta organisationer lägger till fler resurser än vad som ursprungligen planeras och omtilldelar adresser är arbets krävande. Med små undernät läggs ett begränsat säkerhets värde till och mappar en nätverks säkerhets grupp till varje undernät lägger till resurser. Definiera undernät i stor storlek för att säkerställa att du har flexibilitet för tillväxt.

**Bästa praxis**: förenkla hanteringen av regel hantering för nätverks säkerhets grupper genom att definiera [program säkerhets grupper](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Information**: definiera en program säkerhets grupp för listor över IP-adresser som du tror kan ändras i framtiden eller som ska användas över flera nätverks säkerhets grupper. Se till att namnge program säkerhets grupper så att andra kan förstå deras innehåll och syfte.

## <a name="adopt-a-zero-trust-approach"></a>Använd en metod som saknar förtroende
Perimeter-baserade nätverk använder antagandet att alla system i ett nätverk är betrodda. Men dagens anställda får åtkomst till organisationens resurser från var som helst på en rad olika enheter och appar, vilket gör att perimeternätverket inte är irrelevanta. Principer för åtkomst kontroll som bara fokuserar på vem som har åtkomst till en resurs är inte tillräckligt. För att du ska kunna hantera balansen mellan säkerhet och produktivitet måste säkerhets administratörerna också ta hänsyn till *hur* en resurs nås.

Nätverk behöver utvecklas från traditionella försvars gränser eftersom nätverk kan vara sårbara för intrång: en angripare kan kompromettera en enda slut punkt inom den betrodda gränser och sedan snabbt expandera en fäste över hela nätverket. [0 förtroende](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) nätverk eliminerar begreppet förtroende utifrån nätverks platsen inom en perimeter. I stället använder noll Trust-arkitekturer enhets-och användar förtroende anspråk för att få åtkomst till organisationens data och resurser. För nya initiativ bör du ange noll förtroende metoder som verifierar förtroende vid tidpunkten för åtkomst.

Bästa praxis är:

**Bästa praxis**: ge villkorlig åtkomst till resurser baserat på enhet, identitet, säkerhet, nätverks plats och mycket annat.  
**Information**: med [villkorlig åtkomst i Azure AD](../../active-directory/conditional-access/overview.md) kan du tillämpa rätt åtkomst kontroller genom att implementera automatiska åtkomst kontroll beslut baserat på de villkor som krävs. Mer information finns i [Hantera åtkomst till Azure-hantering med villkorlig åtkomst](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).

**Bästa praxis**: Aktivera endast port åtkomst efter arbets flödes godkännande.  
**Information**: du kan använda [just-in-Time VM-åtkomst i Azure Security Center](../../security-center/security-center-just-in-time.md) för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs.

**Bästa praxis**: bevilja tillfälliga behörigheter för att utföra privilegierade uppgifter, vilket förhindrar att obehöriga eller obehöriga användare får åtkomst när behörigheterna har upphört att gälla. Åtkomst beviljas endast när användare behöver den.  
**Information**: Använd just-in-Time-åtkomst i Azure AD Privileged Identity Management eller i en lösning från tredje part för att ge behörighet att utföra privilegierade uppgifter.

Noll Trust är nästa utveckling av nätverks säkerhet. Statusen för cyberattacker Drivers-organisationer att ta "förmoda överträdelse"-tänkesätt, men den här metoden bör inte begränsas. Med noll Trust-nätverk skyddas företags data och resurser samtidigt som organisationer kan bygga en modern arbets plats genom att använda tekniker som gör det möjligt för anställda att vara produktiva när som helst, var som helst.

## <a name="control-routing-behavior"></a>Beteende för styr routning
När du publicerar en virtuell dator i ett virtuellt Azure-nätverk kan den virtuella datorn ansluta till en annan virtuell dator i samma virtuella nätverk, även om de andra virtuella datorerna finns i olika undernät. Detta är möjligt eftersom en samling system vägar som är aktiverade som standard tillåter den här typen av kommunikation. Dessa standard vägar gör det möjligt för virtuella datorer i samma virtuella nätverk att initiera anslutningar med varandra, och med Internet (endast för utgående kommunikation till Internet).

Även om standard system vägar är användbara för många distributions scenarier finns det tillfällen när du vill anpassa konfigurationen av routningen för dina distributioner. Du kan konfigurera nästa hopp adress för att uppnå vissa mål.

Vi rekommenderar att du konfigurerar [användardefinierade vägar](../../virtual-network/virtual-networks-udr-overview.md) när du distribuerar en säkerhetsfunktion för ett virtuellt nätverk. Vi pratar om detta i ett senare avsnitt med rubriken [säkra dina kritiska Azure-tjänsteresurser till endast dina virtuella nätverk](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Användardefinierade vägar krävs inte och standard system vägar fungerar vanligt vis.
>
>

## <a name="use-virtual-network-appliances"></a>Använda virtuella nätverks enheter
Nätverks säkerhets grupper och användardefinierad routning kan ge ett visst mått på nätverks säkerhet på Nätverks-och transport skikten i OSI- [modellen](https://en.wikipedia.org/wiki/OSI_model). Men i vissa situationer vill du eller behöver aktivera säkerhet på hög nivå i stacken. I sådana situationer rekommenderar vi att du distribuerar virtuella nätverks säkerhets enheter från Azure-partner.

Azure Network Security-enheter kan ge bättre säkerhet än vad kontroller på nätverks nivå ger. Nätverks säkerhetsfunktioner i virtuella nätverks säkerhets apparater är:


* Brand vägg
* Intrångs identifiering/skydd mot intrång
* Sårbarhetshantering
* Programregleringstyp
* Nätverks baserad avvikelse identifiering
* Webbfiltrering
* Antivirus
* Botnät skydd

Om du vill hitta tillgängliga Azure Virtual Network Security-enheter går du till [Azure Marketplace](https://azure.microsoft.com/marketplace/) och söker efter "säkerhet" och "nätverks säkerhet".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Distribuera perimeternätverk för säkerhets zoner
Ett [perimeternätverk](/azure/architecture/vdc/networking-virtual-datacenter) (kallas även DMZ) är ett fysiskt eller logiskt nätverks segment som ger ett extra säkerhets lager mellan dina till gångar och Internet. Specialiserade enheter för nätverks åtkomst kontroll på kanten av ett perimeternätverk tillåter endast önskad trafik till det virtuella nätverket.

Perimeternätverk är användbara eftersom du kan fokusera din hantering av nätverks åtkomst kontroll, övervakning, loggning och rapportering av enheterna vid gränsen för ditt virtuella Azure-nätverk. Ett perimeternätverk är där du normalt aktiverar DDoS-skydd (distributed denial of Service), intrångs identifiering/skydd mot intrång (ID/IP-adresser), brand Väggs regler och principer, webb filtrering, nätverks program mot skadlig kod och mycket annat. Nätverks säkerhetsenheterna hamnar mellan Internet och ditt virtuella Azure-nätverk och har ett gränssnitt i båda nätverken.

Även om detta är den grundläggande designen i ett perimeternätverk, finns det många olika design, till exempel back-to-back, Tri-homed och multi-homeed.

Baserat på begreppet noll förtroende som nämnts tidigare rekommenderar vi att du använder ett perimeternätverk för alla hög säkerhets distributioner för att förbättra nivån på nätverks säkerhet och åtkomst kontroll för dina Azure-resurser. Du kan använda Azure eller en lösning från tredje part för att tillhandahålla ett extra säkerhets lager mellan dina till gångar och Internet:

- Inbyggda Azure-kontroller. [Azure-brandväggen](../../firewall/overview.md) och [brand väggen för webbaserade program i Application Gateway](../../application-gateway/features.md#web-application-firewall) ger grundläggande säkerhet med en fullständigt tillstånds känslig brand vägg som en tjänst, inbyggd hög tillgänglighet, obegränsad moln skalbarhet, FQDN-filtrering, stöd för OWASP Core Rule Set och enkel konfiguration och konfiguration.
- Erbjudanden från tredje part. Sök på [Azure Marketplace](https://azuremarketplace.microsoft.com/) efter nästa generations brand vägg (NGFW) och andra tredjepartsprogram som tillhandahåller välbekanta säkerhets verktyg och avsevärt förbättrade nätverks säkerhets nivåer. Konfigurationen kan vara mer komplex, men ett erbjudande från tredje part kan göra att du kan använda befintliga funktioner och färdighetsuppsättningar.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Undvik att exponera Internet med dedikerade WAN-länkar
Många organisationer har valt hybrid IT-vägen. Med hybrid IT är en del av företagets informations till gångar i Azure, och andra förblir lokalt. I många fall körs vissa komponenter i en tjänst i Azure medan andra komponenter förblir lokalt.

I ett hybrid IT-scenario finns det vanligt vis en typ av anslutning mellan olika platser. Anslutning mellan platser gör att företaget kan ansluta sina lokala nätverk till virtuella Azure-nätverk. Det finns två lösningar för anslutning mellan platser:

* [Plats-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Det är en betrodd, tillförlitlig och etablerad teknik, men anslutningen äger rum via Internet. Bandbredden begränsas till högst cirka 1,25 Gbit/s. VPN för plats-till-plats är ett lämpligt alternativ i vissa scenarier.
* **Azure-ExpressRoute**. Vi rekommenderar att du använder [ExpressRoute](../../expressroute/expressroute-introduction.md) för din anslutning mellan platser. Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning som stöds av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts moln tjänster som Azure, Microsoft 365 och Dynamics 365. ExpressRoute är en särskild WAN-länk mellan din lokala plats eller en Microsoft Exchange-värd. Eftersom det här är en Telco-anslutning överförs inte dina data via Internet, så den är inte utsatt för potentiella risker med Internet-kommunikation.

Platsen för din ExpressRoute-anslutning kan påverka brand Väggs kapacitet, skalbarhet, tillförlitlighet och synlighet för nätverks trafik. Du måste identifiera var du ska avsluta ExpressRoute i befintliga (lokala) nätverk. Du kan:

- Avsluta utanför brand väggen (paradigm nätverkets paradigm) om du behöver insyn i trafiken, om du behöver fortsätta en befintlig metod för att isolera data Center, eller om du bara lägger till extra nät resurser på Azure.
- Avsluta i brand väggen (nätverks tilläggets paradigm). Detta är standard rekommendationen. I alla andra fall rekommenderar vi att du behandlar Azure som ett n:te Data Center.

## <a name="optimize-uptime-and-performance"></a>Optimera drift tid och prestanda
Om en tjänst är nere går det inte att komma åt informationen. Om prestanda är så dåligt att data är oanvändbara kan du överväga att data inte är tillgängliga. Från ett säkerhets perspektiv måste du göra vad du kan för att se till att dina tjänster har optimal drift tid och prestanda.

En populär och effektiv metod för att öka tillgängligheten och prestanda är belastnings utjämning. Belastnings utjämning är en metod för att distribuera nätverks trafik mellan servrar som ingår i en tjänst. Om du till exempel har frontend-webbservrar som en del av din tjänst kan du använda belastnings utjämning för att distribuera trafiken över flera klient webb servrar.

Den här distributionen av trafiken ökar tillgängligheten på grund av att en av webb servrarna blir otillgänglig, slutar belastningsutjämnaren att skicka trafik till den servern och dirigerar om den till de servrar som fortfarande är online. Belastnings utjämning ger också prestanda, eftersom processor, nätverk och minnes belastning för att betjäna begär Anden fördelas på alla belastningsutjämnade servrar.

Vi rekommenderar att du använder belastnings utjämning när du kan och efter behov för dina tjänster. Nedan visas scenarier på både Azures virtuella nätverks nivå och global nivå, tillsammans med alternativen för belastnings utjämning för var och en.

**Scenario**: du har ett program som:

- Kräver förfrågningar från samma användare/klientsession för att komma till samma backend-virtuella dator. Exempel på detta är shopping vagns appar och webb e-postservrar.
- Accepterar endast en säker anslutning, så okrypterad kommunikation till servern är inte ett acceptabelt alternativ.
- Kräver flera HTTP-begäranden på samma tids krävande TCP-anslutning som ska dirigeras eller belastnings bal anse ras till olika Server dels servrar.

**Belastnings Utjämnings alternativ**: Använd [Azure Application Gateway](../../application-gateway/overview.md), en belastnings utjämning för http-webbtrafik. Application Gateway stöder TLS-kryptering från slut punkt till slut punkt och [TLS-avslutning](../../application-gateway/overview.md) på gatewayen. Webb servrar kan sedan bli inbördat från kryptering och dekryptering och trafik som flödar okrypterade mot backend-servrarna.

**Scenario**: du måste belastningsutjämna inkommande anslutningar från Internet mellan dina servrar som finns i ett virtuellt Azure-nätverk. Scenarier är när du:

- Ha tillstånds lösa program som accepterar inkommande begär Anden från Internet.
- Kräv inte tröga sessioner eller TLS-avlastning. Tröga sessioner är en metod som används med belastnings utjämning i programmet för att uppnå Server tillhörighet.

**Belastnings Utjämnings alternativ**: Använd Azure Portal för att [skapa en extern belastningsutjämnare](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) som sprider inkommande begär anden över flera virtuella datorer för att ge en högre tillgänglighets nivå.

**Scenario**: du måste belastningsutjämna anslutningar från virtuella datorer som inte är på Internet. I de flesta fall initieras de anslutningar som godkänns för belastnings utjämning av enheter i ett virtuellt Azure-nätverk, till exempel SQL Server instanser eller interna webb servrar.   
**Belastnings Utjämnings alternativ**: Använd Azure Portal för att [skapa en intern belastningsutjämnare](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) som sprider inkommande begär anden över flera virtuella datorer för att ge en högre tillgänglighets nivå.

**Scenario**: du behöver global belastnings utjämning eftersom du:

- Ha en moln lösning som är allmänt fördelad i flera regioner och som kräver högsta möjliga drift tid (tillgänglighet).
- Du behöver högsta möjliga drift tid för att se till att tjänsten är tillgänglig även om ett helt data Center blir otillgängligt.

**Belastnings Utjämnings alternativ**: använd Azure Traffic Manager. Traffic Manager gör det möjligt att belastningsutjämna anslutningar till dina tjänster baserat på användarens plats.

Om användaren till exempel gör en begäran till din tjänst från EU dirigeras anslutningen till tjänsterna som finns i ett EU-datacenter. Den här delen av Traffic Manager global belastnings utjämning hjälper till att förbättra prestandan eftersom det går snabbare att ansluta till närmaste Data Center än att ansluta till data Center som är långt bort.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Inaktivera RDP/SSH-åtkomst till virtuella datorer
Det går att nå virtuella Azure-datorer med hjälp av [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) och SSH-protokollet ( [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) ). Dessa protokoll möjliggör hantering av virtuella datorer från fjärranslutna platser och är standard i datacenterberäkning.

De potentiella säkerhets problemen med att använda dessa protokoll via Internet är att angripare kan använda [Brute Force](https://en.wikipedia.org/wiki/Brute-force_attack) -teknik för att få åtkomst till virtuella Azure-datorer. När angripare får åtkomst kan de använda den virtuella datorn som en startpunkt för att kompromettera andra datorer i det virtuella nätverket eller till och med attackera nätverksanslutna enheter utanför Azure.

Vi rekommenderar att du inaktiverar direkt RDP och SSH-åtkomst till dina virtuella Azure-datorer från Internet. När direkt RDP och SSH-åtkomst från Internet har inaktiverats har du andra alternativ som du kan använda för att komma åt de virtuella datorerna för fjärrhantering.

**Scenario**: Aktivera en enskild användare för att ansluta till ett virtuellt Azure-nätverk via Internet.   
**Alternativ**: [punkt-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) är en annan term för en VPN-klient/server anslutning för fjärråtkomst. När punkt-till-plats-anslutningen har upprättats kan användaren använda RDP eller SSH för att ansluta till alla virtuella datorer som finns i det virtuella Azure-nätverket som användaren är ansluten till via punkt-till-plats-VPN. Detta förutsätter att användaren har behörighet att komma åt de virtuella datorerna.

Punkt-till-plats-VPN är säkrare än direkta RDP-eller SSH-anslutningar eftersom användaren måste autentisera sig två gånger innan den ansluter till en virtuell dator. Först måste användaren autentisera (och vara auktoriserad) för att upprätta punkt-till-plats-VPN-anslutningen. För det andra måste användaren autentisera (och vara auktoriserad) för att upprätta RDP-eller SSH-sessionen.

**Scenario**: Aktivera användare i ditt lokala nätverk för att ansluta till virtuella datorer i ditt virtuella Azure-nätverk.   
**Alternativ**: en [plats-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) ansluter ett helt nätverk till ett annat nätverk via Internet. Du kan använda en plats-till-plats-VPN för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk. Användare i det lokala nätverket ansluter med RDP-eller SSH-protokollet via VPN-anslutningen från plats till plats. Du behöver inte tillåta direkt RDP eller SSH-åtkomst via Internet.

**Scenario**: Använd en särskild WAN-länk för att tillhandahålla funktioner som liknar VPN för plats-till-plats.   
**Alternativ**: Använd [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Den innehåller funktioner som liknar plats-till-plats-VPN. De viktigaste skillnaderna är:

- Den dedikerade WAN-länken går inte igenom Internet.
- Dedikerade WAN-länkar är vanligt vis mer stabila och fungerar bättre.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Skydda dina kritiska Azure-tjänsteresurser enbart till dina virtuella nätverk
Använd Azures privata länk för att få åtkomst till Azure PaaS Services (till exempel Azure Storage och SQL Database) över en privat slut punkt i det virtuella nätverket. Med privata slut punkter kan du skydda dina kritiska Azure-tjänsteresurser enbart till dina virtuella nätverk. Trafik från ditt virtuella nätverk till Azure-tjänsten finns alltid kvar på Microsoft Azure stamnät nätverket. Att exponera det virtuella nätverket på det offentliga Internet är inte längre nödvändigt för att använda Azure PaaS-tjänster. 

Azure Private-länken ger följande fördelar:
- **Förbättrad säkerhet för dina Azure-tjänst resurser**: med en privat Azure-länk kan Azure Service-resurser skyddas till ditt virtuella nätverk med hjälp av privat slut punkt. Att skydda tjänst resurser på en privat slut punkt i ett virtuellt nätverk ger bättre säkerhet genom att helt ta bort offentlig Internet åtkomst till resurser och bara tillåta trafik från privat slut punkt i det virtuella nätverket.
- **Få privat åtkomst till Azure-tjänstens resurser på Azure-plattformen**: Anslut ditt virtuella nätverk till tjänster i Azure med hjälp av privata slut punkter. Det finns inget behov av en offentlig IP-adress. Den privata länk plattformen hanterar anslutningen mellan konsumenter och tjänster över Azures stamnät nätverk.
- **Åtkomst från lokala och peer-baserade nätverk**: åtkomst tjänster som körs i Azure från lokala datorer via privat ExpressRoute, VPN-tunnlar och peer-baserade virtuella nätverk med privata slut punkter. Du behöver inte konfigurera ExpressRoute Microsoft-peering eller gå igenom Internet för att komma åt tjänsten. Privat länk är ett säkert sätt att migrera arbets belastningar till Azure.
- **Skydd mot data läckage**: en privat slut punkt mappas till en instans av en PaaS-resurs i stället för hela tjänsten. Konsumenter kan bara ansluta till den angivna resursen. Åtkomst till någon annan resurs i tjänsten blockeras. Den här mekanismen ger skydd mot data läcker risker.
- **Global räckvidd**: Anslut privat till tjänster som körs i andra regioner. Användarens virtuella nätverk kan vara i region A och det kan ansluta till tjänster i region B.
- **Enkel att konfigurera och hantera**: du behöver inte längre reserverade, offentliga IP-adresser i dina virtuella nätverk för att skydda Azure-resurser via en IP-brandvägg. Det krävs inga NAT-eller gateway-enheter för att konfigurera de privata slut punkterna. Privata slut punkter konfigureras via ett enkelt arbets flöde. På tjänst sidan kan du också hantera anslutnings förfrågningarna på Azure-tjänstens resurs på ett enkelt sätt. Azures privata länk fungerar för konsumenter och tjänster som tillhör olika Azure Active Directory klienter. 
    
Mer information om privata slut punkter och de Azure-tjänster och regioner som privata slut punkter är tillgängliga för finns i [Azure privat länk](https://docs.microsoft.com/azure/private-link/private-link-overview).


## <a name="next-steps"></a>Nästa steg
Se [metod tips och mönster för Azure-säkerhet](best-practices-and-patterns.md) för att få bättre säkerhets metoder att använda när du utformar, distribuerar och hanterar dina moln lösningar med hjälp av Azure.
