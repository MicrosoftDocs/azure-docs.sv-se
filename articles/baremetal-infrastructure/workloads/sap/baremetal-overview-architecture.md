---
title: Översikt över BareMetal Infrastructure Preview i Azure
description: Översikt över BareMetal-infrastrukturen i Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: eb4dc129719dc410f7101598e3d72e68f17809c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97860988"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Vad är BareMetal Infrastructure Preview på Azure?

Azure BareMetal-infrastrukturen ger en säker lösning för migrering av anpassade företags arbets belastningar. BareMetal-instanserna är icke-delade värd/Server-maskinvara som tilldelats dig. Den låser upp din lokal-lösning med specialiserade arbets belastningar som kräver certifierade maskinvaru-, licensierings-och support avtal. Azure hanterar övervakning och underhåll av infrastrukturen för dig, medan övervakning av operativ system (OS) och program övervakning är i ägarskapet.

BareMetal-infrastrukturen tillhandahåller en sökväg för att modernisera din infrastruktur i landskapet samtidigt som du behåller dina befintliga investeringar och arkitektur. Med BareMetal-infrastrukturen kan du hämta specialiserade arbets belastningar till Azure, vilket ger dig till gång till och integrering med Azure-tjänster med låg latens.

## <a name="sku-availability-in-azure-regions"></a>SKU-tillgänglighet i Azure-regioner
BareMetal-infrastruktur för specialiserade och allmänna arbets belastningar är tillgänglig, från och med fyra regioner baserade på revision 4,2-stämplar (rev 4,2):
- Europa, västra
- Europa, norra
- USA, östra 2
- USA, södra centrala

>[!NOTE]
>**Rev 4,2** är den senaste ommärkta BareMetal-infrastrukturen med den befintliga rev 4-arkitekturen.  Rev 4 ger närmare närhet till Azures virtuella dator värdar. Den har betydande förbättringar i nätverks fördröjningen mellan virtuella Azure-datorer och BareMetal-instanser som distribueras i rev 4-stämplar eller rader.  Du kan komma åt och hantera dina BareMetal-instanser via Azure Portal. 

## <a name="support"></a>Support
BareMetal-infrastrukturen är ISO 27001, ISO 27017, SOC 1 och SOC 2-kompatibel.  Den använder också en BYOL-modell (a-Your-License): operativ system, specialiserad arbets belastning och program från tredje part.  

Så snart du får till gång till rot åtkomst och fullständig kontroll, antar du ansvar för:
- Designa och implementera säkerhets kopierings-och återställnings lösningar, hög tillgänglighet och haveri beredskap
- Licensiering, säkerhet och support för operativ system och program vara från tredje part

Microsoft ansvarar för:
- Tillhandahålla maskin vara för specialiserade arbets belastningar 
- Etablering av operativ systemet

:::image type="content" source="media/baremetal-support-model.png" alt-text="BareMetal Infrastructure support modell" border="false":::

## <a name="compute"></a>Compute
BareMetal-infrastrukturen erbjuder flera SKU: er för specialiserade arbets belastningar. Tillgängliga SKU: er är tillgängliga från det mindre systemet med två socklar till 24-sockelsystemet. Använd de arbets belastnings lager som är specifika för din specialiserade arbets belastning.

BareMetal-instansens stämpel kombinerar följande komponenter:

- **Data behandling:** Servrar som baseras på en annan generation Intel Xeon-processorer som tillhandahåller nödvändig data behandling och är certifierade för den specialiserade arbets belastningen.

- **Nätverk:** En enhetlig höghastighets nätverks infrastruktur som sammankopplar data bearbetnings-, lagrings-och LAN-komponenter.

- **Lagring:** En infrastruktur som nås via en enhetlig nätverks infrastruktur resurs.

Inom infrastrukturen för flera innehavare av BareMetal-stämpeln distribueras kunderna i isolerade klienter. När du distribuerar en klient organisation namnger du en Azure-prenumeration i din Azure-registrering. Den här Azure-prenumerationen är den som BareMetal-instanserna faktureras.

>[!NOTE]
>En kund som distribueras i BareMetal-instansen isoleras till en klient. En klient är isolerad i nätverk, lagring och beräknings lager från andra klienter. Lagrings-och beräknings enheter tilldelade till olika klienter kan inte se varandra eller kommunicera med varandra på BareMetal-instanserna.

## <a name="os"></a>Operativsystem
Under etableringen av BareMetal-instansen kan du välja det operativ system som du vill installera på datorerna. 

>[!NOTE]
>Kom ihåg att BareMetal-infrastrukturen är en BYOL modell.

De tillgängliga Linux OS-versionerna är:
- Red Hat Enterprise Linux (RHEL) 7,6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Storage
BareMetal-instanser baserat på en speciell SKU-typ levereras med fördefinierad NFS-lagring för den aktuella arbets belastnings typen. När du etablerar BareMetal kan du etablera mer lagrings utrymme baserat på din beräknade tillväxt genom att skicka en supportbegäran. All lagring levereras med en allt-flash-disk i revision 4,2 med stöd för NFSv3 och NFSv4. Den nyare versionen 4,5 NVMe SSD är tillgänglig. Mer information om lagrings storlek finns i avsnittet [BareMetal arbets belastnings typ](../../../virtual-machines/workloads/sap/get-started.md) .

>[!NOTE]
>Det lagrings utrymme som används för BareMetal uppfyller [Federal Information Processing standard (FIPS) publikation 140-2](/microsoft-365/compliance/offering-fips-140-2) -krav som erbjuder kryptering i vila som standard. Data lagras på ett säkert sätt på diskarna.

## <a name="networking"></a>Nätverk
Arkitekturen i Azure Network Services är en viktig komponent för en lyckad distribution av specialiserade arbets belastningar i BareMetal-instanser. Det är troligt att det inte redan finns några IT-system i Azure. Azure erbjuder nätverks teknik som gör att Azure ser ut som ett virtuellt Data Center till dina lokala program distributioner. De Azure-nätverks funktioner som krävs för BareMetal-instanser är:

- Virtuella Azure-nätverk är anslutna till ExpressRoute-kretsen som ansluter till dina lokala nätverks resurser.
- En ExpressRoute-krets som ansluter lokalt till Azure bör ha en minsta bandbredd på 1 Gbit/s eller högre.
- Utökad Active Directory och DNS i Azure eller helt igång i Azure.

Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft Cloud via en privat anslutning med hjälp av anslutnings leverantören. Du kan aktivera **ExpressRoute Premium** för att utöka anslutningen mellan de politiska gränserna eller använda **ExpressRoute Local** för kostnads effektiv data överföring mellan platsen nära den Azure-region som du vill ha.

BareMetal-instanser är etablerade i Azure VNET-serverns IP-adressintervall.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal-infrastruktur diagram" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

Arkitekturen som visas är uppdelad i tre delar:
- **Left:** visar den lokala infrastruktur som kör olika program, som ansluter via partnern eller den lokala Edge-routern som Equinix. Mer information finns i [anslutnings leverantörer och platser: Azure ExpressRoute](../../../expressroute/expressroute-locations.md).
- **Center:** visar [ExpressRoute](../../../expressroute/expressroute-introduction.md) som tillhandahålls med din Azure-prenumeration som erbjuder anslutning till Azure Edge Network.
- **Höger:** visar Azure-IaaS och i det här fallet använder virtuella datorer som värd för dina program, som är etablerade i det virtuella Azure-nätverket.
- **Längst ned:** visar hur du använder din ExpressRoute-Gateway aktive rad med [ExpressRoute FastPath](../../../expressroute/about-fastpath.md) för BareMetal-anslutningar med låg latens.   
   >[!TIP]
   >Din ExpressRoute-gateway bör vara UltraPerformance för att stödja detta.  Mer information finns i [om ExpressRoute virtuella](../../../expressroute/expressroute-about-virtual-network-gateways.md)nätverksgateway.

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig att identifiera och interagera med BareMetal-instans enheter via Azure Portal.

> [!div class="nextstepaction"]
> [Hantera BareMetal-instanser via Azure-portalen](baremetal-infrastructure-portal.md)