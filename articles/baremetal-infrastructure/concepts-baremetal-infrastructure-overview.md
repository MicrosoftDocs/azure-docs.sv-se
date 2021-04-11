---
title: Översikt över BareMetal-infrastrukturen i Azure
description: Innehåller en översikt över BareMetal-infrastrukturen i Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/08/2021
ms.openlocfilehash: 7a4998a096a5c5d9e793c34d5046dce59262a2ae
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257576"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Vad är BareMetal-infrastruktur på Azure?

Microsoft Azure erbjuder en moln infrastruktur med en mängd olika integrerade moln tjänster som uppfyller dina affärs behov. I vissa fall kan du behöva köra tjänster på Bare Metal-servrar utan ett Virtualization-lager. Du kanske behöver rot åtkomst och kontroll över operativ systemet (OS). För att uppfylla ett sådant behov erbjuder Azure BareMetal-infrastruktur för flera högt värde-och verksamhets kritiska program.

BareMetal-infrastrukturen består av dedikerade BareMetal-instanser (beräknings instanser), högpresterande och programlämplig lagring (NFS, ISCSI och fiber kanal) samt en uppsättning funktions specifika virtuella LAN (VLAN) i en isolerad miljö. Lagring kan delas mellan BareMetal-instanser för att aktivera funktioner som skalbara kluster eller för att skapa hög tillgänglighets par med STONITH.
 
Den här miljön har också särskilda virtuella lokala nätverk som du kan komma åt om du kör virtuella datorer i ett eller flera virtuella Azure-nätverk (virtuella nätverk) i din Azure-prenumeration. Hela miljön visas som en resurs grupp i din Azure-prenumeration.

BareMetal-infrastrukturen erbjuds i över 30 SKU: er från 2-till 24-socket-servrar och minne mellan 1,5 TB upp till 24 TBs. En stor uppsättning SKU: er är också tillgängliga med Octane-minne. Azure erbjuder det största intervallet av Bare Metal-instanser i ett storskaligt moln.

## <a name="why-baremetal-infrastructure"></a>Varför BareMetal-infrastruktur?  

Vissa centrala arbets belastningar i företaget består av tekniker som bara inte är utformade för att köras i en typisk virtualiserad moln inställning. De kräver särskild arkitektur, certifierad maskin vara eller ovanligt stora storlekar. Även om dessa tekniker har de mest avancerade funktionerna för data skydd och affärs kontinuitet, är dessa funktioner inte utformade för det virtualiserade molnet. De är mer känsliga för fördröjningar, störande störningar och kräver mycket mer kontroll över aktiviteter för ändrings hantering och underhåll.

BareMetal-infrastrukturen skapas, certifieras och testas för en urvals uppsättning av sådana program. Azure var det första att erbjuda sådana lösningar och har sedan lett till den största portföljen och de mest sofistikerade systemen.

BareMetal-infrastrukturen erbjuder följande förmåner: 

- Dedikerade instanser
- Certifierad maskin vara för specialiserade arbets belastningar
    - SAP (se [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (se [Oracle-dokument-ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Bare Metal (ingen beräknings virtualisering)
- Låg latens mellan virtuella Azure-baserade program i BareMetal-instanser (0,35 MS)
- Alla Flash SSD och NVMe
    - Upp till 1 PB/klient 
    - IOPS upp till 1,2 miljoner/klient organisation 
    - 40/100 GB nätverks bandbredd
    - Tillgängligt via NFS, ISCSI och FC
- Redundant strömförsörjning, strömförsörjning, NIC-enheter, TORs, portar, WAN, lagring och hantering
- Snabb ersättning för ersättning vid ett haveri (utan behov av omkonfigurering)
- Kund koordinerade underhålls fönster
- Program medveten ögonblicks bilder, arkivering, spegling och kloning


## <a name="sku-availability-in-azure-regions"></a>SKU-tillgänglighet i Azure-regioner

BareMetal-infrastrukturen erbjuder flera SKU: er certifierade för specialiserade arbets belastningar. Använd de arbets belastnings lager enheter som krävs för att uppfylla dina behov.

- Stora instanser – sträcker sig från två socklar till system med fyra socklar.  
- Mycket stora instanser – sträcker sig från fyr till 20-socket-system. 

BareMetal-infrastruktur för specialiserade arbets belastningar finns i följande Azure-regioner:
- Europa, västra
- Europa, norra
- Stöd för Tyskland, västra centrala * zoner
- USA, östra 2 * stöd för zoner
- USA, östra * zoner, stöd
- Västra USA * zoner stöd
- Västra USA 2 * zoner stöd
- USA, södra centrala

>[!NOTE]
>**Stöd för zoner** syftar på tillgänglighets zoner inom en region där BareMetal-instanser kan distribueras mellan zoner för hög återhämtning och tillgänglighet. Den här funktionen möjliggör stöd för aktiv-aktiv skalning med flera platser.

## <a name="managing-baremetal-instances-in-azure"></a>Hantera BareMetal-instanser i Azure 

Programtopologierna för BareMetal-infrastrukturen kan vara komplexa, beroende på dina behov. Du kan distribuera flera instanser, på en eller flera platser, med delad eller dedikerad lagring och specialiserade LAN-och WAN-anslutningar. För BareMetal-infrastruktur erbjuder Azure en rådgivnings insamling av informationen genom en CSA/GBB i fältet i en etablerings Portal. 

Efter den tid som din BareMetal-infrastruktur är etablerad är operativ systemet, nätverken, lagrings volymerna, placeringarna i zoner och regioner, och WAN-anslutningar mellan platser redan förkonfigurerade. Du är inställd på att registrera dina OS-licenser (BYOL), konfigurera operativ systemet och installera program skiktet.

Du kommer att kunna se alla infrastruktur resurser för BareMetal, samt deras tillstånd och attribut, i Azure Portal. Du kan också använda instanserna och öppna tjänst begär Anden och support biljetter därifrån. 

## <a name="operational-model"></a>Drifts modell
BareMetal-infrastrukturen är ISO 27001, ISO 27017, SOC 1 och SOC 2-kompatibel. Den använder också en BYOL-modell (a-Your-License): operativ system, specialiserad arbets belastning och program från tredje part.  

Så snart du får till gång till rot åtkomst och fullständig kontroll, antar du ansvar för:
- Designa och implementera säkerhets kopierings-och återställnings lösningar, hög tillgänglighet och haveri beredskap.
- Licensiering, säkerhet och support för operativ systemet och program vara från tredje part.

Microsoft ansvarar för:
- Tillhandahålla maskin vara för specialiserade arbets belastningar. 
- Etablering av operativ systemet.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagram över BareMetal infrastruktur support modell." border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal-instans stämpel

BareMetal-instansens stämpel kombinerar följande komponenter:

- **Data behandling:** Servrar som baseras på generationens Intel Xeon-processorer och som ger den nödvändiga dator kapaciteten och är certifierade för den specialiserade arbets belastningen.

- **Nätverk:** En enhetlig höghastighets nätverks infrastruktur som sammankopplar data bearbetnings-, lagrings-och LAN-komponenter.

- **Lagring:** En infrastruktur som nås via en enhetlig nätverks infrastruktur resurs.

Inom infrastrukturen för flera innehavare av BareMetal-stämpeln distribueras kunderna i isolerade klienter. När du distribuerar en klient organisation namnger du en Azure-prenumeration i din Azure-registrering. Den här Azure-prenumerationen är den som faktureras för BareMetal-instanserna.

>[!NOTE]
>En kund som distribuerar en BareMetal-instans är isolerad i en klient. En klient är isolerad i nätverk, lagring och beräknings lager från andra klienter. Lagrings-och beräknings enheter som tilldelas till olika klienter kan inte se varandra eller kommunicera med varandra på sina BareMetal-instanser.

## <a name="operating-system"></a>Operativsystem
Under etableringen av BareMetal-instansen kan du välja det operativ system som du vill installera på datorerna. 

>[!NOTE]
>Kom ihåg att BareMetal-infrastrukturen är en BYOL modell.

De tillgängliga Linux OS-versionerna är:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Storage
BareMetal-infrastrukturen ger mycket redundant NFS-lagring och Fiber Channel-lagring. Infrastrukturen erbjuder djupgående integrering för företags arbets belastningar som SAP, SQL och mycket annat. Den ger också programkonsekventa data skydd och funktioner för data hantering. Verktygen för självbetjänings hantering erbjuder utrymmes effektiv ögonblicks bild, kloning och detaljerad replikering tillsammans med en enda panel med glas övervakning. Infrastrukturen ger noll RTO-funktioner för data tillgänglighet och behov av affärs kontinuitet. 

Lagrings infrastrukturen erbjuder:
- Upp till 4 x 100-GB överordnade länkar.
- Upp till 32 GB fiber kanals länkar.
- Alla Flash SSD-och NVMe-enheter.
- Extremt låg latens och högt data flöde.
- Skalar upp till 4 PB RAW-lagring. 
- Upp till 11 000 000 IOPS.

Dessa data åtkomst protokoll stöds: 
- iSCSI 
- NFS (v3 eller v4) 
- Fiber kanal 
- NVMe över FC  

## <a name="networking"></a>Nätverk
Arkitekturen i Azure Network Services är en viktig komponent för en lyckad distribution av specialiserade arbets belastningar i BareMetal-instanser. Det är troligt att det inte redan finns några IT-system i Azure. Azure erbjuder nätverks teknik som gör att Azure ser ut som ett virtuellt Data Center till dina lokala program distributioner. De Azure-nätverks funktioner som krävs för BareMetal-instanser innehåller:

- Virtuella Azure-nätverk som är anslutna till den Azure ExpressRoute-krets som ansluter till dina lokala nätverks resurser.
- ExpressRoute-kretsen som ansluter lokalt till Azure bör ha en minsta bandbredd på 1 Gbit/s eller högre.
- Utökade Active Directory och DNS i Azure, eller som körs helt i Azure.

Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en privat anslutning med hjälp av anslutnings leverantören. Du kan använda **ExpressRoute Local** för kostnads effektiv data överföring mellan din lokala plats och den Azure-region som du vill använda. Om du vill utöka anslutningen mellan politiska gränser kan du aktivera **ExpressRoute Premium**. 

BareMetal-instanser är etablerade i Azure VNet-serverns IP-adressintervall.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Arkitektur diagram för Azure dataBareMetals-infrastruktur diagram." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

Arkitekturen som visas är uppdelad i tre delar:
- **Vänster:** Visar den lokala lokala infrastrukturen som kör olika program, ansluter via partnern eller den lokala Edge-routern som Equinix. Mer information finns i [anslutnings leverantörer och platser: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Center:** Visar [ExpressRoute](../expressroute/expressroute-introduction.md) som tillhandahålls med din Azure-prenumeration som erbjuder anslutning till Azure Edge Network.
- **Höger:** Visar Azure-IaaS och i det här fallet använder virtuella datorer som värdar för dina program, som är etablerade i det virtuella Azure-nätverket.
- **Längst ned:** Visar hur du använder din ExpressRoute-Gateway aktive rad med [ExpressRoute FastPath](../expressroute/about-fastpath.md) för BareMetal-anslutningar med låg latens.   
   >[!TIP]
   >Din ExpressRoute-gateway bör vara UltraPerformance för att stödja detta. Mer information finns i [om ExpressRoute virtuella](../expressroute/expressroute-about-virtual-network-gateways.md)nätverksgateway.

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig att identifiera och interagera med BareMetal-instanser via Azure Portal.

> [!div class="nextstepaction"]
> [Hantera BareMetal-instanser via Azure Portal](connect-baremetal-infrastructure.md)
