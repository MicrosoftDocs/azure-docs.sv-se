---
title: Nätverks arkitektur för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Nätverks arkitektur för hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ed932488551918bb0bfeb7dc9ffcb2f59b6d152
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878909"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (stora instanser) nätverks arkitektur

Arkitekturen i Azure Network Services är en viktig del av distributionen av SAP-program på HANA stor instans. Vanligt vis har SAP HANA på Azure (stora instanser) distributioner ett större SAP-landskap med flera olika SAP-lösningar med varierande storlek på databaser, CPU-resursanvändning och minnes användning. Det är troligt att det inte redan finns några IT-system i Azure. Ditt SAP-landskap är ofta både hybrid och från en DBMS-punkt och SAP-programpunkt i vyn med en blandning av NetWeaver och S/4HANA och SAP HANA och andra DBMS. Azure erbjuder olika tjänster som gör att du kan köra olika DBMS-, NetWeaver-och S/4HANA-system i Azure. Azure erbjuder också nätverks teknik som gör att Azure ser ut som ett virtuellt Data Center till dina lokala program distributioner

Om inte dina kompletta IT-system finns i Azure. Funktionen Azure-nätverk används för att ansluta den lokala världen med dina Azure-tillgångar för att få Azure att se ut som ett virtuellt Data Center. De funktioner för Azure-nätverk som används är: 

- Virtuella Azure-nätverk är anslutna till [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -kretsen som ansluter till dina lokala nätverks resurser.
- En ExpressRoute-krets som ansluter lokalt till Azure bör ha en minsta bandbredd på [1 Gbit/s eller högre](https://azure.microsoft.com/pricing/details/expressroute/). Den här minimala bandbredden ger tillräcklig bandbredd för överföring av data mellan lokala system och system som körs på virtuella datorer. Det ger också tillräcklig bandbredd för anslutning till Azure-system från lokala användare.
- Alla SAP-system i Azure konfigureras i virtuella nätverk för att kommunicera med varandra.
- Active Directory och DNS-värd lokalt utökas till Azure via ExpressRoute lokalt, eller så körs de i Azure.

För det speciella fallet att integrera HANA-stora instanser i Azure Data Center Network Fabric används även Azure ExpressRoute Technology


> [!NOTE] 
> Endast en Azure-prenumeration kan bara länkas till en klient i en HANA-stor instans stämpel i en angiven Azure-region. Å andra sidan kan en enda HANA-processor med stora instanser bara kopplas till en Azure-prenumeration. Detta krav är konsekvent med andra fakturerbara objekt i Azure.

Om SAP HANA på Azure (stora instanser) har distribuerats i flera olika Azure-regioner distribueras en separat klient i den stora superinstansen i HANA. Du kan köra båda under samma Azure-prenumeration så länge dessa instanser är en del av samma SAP-landskap. 

> [!IMPORTANT] 
> Endast Azure Resource Manager distributions metoden stöds med SAP HANA på Azure (stora instanser).

 

## <a name="additional-virtual-network-information"></a>Ytterligare information om virtuellt nätverk

Om du vill ansluta ett virtuellt nätverk till ExpressRoute måste du skapa en Azure ExpressRoute-Gateway. Mer information finns i [om ExpressRoute-gatewayer för ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md). 

En Azure ExpressRoute-Gateway används med ExpressRoute till en infrastruktur utanför Azure eller till en stor instans av en Azure-instans. Du kan ansluta Azure ExpressRoute-gatewayen till maximalt fyra olika ExpressRoute-kretsar under förutsättning att anslutningarna kommer från olika Microsoft Enterprise Edge-routrar. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutning på Azure](hana-overview-infrastructure-connectivity.md). 

> [!NOTE] 
> Maximalt data flöde som du kan uppnå med en ExpressRoute-Gateway är 10 Gbit/s genom att använda en ExpressRoute-anslutning. Att kopiera filer mellan en virtuell dator som finns i ett virtuellt nätverk och ett lokalt system (som en enda kopierings ström) uppnår inte det fullständiga genomflödet av de olika Gateway-SKU: erna. Använd flera strömmar för att utnyttja den kompletta bandbredden för ExpressRoute-gatewayen. Eller så måste du kopiera olika filer i parallella strömmar av en enda fil.


## <a name="networking-architecture-for-hana-large-instance"></a>Nätverks arkitektur för HANA stor instans
Nätverks arkitekturen för en stor instans av HANA kan delas upp i fyra olika delar:

- Lokal nätverks-och ExpressRoute-anslutning till Azure. Den här delen är kundens domän och är ansluten till Azure via ExpressRoute. Den här ExpressRoute-kretsen betalas helt av dig som kund. Bandbredden bör vara tillräckligt stor för att hantera nätverks trafiken mellan dina lokala till gångar och den Azure-region som du ansluter till. Se det nedre högra hörnet i följande figur.
- Azure nätverks tjänster, som tidigare diskuterats, med virtuella nätverk, vilket kräver att ExpressRoute-gatewayer har lagts till. Den här delen är ett område där du behöver hitta lämpliga designer för dina program krav, säkerhet och krav på efterlevnad. Oavsett om du använder HANA stor instans är det en annan punkt att tänka på när antalet virtuella nätverk och Azure Gateway-SKU: er kan väljas. Se det övre högra hörnet i bilden.
- Anslutning av HANA stor instans genom ExpressRoute-teknik till Azure. Den här delen distribueras och hanteras av Microsoft. Allt du behöver göra är att ange vissa IP-adressintervall efter att du har distribuerat till gångar i HANA stor instans Anslut ExpressRoute-kretsen till de virtuella nätverken. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutning på Azure](hana-overview-infrastructure-connectivity.md). Det finns ingen extra avgift för dig som kund för anslutningen mellan Azure Data Centers nätverks infrastruktur resurser och HANA stora instans enheter.
- Nätverk inom den HANA-stora instans stämpeln som är mest transparent för dig.

![Virtuellt nätverk som är anslutet till SAP HANA på Azure (stora instanser) och lokalt](./media/hana-overview-architecture/image1-architecture.png)

Kravet på att dina lokala till gångar måste ansluta via ExpressRoute till Azure ändras inte eftersom du använder en stor instans av HANA. Kravet att ha ett eller flera virtuella nätverk som kör de virtuella datorerna, som är värd för det program skikt som ansluter till HANA-instanser som finns i HANA stora instans enheter, ändras inte heller. 

Skillnaderna med SAP-distributioner i Azure är:

- De HANA-stora instans enheterna av din kund klient är anslutna via en annan ExpressRoute-krets till dina virtuella nätverk. För att separera belastnings villkoren delar inte de lokala Azure-ExpressRoute kretsarna och kretsarna mellan virtuella Azure-nätverk och HANA stora instanser med samma routrar.
- Arbets belastnings profilen mellan SAP-programlagret och HANA stor-instansen är av en annan typ, med många små förfrågningar och burst som data överföringar (resultat uppsättningar) från SAP HANA till program lagret.
- SAP-programarkitekturen är mer känslig för nätverks fördröjningar än vanliga scenarier där data utbyts mellan lokala platser och Azure.
- Azure ExpressRoute-gatewayen har minst två ExpressRoute-anslutningar. En krets som är ansluten från en lokal plats och en som är ansluten från HANA-stora instanser. Detta lämnar bara utrymme för ytterligare två kretsar från olika msee för att ansluta till ExpressRoute-Gateway. Den här begränsningen är oberoende av användningen av ExpressRoute snabb väg. Alla anslutna kretsar delar den maximala bandbredden för inkommande data för ExpressRoute-gatewayen.

Med revision 3 av HANA stor instans-stämplar kan nätverks fördröjningen mellan virtuella datorer och HANA stora instans enheter vara högre än en typisk fördröjning för VM-till-VM-nätverk. Värdena som är beroende av Azure-regionen kan överstiga 0,7-MS tur och retur-svars tiden som klassificeras som under genomsnitt i [SAP obs #1100926 – vanliga frågor och svar: nätverks prestanda](https://launchpad.support.sap.com/#/notes/1100926/E). Beroende av Azure-region och-verktyg för att mäta fördröjningar i nätverks fördröjningar mellan en virtuell Azure-dator och HANA stor instans enhet, kan den uppmätta svars tiden vara upp till och omkring 2 millisekunder. Kunder distribuerar dock SAP HANA-baserade produktion SAP-program på SAP HANA stor instans. Se till att testa dina affärs processer noggrant i Azure HANA stor instans. En ny funktion, som kallas ExpressRoute snabb sökväg, kan minska nätverks fördröjningen mellan HANA-stora instanser och virtuella program på virtuella datorer i Azure i huvudsak (se nedan). 

Med revision 4 av HANA stor instans-stämplar, är nätverks fördröjningen mellan virtuella Azure-datorer som distribueras i närheten av den stora instans stämpeln i den stora instansen att uppfylla genomsnittet eller bättre än genomsnitts klassificeringen som dokumenteras i [SAP obs #1100926 – vanliga frågor och svar: nätverks prestanda](https://launchpad.support.sap.com/#/notes/1100926/E) om Azure ExpressRoute snabb sökväg har kon figurer ATS (se nedan). För att kunna distribuera virtuella Azure-datorer i nära närhet till HANA stora instans enheter av revision 4 måste du utnyttja [Azure närhets placerings grupper](../../co-location.md). Hur närhets placerings grupper kan användas för att hitta SAP-program skiktet i samma Azure-datacenter som den revision 4 värdbaserade HANA-enheten beskrivs i [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md).

För att tillhandahålla deterministisk nätverks fördröjning mellan virtuella datorer och HANA stor instans är valet av SKU för ExpressRoute-Gateway nödvändigt. Till skillnad från trafik mönster mellan lokala och virtuella datorer kan trafik mönstret mellan virtuella datorer och HANA stor instans utveckla små men höga burst-sändningar för begär Anden och data volymer som ska överföras. För att hantera sådana burst-fel rekommenderar vi starkt att du använder UltraPerformance Gateway-SKU: n. För typ II-klassen för HANA stor instans-SKU: er är det obligatoriskt att använda UltraPerformance Gateway-SKU: n som en ExpressRoute-Gateway.

> [!IMPORTANT] 
> Med tanke på den övergripande nätverks trafiken mellan SAP-program och databas lager, stöds endast HighPerformance-eller UltraPerformance Gateway-SKU: er för virtuella nätverk för anslutning till SAP HANA på Azure (stora instanser). För HANAs stora instans typ II SKU: er stöds endast UltraPerformance Gateway-SKU: n som en ExpressRoute-Gateway. Undantag gäller när du använder ExpressRoute snabb sökväg (se nedan)

### <a name="expressroute-fast-path"></a>ExpressRoute snabb sökväg
För att minska svars tiden har ExpressRoute snabb sökväg lanserats och lanserats i maj 2019 för den specifika anslutningen av HANA-stora instanser till virtuella Azure-nätverk som är värdar för de virtuella SAP-programmen. Den största skillnaden för lösningen som distribueras hittills, är att data flödar mellan virtuella datorer och HANA-stora instanser inte dirigeras genom ExpressRoute-gatewayen längre. I stället kommunicerar de virtuella datorerna i under näten i det virtuella Azure-nätverket direkt med den dedikerade Enterprise Edge-routern. 

> [!IMPORTANT] 
> ExpressRoute-funktionen för snabb sökväg kräver att de undernät som kör de virtuella SAP-programmen finns i samma virtuella Azure-nätverk som var anslutna till de stora HANA-instanserna. Virtuella datorer som finns i virtuella Azure-nätverk som är peer-kopplade med det virtuella Azure-nätverket som är anslutna direkt till de stora instanser i HANA-instansen är inte förmånen från ExpressRoute snabb väg. Som en typisk hubb och Ekers design för virtuella nätverk, där ExpressRoute-kretsarna ansluter mot ett virtuellt hubb nätverk och virtuella nätverk som innehåller SAP-program skiktet (ekrar), kommer inte snabb sökvägen för optimering av ExpressRoute att fungera. I utöver stöder ExpressRoute snabb sökväg inte användardefinierade regler för routning (UDR) idag. Mer information finns i [ExpressRoute virtuell nätverksgateway och FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md). 


Mer information om hur du konfigurerar ExpressRoute snabb sökväg, finns i dokumentet [Anslut ett virtuellt nätverk till Hana-stora instanser](./hana-connect-vnet-express-route.md).    

> [!NOTE]
> En UltraPerformance ExpressRoute-Gateway krävs för att ExpressRoute snabb väg ska fungera


## <a name="single-sap-system"></a>Enskilt SAP-system

Den lokala infrastruktur som visades tidigare är ansluten via ExpressRoute till Azure. ExpressRoute-kretsen ansluter till en Microsoft Enterprise Edge-router (MSEE: N). Mer information finns i [teknisk översikt över ExpressRoute](../../../expressroute/expressroute-introduction.md). När vägen har upprättats ansluter den till Azure-stamnätet.

> [!NOTE] 
> Om du vill köra SAP-landskap i Azure ansluter du till den företags gräns routern som är närmast Azure-regionen i SAP landskap. HANA-stora instanser är anslutna via dedikerade företagsspecifika router-enheter för att minimera nätverks fördröjningen mellan virtuella datorer i Azure IaaS och HANA-stora instans stämplar.

ExpressRoute-gatewayen för de virtuella datorer som är värdar för SAP-programinstanser är anslutna till en ExpressRoute-krets som ansluter till lokalt. Samma virtuella nätverk är anslutet till en separat Enterprise Edge-router som är avsedd att ansluta till stora instans stämplar. Med ExpressRoute snabb sökväg dirigeras data flödet från HANA-stora instanser till de virtuella SAP-program på virtuella datorer som inte är i ExpressRoute-gatewayen längre och med detta minskar svars tiden för nätverks fördröjning.

Det här systemet är ett enkelt exempel på ett enda SAP-system. SAP-programlagret finns i Azure. SAP HANA-databasen körs på SAP HANA på Azure (stora instanser). Antagandet är att bandbredden för ExpressRoute-gatewayen på 2 Gbit/s eller 10 Gbit/s inte är en Flask hals.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Flera SAP-system eller stora SAP-system

Om flera SAP-system eller stora SAP-system distribueras för att ansluta till SAP HANA på Azure (stora instanser) kan genomflödet i ExpressRoute-gatewayen bli en Flask hals. Eller om du vill isolera produktions-och icke-produktionssystem i olika virtuella Azure-nätverk. I sådana fall delar du upp program skikten i flera virtuella nätverk. Du kan också skapa ett särskilt virtuellt nätverk som ansluter till HANA stor instans för fall som:

- Säkerhetskopiera direkt från HANA-instanserna i HANA stor instans till en virtuell dator i Azure som är värd för NFS-resurser.
- Kopiera stora säkerhets kopior eller andra filer från HANA-stora instans enheter till disk utrymme som hanteras i Azure.

Använd ett separat virtuellt nätverk som värd för virtuella datorer som hanterar lagring för Mass överföring av data mellan HANA-stora instanser och Azure. I den här ordningen undviker du effekterna av stor fil-eller data överföring från HANA stor instans till Azure på ExpressRoute-gatewayen som hanterar de virtuella datorer som kör SAP-Programskiktet. 

För en mer skalbar nätverks arkitektur:

- Utnyttja flera virtuella nätverk för ett enda, större SAP-programnivå.
- Distribuera ett separat virtuellt nätverk för varje SAP-system som distribueras, jämfört med att kombinera dessa SAP-system i separata undernät under samma virtuella nätverk.

  En skalbar nätverks arkitektur för SAP HANA på Azure (stora instanser):

![Distribuera SAP-programlager över flera virtuella nätverk](./media/hana-overview-architecture/image4-networking-architecture.png)

Beroende av reglerna och begränsningarna som du vill tillämpa mellan de olika virtuella nätverk som är värdar för virtuella datorer i olika SAP-system, bör du peer-koppla dessa virtuella nätverk. Mer information om peering för virtuella nätverk finns i [peering för virtuella nätverk](../../../virtual-network/virtual-network-peering-overview.md).


## <a name="routing-in-azure"></a>Routning i Azure

Som standard är tre nätverks Dirigerings överväganden viktiga för SAP HANA på Azure (stora instanser):

* SAP HANA på Azure (stora instanser) kan endast nås via virtuella Azure-datorer och den dedikerade ExpressRoute-anslutningen, inte direkt från lokalt. Det är inte möjligt att direkt komma åt från lokala enheter till de stora instanser av HANA, som levereras av Microsoft. Begränsningarna för transitiv routning beror på den aktuella Azure-nätverks arkitekturen som används för SAP HANA stor instans. Vissa administrations klienter och program som behöver direkt åtkomst, t. ex. SAP Solution Manager som körs lokalt, kan inte ansluta till SAP HANA databasen. För undantag kontrollerar du avsnittet direkt routning till HANA-stora instanser.

* Om du har ett stort antal HANA-instanser som distribuerats i två olika Azure-regioner för haveri beredskap, har samma begränsningar för tillfälliga routning tillämpats tidigare. Med andra ord dirigerades inte IP-adresserna för en HANA stor instans enhet i en region (till exempel västra USA) till en stor instans enhet som har distribuerats i en annan region (till exempel USA, östra). Den här begränsningen var oberoende av användningen av Azures nätverks-peering mellan regioner eller mellan att koppla samman de ExpressRoute-kretsar som ansluter HANA-stora instans enheter till virtuella nätverk. För en grafisk representation, se bilden i avsnittet "använda HANA-stora instans enheter i flera regioner". Den här begränsningen, som medföljde den distribuerade arkitekturen, förhindrade omedelbar användning av HANA-systemreplikering som katastrof återställnings funktion. För de senaste ändringarna kan du leta upp avsnittet "använda HANA-stora instans enheter i flera regioner". 

* SAP HANA på Azure-enheter (stora instanser) har en tilldelad IP-adress från adress intervallet för serverns IP-adresspool som du skickade när du begärde den stora distribution av HANA-instanser. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutning på Azure](hana-overview-infrastructure-connectivity.md). Den här IP-adressen är tillgänglig via de Azure-prenumerationer och-kretsar som ansluter virtuella Azure-nätverk till HANA-stora instanser. Den IP-adress som har tilldelats från IP-adresspoolen för serverns IP-adress intervall tilldelas direkt till maskin varu enheten. Den är *inte* tilldelad till NAT längre, vilket var fallet i de första distributionerna av den här lösningen. 

### <a name="direct-routing-to-hana-large-instances"></a>Direkt routning till HANA-stora instanser

Den transitiva routningen fungerar som standard inte i följande scenarier:

* Mellan HANA-stora instans enheter och en lokal distribution.

* Mellan HANA-stor instans routning som distribueras i två olika regioner.

Det finns tre sätt att aktivera transitiv routning i dessa scenarier:

- En omvänd proxy för att dirigera data till och från. Till exempel F5 BIG-IP, NGINX med Traffic Manager distribuerat i det virtuella Azure-nätverket som ansluter till HANA-stora instanser och till lokal som en virtuell brand vägg/Traffic routing-lösning.
- Använda program varan iptables-regler i en virtuell Linux-dator för att aktivera routning mellan lokala platser och HANA stora instans enheter, eller mellan HANA-stora instans enheter i olika regioner. Den virtuella datorn som kör program varan iptables måste distribueras i det virtuella Azure-nätverket som ansluter till HANA-stora instanser och till lokalt. Den virtuella datorn måste ha en storlek enligt detta, så att nätverks data flödet på den virtuella datorn räcker för den förväntade nätverks trafiken. Mer information om bandbredd för virtuella datorer finns i artikel [storlekarna för virtuella Linux-datorer i Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure-brandväggen](https://azure.microsoft.com/services/azure-firewall/) skulle vara en annan lösning för att aktivera direkt trafik mellan lokala och Hana stora instans enheter. 

All trafik från dessa lösningar kommer att dirigeras via ett virtuellt Azure-nätverk och eftersom trafiken också kan begränsas av de mjuka enheter som används eller av Azures nätverks säkerhets grupper, så att vissa IP-adresser eller IP-adressintervall från lokala platser kan blockeras eller uttryckligen tillåtas åtkomst till HANA-stora instanser. 

> [!NOTE]  
> Tänk på att implementering och stöd för anpassade lösningar som inbegriper nätverks apparater från tredje part eller program varan iptables inte tillhandahålls av Microsoft. Support måste tillhandahållas av leverantören av komponenten som används eller integreraren. 

#### <a name="express-route-global-reach"></a>Express Route-Global Reach
Microsoft introducerade en ny funktion som kallas [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md). Global Reach kan användas för HANA-stora instanser i två scenarier:

- Aktivera direkt åtkomst från lokalt till dina HANA stora instanser som distribuerats i olika regioner
- Aktivera direkt kommunikation mellan dina HANA-stora instans enheter som distribuerats i olika regioner


##### <a name="direct-access-from-on-premises"></a>Direkt åtkomst från lokalt
I Azure-regionerna där Global Reach erbjuds kan du begära att Global Reach-funktionen för ExpressRoute-kretsen som ansluter ditt lokala nätverk till det virtuella Azure-nätverket som ansluter till dina HANA-stora instans enheter. Det finns vissa kostnads konsekvenser för den lokala sidan av din ExpressRoute-krets. För priser kontrollerar du priserna för [Global Reach-tillägg](https://azure.microsoft.com/pricing/details/expressroute/). Det finns inga ytterligare kostnader för dig som är relaterad till den krets som ansluter de stora HANA-instans enheterna till Azure. 

> [!IMPORTANT]  
> Om du använder Global Reach för att aktivera direkt åtkomst mellan dina HANA stora instans enheter och lokala till gångar, dirigeras inte nätverks data och kontroll flödet **via virtuella Azure-nätverk**, utan direkt mellan Microsoft Enterprise Exchange-routrarna. Som ett resultat av NSG-eller grupperna-regler, eller någon typ av brand vägg, NVA eller proxy som du har distribuerat i ett virtuellt Azure-nätverk, kommer inte att bli touch. **Om du använder ExpressRoute Global Reach för att aktivera direkt åtkomst från lokala platser till HANA-begränsningar och behörigheter för att komma åt HANA stora instans enheter måste definieras i brand väggar på den lokala sidan** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Ansluta HANA-stora instanser i olika Azure-regioner
På samma sätt kan du, eftersom ExpressRoute Global Reach kan användas för att ansluta lokalt till HANA stora instans enheter, det kan användas för att ansluta två HANA-instanser som distribueras åt dig i två olika regioner. Isoleringen är de ExpressRoute-kretsar som dina HANA-instanser i stor instans använder för att ansluta till Azure i båda regionerna. Det finns inga ytterligare avgifter för att ansluta två HANA-instanser med stora instanser som distribueras i två olika regioner. 

> [!IMPORTANT]  
> Data flödet och kontroll flödet för nätverks trafiken mellan olika HANA-instanser för stora instanser skickas inte via Azure-nätverk. Det innebär att du inte kan använda Azure-funktioner eller NVA för att genomdriva kommunikations begränsningar mellan dina två HANA stora instanser-klienter. 

Mer information om hur du hämtar ExpressRoute Global Reach aktiverat finns i dokumentet [Anslut ett virtuellt nätverk till Hana-stora instanser](./hana-connect-vnet-express-route.md).


## <a name="internet-connectivity-of-hana-large-instance"></a>Internet anslutning för HANA stor instans
HANA-stor instans har *ingen* direkt Internet anslutning. Den här begränsningen kan till exempel begränsa din möjlighet att registrera operativ system avbildningen direkt med operativ system leverantören. Du kan behöva arbeta med din lokala SUSE Linux Enterprise Server prenumerations hanterings verktygs Server eller Red Hat Enterprise Linux prenumerations hanteraren.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Data kryptering mellan virtuella datorer och HANA stor instans
Data som överförs mellan HANA stor instans och virtuella datorer är inte krypterade. Men endast för Exchange mellan HANA-DBMS sida och JDBC/ODBC-baserade program, kan du aktivera kryptering av trafik. Mer information finns i [den här dokumentationen av SAP](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Använd HANA-stora instans enheter i flera regioner

Om du vill använda UPS för haveri beredskap måste du ha SHANA stora instans enheter i flera Azure-regioner. Även om du använder Azure [global VNet-peering], fungerar inte den transitiva routning som standard mellan HANA-instanser med stora instanser i två olika regioner. Global Reach öppnar dock kommunikations vägen mellan de HANA-stora instans enheter som du har etablerad i två olika regioner. Det här användnings scenariot för ExpressRoute Global Reach möjliggör:

 - HANA-systemreplikering utan ytterligare proxyservrar eller brand väggar
 - Kopiera säkerhets kopior mellan HANA-stora instans enheter i två olika regioner för att utföra system kopior eller systemuppdateringar


![Virtuellt nätverk som är anslutet till Azures stora instanser i olika Azure-regioner](./media/hana-overview-architecture/image8-multiple-regions.png)

Bilden visar hur de olika virtuella nätverken i båda regionerna är anslutna till två olika ExpressRoute-kretsar som används för att ansluta till SAP HANA på Azure (stora instanser) i båda Azure-regionerna (grå linjer). Anledningen till att dessa två kors anslutningar är att skydda mot ett avbrott i msee på någon av sidorna. Kommunikations flödet mellan de två virtuella nätverken i de två Azure-regionerna ska hanteras över [Global peering](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) av de två virtuella nätverken i de två olika regionerna (blå streckad linje). Den tjocka röda linjen beskriver ExpressRoute Global Reach-anslutningen, som gör att de många HANA-instanser av dina klienter i två olika regioner kan kommunicera med varandra. 

> [!IMPORTANT] 
> Om du har använt flera ExpressRoute-kretsar, som sökväg i väntan och lokala inställningar för BGP-inställningar, ska du använda för att säkerställa korrekt trafik trafik.

**Nästa steg**
- Referera [SAP HANA (stora instanser) lagrings arkitektur](hana-storage-architecture.md)