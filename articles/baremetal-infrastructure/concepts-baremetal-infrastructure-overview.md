---
title: Vad är BareMetal-infrastruktur i Azure?
description: Ger en översikt över BareMetal-infrastrukturen på Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c0fd250a63ce93d3f8b62dfe76fe753c928801ce
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536894"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Vad är BareMetal-infrastruktur i Azure?

Microsoft Azure en molninfrastruktur med en mängd integrerade molntjänster som uppfyller dina affärsbehov. I vissa fall kan du dock behöva köra tjänster på servrar utan virtualiseringslager. Du kan behöva rotåtkomst och kontroll över operativsystemet (OS). För att uppfylla ett sådant behov erbjuder Azure BareMetal-infrastruktur för flera viktiga och verksamhetskritiska program.

BareMetal-infrastrukturen består av dedikerade BareMetal-instanser (beräkningsinstanser), högpresterande och program lämplig lagring (NFS, ISCSI och Fiber Channel), samt en uppsättning funktionsspecifika virtuella LAN (VLAN) i en isolerad miljö. Lagring kan delas mellan BareMetal-instanser för att aktivera funktioner som utskalningskluster eller för att skapa par med hög tillgänglighet med STONITH.
 
Den här miljön har också särskilda VLAN som du kan komma åt om du kör virtuella datorer (VM) på ett eller flera virtuella Azure-nätverk (VNet) i din Azure-prenumeration. Hela miljön representeras som en resursgrupp i din Azure-prenumeration.

BareMetal-infrastrukturen finns i över 30 SKU:er från servrar med 2 socket till 24 sockets och minne på mellan 1,5 TB och upp till 24 TB. En stor uppsättning SKU:er är också tillgängliga med oktant minne. Azure erbjuder det största utbudet av bare metal-instanser i ett moln i hyperskala.

## <a name="why-baremetal-infrastructure"></a>Varför BareMetal-infrastruktur?  

Vissa centrala arbetsbelastningar i företaget består av tekniker som inte är utformade för att köras i en typisk virtualiserad molnmiljö. De kräver särskild arkitektur, certifierad maskinvara eller ovanligt stora storlekar. Även om dessa tekniker har de mest avancerade funktionerna för dataskydd och affärskontinuisering, är dessa funktioner inte byggda för det virtualiserade molnet. De är mer känsliga för svarstider, grannar med brus och kräver mycket mer kontroll över ändringshantering och underhållsaktivitet.

BareMetal-infrastrukturen har skapats, certifierats och testats för en uppsättning sådana program. Azure var först med att erbjuda sådana lösningar och har sedan dess lett med den största portföljen och mest avancerade system.

BareMetal Infrastructure erbjuder följande fördelar: 

- Dedikerade instanser
- Certifierad maskinvara för specialiserade arbetsbelastningar
    - SAP (se [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (se [Oracle-dokument-ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Bare Metal (ingen beräkningsvirtualisering)
- Låg svarstid mellan virtuella Azure-datorer med värdprogram till BareMetal-instanser (0,35 ms)
- Alla Flash SSD och NVMe
    - Upp till 1 PB/klientorganisation 
    - IOPS upp till 1,2 miljoner per klientorganisation 
    - Nätverksbandbredd på 40/100 GB
    - Tillgänglig via NFS, ISCSI och FC
- Redundant strömförsörjning, strömförsörjning, nätverkskort, TOR, portar, WAN, lagring och hantering
- Snabbersättningar för ersättning vid ett fel (utan att du behöver konfigurera om)
- Kundkoordinerade underhållsfönster
- Programmedvetna ögonblicksbilder, arkiv, spegling och kloning


## <a name="baremetal-benefits"></a>BareMetal-fördelar  

BareMetal-infrastrukturen är avsedd för verksamhetskritiska arbetsbelastningar som kräver certifiering för att köra dina företagsprogram. BareMetal-instanserna är endast dedikerade till dig och du har fullständig åtkomst (rotåtkomst) till operativsystemet (OS). Du hanterar installation av operativsystem och program efter behov. Av säkerhetsskäl etableras instanserna i Azure Virtual Network (VNet) utan Internetanslutning. Endast tjänster som körs på dina virtuella datorer (VM) och andra Azure-tjänster i samma nätverk på nivå 2 kan kommunicera med dina BareMetal-instanser.  

BareMetal Infrastructure har följande fördelar: 

- Certifierad maskinvara för specialiserade arbetsbelastningar
- SAP (se [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533))
- Oracle (se [Oracle-dokument-ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- BareMetal-instans som inte är hyperviserad, ägarskap för enskild klientorganisation
- Låg fördröjning mellan virtuella Azure-värdprograms-VM:ar till BareMetal-instanser (0,35 ms)
- Alla Stöd för Flash SSD och NVMe
- Databas upp till 1 PB/klientorganisation 
- IOPS upp till 1,2 miljoner per klientorganisation 
- Nätverksbandbredd på 50 GB 

## <a name="sku-availability-in-azure-regions"></a>SKU-tillgänglighet i Azure-regioner

BareMetal Infrastructure erbjuder flera SKU:er som är certifierade för specialiserade arbetsbelastningar. Använd de arbetsbelastningsspecifika SKU:erna för att uppfylla dina behov.

- Stora instanser – allt från system med två socketar till fyra socketar.  
- Mycket stora instanser – allt från system med fyra socketar till tjugosocketar. 

BareMetal-infrastrukturen för specialiserade arbetsbelastningar är tillgänglig i följande Azure-regioner:
- Europa, västra
- Europa, norra
- Stöd för *zoner i Tyskland, västra centrala
- Stöd för USA, östra 2 *zoner
- Stöd för *zoner i USA, östra
- Stöd för *zoner i USA, västra
- Stöd för *zoner i USA, västra 2
- USA, södra centrala

>[!NOTE]
>**Stöd för** zoner avser tillgänglighetszoner i en region där BareMetal-instanser kan distribueras mellan zoner för hög återhämtning och tillgänglighet. Den här funktionen möjliggör stöd för aktiv-aktiv-skalning för flera platser.

## <a name="managing-baremetal-instances-in-azure"></a>Hantera BareMetal-instanser i Azure 

Beroende på dina behov kan program topologierna för BareMetal Infrastructure vara komplexa. Du kan distribuera flera instanser på en eller flera platser med delad eller dedikerad lagring och specialiserade LAN- och WAN-anslutningar. För BareMetal Infrastructure erbjuder Azure alltså en avskiljningsfångst av informationen av en CSA/GBB i fältet i en etableringsportal. 

När BareMetal-infrastrukturen har etablerats är operativsystemet, nätverk, lagringsvolymer, placering i zoner och regioner samt WAN-anslutningar mellan platser redan förkonfigurerade. Du är inställd på att registrera dina OS-licenser (BYOL), konfigurera operativsystemet och installera programlagret.

Du kommer att kunna se alla BareMetal-resurser, deras tillstånd och attribut, i Azure Portal. Du kan också använda instanserna och öppna tjänstbegäranden och supportärenden därifrån. 

## <a name="operational-model"></a>Driftsmodell
BareMetal-infrastrukturen är kompatibel med ISO 27001, ISO 27017, SOC 1 och SOC 2. Den använder också en BYOL-modell (Bring Your Own License): OPERATIVSYSTEM, specialiserad arbetsbelastning och program från tredje part.  

Så snart du får rotåtkomst och fullständig kontroll övertar du ansvaret för:
- Utforma och implementera lösningar för säkerhetskopiering och återställning, hög tillgänglighet och haveriberedskap.
- Licensiering, säkerhet och support för operativsystemet och programvara från tredje part.

Microsoft ansvarar för:
- Tillhandahålla maskinvara för specialiserade arbetsbelastningar. 
- Etablering av operativsystemet.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagram över Stödmodell för BareMetal-infrastruktur." border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal-instansstämpel

Stämpeln för BareMetal-instansen kombinerar följande komponenter:

- **Databehandling:** Servrar som baseras på genereringen av Intel Xeon-processorer som tillhandahåller den nödvändiga datorkapaciteten och är certifierade för den specialiserade arbetsbelastningen.

- **Nätverk:** En enhetlig nätverks fabric med hög hastighet sammankopplar beräknings-, lagrings- och LAN-komponenter.

- **Lagring:** En infrastruktur som nås via en enhetlig nätverksinfrastruktur.

I BareMetal-stämpelts infrastruktur för flera innehavare distribueras kunder i isolerade klienter. När du distribuerar en klient kan du namnge en Azure-prenumeration i din Azure-registrering. Den här Azure-prenumerationen faktureras för dina BareMetal-instanser.

>[!NOTE]
>En kund som distribuerar en BareMetal-instans isoleras till en klientorganisation. En klientorganisation är isolerad i nätverks-, lagrings- och beräkningslagret från andra klienter. Lagrings- och beräkningsenheter som tilldelats olika klienter kan inte se varandra eller kommunicera med varandra på sina BareMetal-instanser.

## <a name="operating-system"></a>Operativsystem
Under etableringen av BareMetal-instansen kan du välja det operativsystem som du vill installera på datorerna. 

>[!NOTE]
>Kom ihåg att BareMetal-infrastrukturen är en BYOL-modell.

De tillgängliga Linux OS-versionerna är:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Storage
BareMetal Infrastructure tillhandahåller mycket redundant NFS-lagring och Fiber Channel-lagring. Infrastrukturen erbjuder djupgående integrering för företagsarbetsbelastningar som SAP, SQL med mera. Det ger också program konsekvent dataskydd och funktioner för datahantering. Självbetjäningshanteringsverktygen erbjuder utrymmeseffektiva funktioner för ögonblicksbilder, kloning och detaljerad replikering tillsammans med en enda fönsterövervakning. Infrastrukturen ger noll RPO- och RTO-funktioner för datatillgänglighet och affärskontinuktionsbehov. 

Lagringsinfrastrukturen erbjuder:
- Upp till 4 x 100 GB förlänkar.
- Upp till 32 GB Fibre Channel-länkar.
- Alla flash SSD- och NVMe-enheter.
- Extremt låg latens och högt dataflöde.
- Skalar upp till 4 PB rålagring. 
- Upp till 11 miljoner IOPS.

Dessa protokoll för dataåtkomst stöds: 
- iSCSI 
- NFS (v3 eller v4) 
- Fiberkanal 
- NVMe över FC  

## <a name="networking"></a>Nätverk
Arkitekturen för Azure-nätverkstjänster är en viktig komponent för en lyckad distribution av specialiserade arbetsbelastningar i BareMetal-instanser. Det är troligt att inte alla IT-system redan finns i Azure. Azure erbjuder nätverksteknik som gör att Azure ser ut som ett virtuellt datacenter för dina lokala programvarudistributioner. Nätverksfunktionerna i Azure som krävs för BareMetal-instanser omfattar:

- Virtuella Azure-nätverk som är Azure ExpressRoute en krets som ansluter till dina lokala nätverkstillgångar.
- ExpressRoute-kretsen som ansluter lokalt till Azure bör ha en minsta bandbredd på 1 Gbit/s eller högre.
- Utökad Active Directory och DNS i Azure, eller helt igång i Azure.

Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en privat anslutning med hjälp av en anslutningsleverantör. Du kan använda **ExpressRoute Local** för kostnadseffektiv dataöverföring mellan din lokala plats och den Azure-region som du vill använda. Om du vill utöka anslutningen över geopolitiska gränser kan du aktivera **ExpressRoute Premium.** 

BareMetal-instanser etableras inom azure-serverns IP-adressintervall.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Arkitekturdiagram över Azure BareMetal Infrastructure-diagram." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

Arkitekturen som visas är indelad i tre avsnitt:
- **Vänster:** Visar kundens lokala infrastruktur som kör olika program och ansluter via partner- eller lokal gränsrouter som Equinix. Mer information finns i [Anslutningsproviders och platser: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Center:** Visar [ExpressRoute som](../expressroute/expressroute-introduction.md) etablerats med din Azure-prenumeration som erbjuder anslutning till Azure Edge-nätverk.
- **Höger:** Visar Azure IaaS, och i det här fallet användning av virtuella datorer som värd för dina program, som etableras i ditt virtuella Azure-nätverk.
- **Nederkant:** Visar hur du använder din ExpressRoute-gateway aktiverad [med ExpressRoute FastPath](../expressroute/about-fastpath.md) för BareMetal-anslutning som ger korta svarstider.   
   >[!TIP]
   >För att stödja detta bör Din ExpressRoute-gateway vara UltraPerformance. Mer information finns i Om [virtuella ExpressRoute-nätverksgatewayer.](../expressroute/expressroute-about-virtual-network-gateways.md)

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig hur du identifierar och interagerar med BareMetal-instanser via Azure Portal.

> [!div class="nextstepaction"]
> [Hantera BareMetal-instanser via Azure Portal](connect-baremetal-infrastructure.md)
