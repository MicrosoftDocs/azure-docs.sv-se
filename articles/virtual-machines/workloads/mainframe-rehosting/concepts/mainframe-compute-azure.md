---
title: Flytta stordator beräkning till Azure Virtual Machines
description: Azure Compute Resources jämför favorably med stordator kapacitet så att du kan migrera och modernisera IBM Z14-program.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.openlocfilehash: 32f259f20e0e24b4d5346c598e23fdc2df48dee6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556442"
---
# <a name="move-mainframe-compute-to-azure"></a>Flytta stordator beräkning till Azure

Stordatorer har ett rykte för hög tillförlitlighet och tillgänglighet och fortsätter att vara det betrodda stamnätet för många företag. De är ofta tänkta att ha nästan obegränsad skalbarhet och dator kraft. Vissa företag har dock utökar kapaciteten hos de största tillgängliga stordatorerna. Om det låter dig som det låter Azure erbjuda dig flexibilitet, räckvidd och infrastruktur besparingar.

Om du vill köra stordator belastningar på Microsoft Azure måste du veta hur din stordators beräknings funktioner jämför med Azure. Den här artikeln beskriver hur du får ett jämförbart resultat på Azure baserat på en stordator i IBM Z14 (den mest aktuella modellen när du skriver).

Kom igång genom att tänka på miljön sida vid sida. Följande bild jämför en stordator miljö för att köra program i en Azure-värd miljö.

![Azure-tjänster och emuleringsklienter erbjuder jämförbar support och effektiviserar migreringen](media/mainframe-compute-azure.png)

Kraften i stordatorer används ofta för OLTP-system (Online Transaction Processing) som hanterar miljon tals uppdateringar för tusentals användare. Dessa program använder ofta program vara för transaktions bearbetning, skärm hantering och formulär poster. De kan använda ett CICS (Customer information Control system), ett informations hanterings system (IMS) eller ett transaktions gränssnitts paket (TIP).

När bilden visas kan en TPM-emulator i Azure hantera CICS-och IMS-arbetsbelastningar. En batch system-emulator på Azure utför rollen som JCL (Job Control Language). Stordator data migreras till Azure-databaser, t. ex. Azure SQL Database. Azure-tjänster eller annan program vara som finns i Azure Virtual Machines kan användas för system hantering.

## <a name="mainframe-compute-at-a-glance"></a>En översikt över stordatorer

I Z14-stordatoren är processorerna ordnade i upp till fyra *lådor*. En *låda* är helt enkelt ett kluster med processorer och krets uppsättningar. Varje låda kan ha sex aktiva chips (Central processor) och varje CP har 10 system Controller-chips (SC). I Intel x86-terminologi finns det sex Sockets per låda, 10 kärnor per socket och fyra lådor. Den här arkitekturen ger en grov motsvarighet till 24 Sockets och 240 kärnor, maximum för en Z14.

Snabb Z14 CP har en hastighet på 5,2 GHz. Vanligt vis levereras en Z14 med alla CPs i rutan. De är aktiverade vid behov. En kund debiteras vanligt vis i minst fyra timmar av beräknings tid per månad trots den faktiska användningen.

En stordator processor kan konfigureras som en av följande typer:

- Generell användnings processor (GP)
- System z Integrated information processor (zIIP)
- Integrerad funktion för Linux (IFL)-processor
- System Assist-processor (SAP)
- ICF-processor (Integrated kopplings funktion)

## <a name="scaling-mainframe-compute-up-and-out"></a>Skalning av stordatorer upp och ut

IBM-stordatorer ger möjlighet att skala upp till 240 kärnor (den aktuella Z14-storleken för ett enda system). Dessutom kan IBM-stordatorer skala ut genom en funktion som kallas kopplings funktion (CF). CF gör att flera stordator system kan komma åt samma data samtidigt. Med hjälp av CF är den parallella Sysplex-tekniken grupper stordator processor i kluster. När den här guiden skrevs, stödde parallell Sysplex-funktionen 32 grupperingar av 64-processorer. Upp till 2 048-processorer kan grupperas på det här sättet för att skala ut beräknings kapaciteten.

Ett CF gör det möjligt för beräknings kluster att dela data med direkt åtkomst. Den används för att låsa information, cachelagra information och listan över delade data resurser. En parallell Sysplex som använder en eller flera CFs kan ses som ett "delat allt"-beräknings kluster. Mer information om dessa funktioner finns [parallell Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) på IBM-webbplatsen.

Programmen kan använda dessa funktioner för att ge både skalbar prestanda och hög tillgänglighet. För information om hur CICS kan använda parallella Sysplex med CF, laddar du ned [IBM CICS och kopplings funktionen: utöver de grundläggande](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) Redbook.

## <a name="azure-compute-at-a-glance"></a>En snabbt ITT på Azure Compute

Vissa personer är av misstag tro att Intel-baserade servrar inte är lika kraftfulla som stordatorer. De nya Core-täta, Intel-baserade systemen har dock lika mycket beräknings kapacitet som stordatorer. I det här avsnittet beskrivs IaaS-alternativ (Infrastructure-as-a-Service) för data behandling och lagring. Azure tillhandahåller PaaS-alternativ (Platform-as-a-Service) också, men den här artikeln fokuserar på de IaaS-alternativ som ger jämförbar stordator kapacitet.

Azure Virtual Machines ger beräknings kraft i flera olika storlekar och typer. I Azure motsvarar en virtuell processor (vCPU) en kärna på en stordator.

För närvarande tillhandahåller intervallet för storleken på virtuella Azure-datorer mellan 1 och 128 virtuella processorer. Typer av virtuella datorer (VM) är optimerade för vissa arbets belastningar. I följande lista visas till exempel de virtuella dator typerna (aktuella vid skrivning) och deras rekommenderade användnings områden:

| Storlek     | Typ och beskrivning                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D-serien | Generell användning med 64 vCPU och upp till 3,5 GHz klock hastighet                           |
| E-serien | Minne optimerat med upp till 64 virtuella processorer                                                 |
| F-serien | Compute-optimerad med upp till 64 virtuella processorer och 3.. 7 GHz klock hastighet                       |
| H-serien | Optimerad för HPC-program (data behandling med höga prestanda)                          |
| L-serien | Lagring som är optimerad för program med stora data flöden som backas upp av databaser som NoSQL |
| M-serien | Största beräknings-och minnesoptimerade virtuella datorer med upp till 128 virtuella processorer                        |

Mer information om tillgängliga virtuella datorer finns i [serien för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

En Z14-stordator kan ha upp till 240 kärnor. Z14-stordatorer använder dock nästan aldrig alla kärnor för ett enda program eller en arbets belastning. I stället är en stordator som särskiljer arbets belastningar till logiska partitioner (LPARs) och LPARs har klassificeringar – MIPS (miljon tals instruktioner per sekund) eller MSU (miljon enhets tjänster). När du avgör vilken jämförbar VM-storlek som behövs för att köra en stordator i Azure, faktor i MIPS-eller MSU-klassificeringen.

Följande är allmänna uppskattningar:

-   150 MIPS per vCPU

-   1 000 MIPS per processor

För att fastställa rätt VM-storlek för en specifik arbets belastning i en LPAR, optimera först den virtuella datorn för arbets belastningen. Bestäm sedan antalet virtuella processorer som behövs. En försiktig uppskattning är 150 MIPS per vCPU. Baserat på denna uppskattning, till exempel en virtuell dator i F-serien med 16 virtuella processorer, kunde enkelt stödja en IBM DB2-arbetsbelastning från en LPAR med 2 400 MIPS.

## <a name="azure-compute-scale-up"></a>Azure Compute-skalning

Virtuella datorer i M-serien kan skala upp till 128 virtuella processorer (vid den tidpunkt då den här artikeln skrevs). Med den restriktiva uppskattningen på 150 MIPS per vCPU, motsvarar den virtuella datorn i M-serien till cirka 19 000 MIPS. Den allmänna regeln för att uppskatta MIPS för en stordator är 1 000 MIPS per processor. En Z14-stordator kan ha upp till 24 processorer och tillhandahålla cirka 24 000 MIPS för ett enda stordator system.

Den största enkla Z14-stordatoren har ungefär 5 000 MIPS mer än den största virtuella datorn som är tillgänglig i Azure. Det är ännu viktigt att du jämför hur arbets belastningarna distribueras. Om ett stordator system har både ett program och en Relations databas, distribueras de vanligt vis på samma fysiska stordator – var och en i sin egen LPAR. Samma lösning på Azure distribueras ofta med en virtuell dator för programmet och en separat, lämplig storlek för den virtuella datorn för databasen.

Om t. ex. ett M64 vCPU-system har stöd för programmet och en M96-vCPU används för databasen, krävs ungefär 150 virtuella processorer – eller cirka 24 000 MIPS som följande bild visas.

![Jämför arbets belastnings distributioner på 24 000 MIPS](media/mainframe-compute-mips.png)

Metoden är att migrera LPARs till enskilda virtuella datorer. Sedan skalar Azure enkelt upp till den storlek som krävs för de flesta program som distribueras i ett enda stordator system.

## <a name="azure-compute-scale-out"></a>Azure Compute-utskalning

En av fördelarna med en Azure-baserad lösning är möjligheten att skala ut. Skalning gör nästan obegränsad beräknings kapacitet tillgänglig för ett program. Azure stöder flera metoder för att skala ut beräknings kraften:

- **Belastnings utjämning i ett kluster.** I det här scenariot kan ett program använda en [belastningsutjämnare](../../../../load-balancer/load-balancer-overview.md) eller Resource Manager för att sprida arbets belastningen mellan flera virtuella datorer i ett kluster. Om du behöver mer beräknings kapacitet läggs ytterligare virtuella datorer till i klustret.

- **Skalnings uppsättningar för virtuella datorer.** I det här burst-scenariot kan ett program skalas till ytterligare [beräknings resurser](../../../../virtual-machine-scale-sets/overview.md) baserat på användningen av virtuella datorer. När efter frågan finns kan antalet virtuella datorer i en skalnings uppsättning också gå nedåt, vilket garanterar en effektiv användning av data bearbetnings kraften.

- **PaaS-skalning.** Azure PaaS-erbjudanden skalar beräknings resurser. [Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) allokerar till exempel beräknings resurser för att möta ökningen av antalet begär Anden.

- **Kubernetes-kluster.** Program på Azure kan använda [Kubernetes-kluster](../../../../aks/concepts-clusters-workloads.md) för beräknings tjänster för angivna resurser. Azure Kubernetes service (AKS) är en hanterad tjänst som dirigerar Kubernetes noder, pooler och kluster i Azure.

Om du vill välja rätt metod för att skala ut beräknings resurser är det viktigt att förstå hur Azure och stordatorer skiljer sig. Nyckeln är hur – eller om – data delas av beräknings resurser. I Azure delas data (som standard) vanligt vis inte av flera virtuella datorer. Om data delning krävs av flera virtuella datorer i ett skalbart beräknings kluster, måste delade data finnas i en resurs som har stöd för den här funktionen. På Azure omfattar data delningen lagrings utrymme som beskrivs i följande avsnitt.

## <a name="azure-compute-optimization"></a>Optimering av Azure-beräkning

Du kan optimera varje nivå av bearbetning i en Azure-arkitektur. Använd den lämpligaste typen av virtuella datorer och funktioner för varje miljö. Följande bild visar ett möjligt mönster för att distribuera virtuella datorer i Azure för att stödja ett CICS-program som använder DB2. På den primära platsen distribueras produktion, för produktion och testning av virtuella datorer med hög tillgänglighet. Den sekundära platsen är för säkerhets kopiering och haveri beredskap.

Varje nivå kan också tillhandahålla lämplig katastrof återställnings tjänst. Till exempel kan virtuella datorer och databaser kräva en frekvent eller varm återställning, medan de virtuella datorerna i utvecklings-och testnings miljön stöder en kall återställning.

![Distribution med hög tillgänglighet som stöder haveri beredskap](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Nästa steg

- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Revärdering av stordatorer på Azure Virtual Machines](../overview.md)
- [Flytta stordator lagring till Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM-resurser

- [Parallella Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM-CICS och kopplings funktionen: utöver grunderna](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Skapa nödvändiga användare för en funktion installation av DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt – skapa instans kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 pureScale-klustrad databas lösning](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government moln för stordator program](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft och FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Fler migrerings resurser

- [Guide för att lyfta och flytta Azure Virtual Data Center](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
