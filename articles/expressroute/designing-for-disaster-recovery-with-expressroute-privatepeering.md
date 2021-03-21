---
title: 'Azure-ExpressRoute: design för katastrof återställning'
description: Den här sidan innehåller arkitektur rekommendationer för haveri beredskap när du använder Azure-ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: duau
ms.openlocfilehash: 2a5730cd75ccb76d25897e9109555113f7355c2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202421"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Design för haveri beredskap med ExpressRoute privat peering

ExpressRoute har utformats för att ge hög tillgänglighet för att tillhandahålla bärvåg för privat nätverks anslutning till Microsoft-resurser. Det finns med andra ord ingen enskild felpunkt i ExpressRoute-sökvägen i Microsoft-nätverket. Design överväganden för att maximera tillgängligheten för en ExpressRoute-krets finns i [utforma för hög tillgänglighet med ExpressRoute][HA].

Men om du tar Murphy populärt Adage –*om något går fel, kommer det att* övervägas, i den här artikeln kan vi fokusera på lösningar som går utöver fel som kan åtgärdas med hjälp av en enda ExpressRoute-krets. I den här artikeln kan vi med andra ord titta på nätverks arkitektur för att skapa robusta Server dels nätverks anslutningar för haveri beredskap med geo-redundanta ExpressRoute-kretsar.

>[!NOTE]
>Begreppen som beskrivs i den här artikeln gäller även när en ExpressRoute-krets skapas under Virtual WAN eller utanför den.
>

## <a name="need-for-redundant-connectivity-solution"></a>Behov av redundant anslutnings lösning

Det finns möjligheter och instanser där en hel regional tjänst (var den Microsoft, nätverks tjänst leverantörer, kund eller andra moln tjänst leverantörer) försämras. Rotor saken till sådan regional omfattande tjänst påverkan är naturlig Calamity. Därför är det viktigt att planera för haveri beredskap för affärs kontinuitet och verksamhets kritiska program.   

Oavsett om du kör dina verksamhets kritiska program i en Azure-region eller lokalt eller var du än befinner dig, kan du använda en annan Azure-region som redundans plats. Följande artiklar behandlar haveri beredskap från program och åtkomst till klient åtkomst perspektiv:

- [Haveriberedskap i företagsskala][Enterprise DR]
- [SMB-haveriberedskap med Azure Site Recovery][SMB DR]

Om du förlitar dig på ExpressRoute-anslutning mellan ditt lokala nätverk och Microsoft för verksamhets kritiska åtgärder bör din katastrof återställnings plan även omfatta Geo-redundant nätverks anslutning. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Utmaningar med att använda flera ExpressRoute-kretsar

När du samman samma uppsättning nätverk med mer än en anslutning, introduceras parallella sökvägar mellan nätverken. Parallella sökvägar, om de inte är korrekt konstruerade, kan leda till asymmetrisk routning. Om du har tillstånds känsliga entiteter (till exempel NAT, brand vägg) i sökvägen kan asymmetrisk routning blockera trafikflöde.  Vanligt vis kommer du inte över tillstånds känsliga entiteter, till exempel NAT eller brand väggar, via ExpressRoute privata peering-sökvägen. Därför blockerar asymmetrisk routning via ExpressRoute privat peering inte nödvändigt vis trafikflödet.
 
Men om du belastningsutjämna trafik över geo-redundanta parallella sökvägar, oavsett om du har tillstånds känsliga entiteter eller inte, så kommer du att uppleva inkonsekvent nätverks prestanda. I den här artikeln diskuterar vi hur du kan åtgärda de här utmaningarna.

## <a name="small-to-medium-on-premises-network-considerations"></a>Överväganden för små till medel stora nätverks platser

Nu ska vi titta på exempel nätverket som illustreras i följande diagram. I exemplet upprättas Geo-redundant ExpressRoute-anslutning mellan en lokal plats i Contoso och Contosos VNet i en Azure-region. I diagrammet indikerar heldragen grön linje önskad sökväg (via ExpressRoute 1) och den prickade en representerar en behållen väg (via ExpressRoute 2).

[![81.1]][1]

När du utformar ExpressRoute-anslutningen för haveri beredskap måste du tänka på följande:

- använda geo-redundanta ExpressRoute-kretsar
- använda olika tjänst leverantörs nätverk för olika ExpressRoute-kretsar
- utforma var och en av ExpressRoute-kretsen för [hög tillgänglighet][HA]
- avsluta olika ExpressRoute-kretsar på olika platser i kundens nätverk

Om du annonserar vägar identiskt över alla ExpressRoute-sökvägar kommer Azure att belastningsutjämna en lokal bindnings trafik över alla ExpressRoute sökvägar med hjälp av ECMP-routning (lika-kostnad multi-path).

Men med de geo-redundanta ExpressRoute-kretsarna behöver vi ta hänsyn till olika nätverks prestanda med olika nätverks Sök vägar (särskilt för nätverks fördröjning). Om du vill få mer konsekvent nätverks prestanda under normal drift kan du vilja föredra den ExpressRoute-krets som har den minsta svars tiden.

Du kan påverka Azure för att föredra en ExpressRoute-krets över en annan med hjälp av någon av följande metoder (anges i effektivitets ordning):

- Annonsera mer speciell väg över den önskade ExpressRoute-kretsen jämfört med andra ExpressRoute-kretsar
- Konfigurera högre anslutnings vikt för anslutningen som länkar det virtuella nätverket till önskad ExpressRoute-krets
- Annonsera vägarna över mindre önskad ExpressRoute-krets med längre sökväg (som sökväg lägga)

### <a name="more-specific-route"></a>Mer speciell väg

Följande diagram illustrerar hur du påverkar ExpressRoute sökvägar med hjälp av mer speciell väg annonsering. I det här exemplet visas Contosos lokalt/24 IP-intervall som två/25 adress intervall via önskad sökväg (ExpressRoute 1) och som/24 via sökvägen (ExpressRoute 2).

[![11.2]][2]

Eftersom/25 är mer särskilt jämfört med/24 skulle Azure skicka trafiken till 10.1.11.0/24 via ExpressRoute 1 i normalt tillstånd. Om båda anslutningarna för ExpressRoute 1 går ned skulle VNet se 10.1.11.0/24-väg annonsen endast via ExpressRoute 2; och därför används standby-kretsen i det här felet.

### <a name="connection-weight"></a>Anslutnings vikt

Följande skärm bild visar hur du konfigurerar en ExpressRoute-anslutnings vikt via Azure Portal.

[![3]][3]

Följande diagram illustrerar hur du påverkar ExpressRoute Sök vägs val med anslutnings vikt. Standard anslutningens vikt är 0. I exemplet nedan konfigureras vikten för anslutningen för ExpressRoute 1 som 100. När ett VNet får ett väg-prefix som annonseras via fler än en ExpressRoute-krets föredrar det virtuella nätverket anslutningen med högsta vikt.

[![4]][4]

Om båda anslutningarna för ExpressRoute 1 går ned skulle VNet se 10.1.11.0/24-väg annonsen endast via ExpressRoute 2; och därför används standby-kretsen i det här felet.

### <a name="as-path-prepend"></a>SOM sökväg lägga

Följande diagram illustrerar hur du påverkar ExpressRoute Sök vägs val med hjälp av sökvägen lägga. I diagrammet anger väg annonsen över ExpressRoute 1 standard beteendet för eBGP. I väg annonsen över ExpressRoute 2 är det lokala nätverkets ASN anpassningsprefix också på vägens AS-sökväg. När samma väg tas emot via flera ExpressRoute-kretsar, skulle VNet prioritera vägen med den kortaste AS-sökvägen per eBGP. 

[![5]][5]

Om båda anslutningarna för ExpressRoute 1 går ned skulle VNet se 10.1.11.0/24-väg annonsen endast via ExpressRoute 2. I tur och ordning skulle den längre som sökvägen bli irrelevant. Därför används standby-kretsen i det här felet.

Om du använder någon av teknikerna, om du påverkar Azure för att föredra en av dina ExpressRoute över andra, måste du också se till att det lokala nätverket också föredrar samma ExpressRoute-sökväg för Azure-bindningen för att undvika asymmetriska flöden. Inställningen för lokalt värde används vanligt vis för att påverka det lokala nätverket för att föredra en ExpressRoute-krets över andra. Lokal preferens är ett internt BGP (iBGP) mått. BGP-vägen med det högsta lokala preferens värdet är föredra.

> [!IMPORTANT]
> När du använder vissa ExpressRoute-kretsar som fristående måste du hantera dem aktivt och regelbundet testa redundansväxlingen. 
> 

## <a name="large-distributed-enterprise-network"></a>Stort distribuerat företags nätverk

När du har ett stort distribuerat företags nätverk har du förmodligen flera ExpressRoute-kretsar. I det här avsnittet får du se hur du kan designa haveri beredskap med aktiva ExpressRoute-kretsar, utan att behöva ytterligare driv kretsar. 

Vi ska titta på exemplet som illustreras i följande diagram. I exemplet har contoso två lokala platser som är anslutna till två contoso IaaS-distribution i två olika Azure-regioner via ExpressRoute-kretsar på två olika peering-platser. 

[![3-6]][6]

Hur vi skapar haveri beredskap har en inverkan på hur trafiken mellan regionala och globala platser (Region1/Region2 till location2/location1) dirigeras. Vi tänker på två olika katastrof arkitekturer som dirigerar trafik mellan olika regioner.

### <a name="scenario-1"></a>Scenario 1

I det första scenariot kan vi utforma haveri beredskap, så att all trafik mellan en Azure-region och ett lokalt nätverks flöde via den lokala ExpressRoute-kretsen i stabilt tillstånd. Om den lokala ExpressRoute-kretsen Miss lyckas används fjärran sluten ExpressRoute-kretsen för alla trafikflöden mellan Azure och det lokala nätverket.

Scenario 1 illustreras i följande diagram. I diagrammet indikerar gröna linjer sökvägar för trafikflöde mellan VNet1 och lokala nätverk. De blå linjerna indikerar vägar för trafikflöde mellan VNet2 och lokala nätverk. Heldragna linjer anger önskad sökväg i stabilt läge och de streckade linjerna indikerar trafik Sök vägen vid en ExpressRoute-krets som har ett stabilt tillstånds trafikflöde. 

[![3,7]][7]

Du kan skapa scenariot med anslutnings vikt för att påverka virtuella nätverk för att föredra anslutning till den lokala peering-platsen ExpressRoute för den lokala nätverks bindnings trafiken. För att slutföra lösningen måste du se till att symmetriskt flöde för trafikflödet återställs. Du kan använda lokal inställning på iBGP-sessionen mellan BGP-routrarna (där ExpressRoute-kretsar avslutas på den lokala sidan) för att föredra en ExpressRoute-krets. Lösningen illustreras i följande diagram. 

[![8]][8]

### <a name="scenario-2"></a>Scenario 2

Scenario 2 illustreras i följande diagram. I diagrammet indikerar gröna linjer sökvägar för trafikflöde mellan VNet1 och lokala nätverk. De blå linjerna indikerar vägar för trafikflöde mellan VNet2 och lokala nätverk. I stabilt läge (heldragna linjer i diagrammet) flödar all trafik mellan virtuella nätverk och lokala platser via Microsoft stamnät för den mesta delen, och flödar genom samtrafik mellan lokala platser endast i felläget (prickade linjer i diagrammet) för en ExpressRoute.

[![1.9]][9]

Lösningen illustreras i följande diagram. Som det illustreras kan du skapa scenariot med hjälp av en mer speciell väg (alternativ 1) eller som lägga (alternativ 2) för att påverka val av VNet-sökväg. Om du vill påverka det lokala nätverks vägs valet för Azure-Bound, måste du konfigurera sammanlänkningen mellan den lokala platsen så att den blir mindre prioriterad. Howe du konfigurerar länken mellan anslutning som helst beror på routningsprotokollet som används i det lokala nätverket. Du kan använda lokal inställning med iBGP eller Metric med IGP (OSPF eller är-är).

[![10]][10]


## <a name="next-steps"></a>Nästa steg

I den här artikeln diskuterade vi hur du utformar för haveri beredskap för en privat peering-anslutning i ExpressRoute-kretsen. Följande artiklar behandlar haveri beredskap från program och åtkomst till klient åtkomst perspektiv:

- [Haveriberedskap i företagsskala][Enterprise DR]
- [SMB-haveriberedskap med Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "små till medel stora nätverks överväganden vid lokala nätverk"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.pngatt "påverka Sök vägs valet med mer exakta vägar"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Konfigurera anslutnings vikt via Azure Portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "påverka Sök vägs val med anslutnings vikt"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "påverka Sök VÄGS valet med som sökväg lägga"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "stora nätverks överväganden vid nätverks distribution"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "Scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "aktiva-aktiva ExpressRoute-kretsar lösning 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "Scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "aktiva-aktiva ExpressRoute kretsar lösning 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending