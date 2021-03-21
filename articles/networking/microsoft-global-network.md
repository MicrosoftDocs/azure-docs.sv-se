---
title: Microsoft globalt nätverk – Azure
description: Lär dig hur Microsoft bygger och arbetar med ett av de största stamnät nätverken i världen och varför det är centralt för att leverera en bra moln upplevelse.
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/05/2020
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: a33c7e7d637b519b249c250481a73d04c661d4d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97915852"
---
# <a name="microsoft-global-network"></a>Microsofts globala nätverk

Microsoft äger och arbetar med ett av de största stamnät nätverken i världen. Den här globala och sofistikerade arkitekturen, som sträcker sig över 165 000 mil, ansluter våra data Center och kunder. 
 
Varje dag ansluter kunderna över hela världen till att Microsoft Azure, Bing, Dynamics 365, Microsoft 365, XBox och många andra. Oavsett typ förväntar kunderna omedelbar tillförlitlighet och svars tider från våra tjänster. 
 
[Microsoft Global Network](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) är en central del av att leverera en bra moln upplevelse. Att ansluta våra Microsoft- [datacenter](https://azure.microsoft.com/global-infrastructure/) över 61 Azure-regioner och stora nät av Edge-noder som har placerats strategiskt runt i världen, erbjuder vårt globala nätverk både tillgänglighet, kapacitet och flexibilitet för att uppfylla alla behov.

![Microsofts globala nätverk](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Hämta Premium Cloud-nätverket
 
Väljer för [bästa möjliga upplevelse](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) är enkel när du använder Microsoft Cloud. Från den tidpunkt då kund trafiken går in i vårt globala nätverk genom våra strategiska placerade Edge-noder, färdas dina data genom optimerade vägar nära ljus hastigheten. Detta säkerställer optimal fördröjning för bästa prestanda. Dessa Edge-noder, som är sammankopplade till fler än 4000 unika Internet partner (peer-datorer) via tusentals anslutningar på fler än 175 platser, ger grunden för vår sammanlänknings strategi. 
 
Oavsett om du ansluter från London till Tokyo eller från Washington DC till Los Angeles, kvantifieras nätverks prestanda och påverkas av saker som svars tid, Darr, paket förlust och data flöde.  På Microsoft föredrar vi och använder direkt sammankopplade anslutningar i stället för överförings länkar, och det håller svars trafiken symmetrisk och hjälper till att hålla hopp, peering-parter och sökvägar så kort och enkelt som möjligt. 

Om en användare i London försöker få åtkomst till en tjänst i Tokyo, kommer Internet trafiken till en av våra gränser i London, gå över Microsoft WAN via Frankrike, våra Trans-Arabia sökvägar mellan Europa och Indien, och sedan till Japan där tjänsten finns. Svars trafiken är symmetrisk. Detta kallas ibland för [routning av kall potatis](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) , vilket innebär att trafiken stannar på Microsoft-nätverket så länge som möjligt innan vi kan göra det.  
  
Det innebär alltså all trafik när du använder Microsoft-tjänster? Ja, all trafik mellan data Center, inom Microsoft Azure eller mellan Microsoft-tjänster som Virtual Machines, Microsoft 365, XBox, SQL-databaser, lagring och virtuella nätverk dirigeras i vårt globala nätverk och aldrig över det offentliga Internet, för att säkerställa optimala prestanda och integritet.  
 
Stora investeringar i fiber kapacitet och varierande variation mellan vägar i Metro, land och undervattens är avgörande för att vi ska kunna upprätthålla konsekvent och hög service nivå samtidigt som den yttersta tillväxten av vårt moln och onlinetjänster. De senaste tilläggen till vårt globala nätverk är vår [Marea](https://www.submarinecablemap.com/#/submarine-cable/marea) undervattens-kabel, branschens första öppna rad system (OLS) över Subsea, mellan Bilbao, Spanien och Virginia strand, Virginia, USA, samt [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) mellan New York, USA och Dublin, Irland och [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) mellan Tokyo, Japan och Göteborg, USA, USA. 
 

## <a name="our-network-is-your-network"></a>Vårt nätverk är ditt nätverk

Vi har gjort två årtionden, tillsammans med enorma investeringar i nätverket, för att säkerställa optimala prestanda hela tiden. Företag kan dra full nytta av våra nätverks till gångar och bygga avancerade överläggs arkitekturer överst. 
 
Microsoft Azure erbjuder den övergripande portföljen av tjänster och funktioner, så att kunder snabbt och enkelt kan bygga, expandera och uppfylla nätverks krav var som helst. Vår serie anslutnings tjänster sträcker sig över virtuella nätverks peering mellan regioner, hybrid-och molnbaserade punkt-till-plats-och plats-till-plats-arkitekturer samt globala IP-överförings scenarier.  För företag som vill ansluta sina egna data Center eller nätverk till Azure, eller kunder med enorma data inmatnings-eller överförings behov, [ExpressRoute](../expressroute/expressroute-introduction.md)och [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) ger alternativ upp till 100 Gbit/s bandbredd, direkt till Microsofts globala nätverk på peering-platser över hela världen.  
 
[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) har utformats för att komplettera din tjänst leverantörs WAN-implementering och ansluta dina lokala platser över hela världen. Om du kör en global åtgärd kan du använda ExpressRoute Global Reach tillsammans med dina önskade och lokala tjänst leverantörer för att ansluta alla dina globala webbplatser med Microsofts globala nätverk. Det nya nätverket i molnet (WAN) för att omfatta ett stort antal förgreningar kan utföras via Azure Virtual WAN, som ger möjlighet att smidigt ansluta dina grenar till Microsofts globala nätverk med SDWAN & VPN-enheter (det vill säga kund lokal utrustning eller CPE) med inbyggd enkel användning och automatiserad anslutning och konfigurations hantering. 
 
[Global VNet-peering](../virtual-network/virtual-network-peering-overview.md) gör det möjligt för kunder att ansluta två eller flera virtuella Azure-nätverk över flera regioner. När de virtuella nätverken har peer-kopplats visas de som ett. Trafiken mellan virtuella datorer i peer-baserade virtuella nätverk dirigeras via Microsoft stamnät-infrastrukturen, ungefär som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk via enbart privata IP-adresser. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Väl hanterad med hjälp av programdefinierad innovation

Genom att köra ett av de ledande molnen i världen har Microsoft fått stor insikt och erfarenhet av att skapa och hantera global infrastruktur med hög prestanda.  
 
Vi följer en robust uppsättning operativa principer: 
 
- Använd den bästa växlings maskin varan på olika nivåer i nätverket.  
- Distribuera nya funktioner utan påverkan på slutanvändare.  
- Distribuera uppdateringar på ett säkert och tillförlitligt sätt över flottan, så snabbt som möjligt. Timmar istället för veckor.  
- Använd djup telemetri i moln skala och helt automatiserad fel minskning.  
- Använd enhetlig och programvarubaserad nätverks teknik för att kontrol lera alla maskin varu element i nätverket.  Eliminerar duplicering och minskar felen. 
 
Dessa principer gäller för alla skikt i nätverket: från värd nätverks gränssnittet, byter plattform, nätverkets funktioner i data centret, till exempel belastningsutjämnare, allt upp till WAN med vår teknik teknik plattform och våra optiska nätverk.  
 
Den exponentiella tillväxten i Azure och dess nätverk har nått en punkt där vi slutligen skulle ha uppnått att mänsklig intuition inte längre kan förlita sig på att hantera globala nätverks åtgärder. För att uppfylla behovet av att verifiera långsiktiga ändringar i nätverket har vi utvecklat en plattform för spegling och emulering av vårt produktions nätverk syntetiskt. Möjligheten att skapa speglade miljöer och köra miljon tals simuleringar, gör att vi kan testa program-och maskin varu ändringar och deras påverkan innan de allokeras till vår produktions plattform och nätverk. 

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer om hur Microsoft driver global nätverks tillförlitlighet via intelligent program vara](https://azure.microsoft.com/blog/advancing-global-network-reliability-through-intelligent-software-part-1-of-2/)
- [Läs mer om de nätverks tjänster som tillhandahålls i Azure](https://azure.microsoft.com/product-categories/networking/)