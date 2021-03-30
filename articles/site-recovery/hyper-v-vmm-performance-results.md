---
title: Testa replikering av virtuella Hyper-V-datorer till en sekundär plats med VMM med hjälp av Azure Site Recovery
description: Den här artikeln innehåller information om prestanda testning för replikering av virtuella Hyper-V-datorer i VMM-moln till en sekundär plats med hjälp av Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 6c8219214e7053dcf6b119f6cd5dc97daaa355f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327645"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Test resultat för Hyper-V-replikering till en sekundär plats


Den här artikeln innehåller resultatet av prestanda testning när du replikerar virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till ett sekundärt Data Center.

## <a name="test-goals"></a>Test mål

Syftet med testningen var att undersöka hur Site Recovery utför under stabilt tillstånds replikering.

- Replikering av stabilt tillstånd sker när virtuella datorer har slutfört den inledande replikeringen och synkroniserar delta ändringar.
- Det är viktigt att mäta prestanda med stabilt tillstånd, eftersom det är det tillstånd som de flesta virtuella datorer är kvar på, om oväntade avbrott inträffar.
- Test distributionen består av två lokala platser, med en VMM-Server på varje plats. Den här test distributionen är typisk för distribution av huvud kontor och avdelnings kontor, med huvud kontor som fungerar som den primära platsen och avdelnings kontoret som sekundär plats eller återställnings plats.

## <a name="what-we-did"></a>Vad vi gjorde

Det här är vad vi gjorde i test passet:

1. Skapade virtuella datorer med VMM-mallar.
2. Startade virtuella datorer och hämtade bas linje prestanda mått under 12 timmar.
3. Skapade moln på den primära och återställnings VMM-servern.
4. Konfigurerad replikering i Site Recovery, inklusive mappning mellan käll-och återställnings moln.
5. Aktiverat skydd för virtuella datorer och har tillåtelse att slutföra den inledande replikeringen.
6. Väntade några timmar för system stabilisering.
7. Hämtade prestanda mått under 12 timmar, där alla virtuella datorer befann sig i ett förväntat replikeringstillstånd för de 12 timmarna.
8. Mätte delta mellan bas linje prestanda mått och prestanda mått för replikering.


## <a name="primary-server-performance"></a>Primär server prestanda

* Hyper-V-replikering (används av Site Recovery) asynkront spårar ändringar i en loggfil, med minsta möjliga lagrings belastning på den primära servern.
* Hyper-V-replikering använder sig av fristående cacheminne för att minimera IOPS-omkostnader för spårning. Den lagrar skrivningar till VHDX i minnet och tömmer dem till logg filen före den tid som loggen skickas till återställnings platsen. En disk tömning sker också om skrivningar når en fördefinierad gräns.
* I diagrammet nedan visas den stabila statusen IOPS för replikering. Vi kan se att IOPS-belastningen på grund av replikering är cirka 5%, vilket är ganska lågt.

  ![Diagram som visar det stabila läget för IOPS-kostnader för replikering.](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V-replikering använder minne på den primära servern för att optimera disk prestanda. Som du ser i följande diagram är minnes belastningen på alla servrar i det primära klustret marginaler. Den minnes kapacitet som visas är procent andelen minne som används av replikering, jämfört med det totala installerade minnet på Hyper-V-servern.

![Primära resultat](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V-replikering har lägsta CPU-belastning. Som det visas i diagrammet är replikeringens kostnader i intervallet 2-3%.

![Diagram som visar belastningen på replikering är i intervallet 2-3%.](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Sekundär server prestanda

Hyper-V-replikering använder en liten mängd minne på återställnings servern för att optimera antalet lagrings åtgärder. Diagrammet sammanfattar minnes användningen på återställnings servern. Den minnes kapacitet som visas är procent andelen minne som används av replikering, jämfört med det totala installerade minnet på Hyper-V-servern.

![Diagram som sammanfattar minnes användningen på återställnings servern.](./media/hyper-v-vmm-performance-results/IC744916.png)

Mängden i/O-åtgärder på återställnings platsen är en funktion av antalet skriv åtgärder på den primära platsen. Nu ska vi titta på de totala I/O-åtgärderna på återställnings platsen jämfört med de totala I/O-åtgärderna och skriv åtgärderna på den primära platsen. Diagrammen visar att den totala IOPS på återställnings platsen är

* Cirka 1,5 gånger skrivs IOPS på den primära.
* Cirka 37% av det totala antalet IOPS på den primära platsen.

![Diagram som visar en jämförelse av IOPS på primära och sekundära platser.](./media/hyper-v-vmm-performance-results/IC744917.png)

![Sekundära resultat](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Påverkan på nätverks användning

Ett genomsnitt på 275 MB per sekund av nätverks bandbredden användes mellan primära noder och återställnings noder (med komprimering aktiverat), mot en befintlig bandbredd på 5 GB per sekund.

![Resultat av nätverks användning](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Inverkan på VM-prestanda

Ett viktigt övervägande är effekten av replikering av produktions arbets belastningar som körs på de virtuella datorerna. Om den primära platsen har allokerats för replikering bör det inte påverka arbets belastningarna. Hyper-V-replikeringens mekanism för enkel spårning säkerställer att arbets belastningar som körs på de virtuella datorerna inte påverkas under stabilt tillstånd. Detta illustreras i följande diagram.

I det här diagrammet visas IOPS som utförs av virtuella datorer som kör olika arbets belastningar, innan och efter att replikering har Aktiver ATS. Du kan observera att det inte finns någon skillnad mellan de två.

![Resultat av replik effekter](./media/hyper-v-vmm-performance-results/IC744920.png)

I följande diagram visas data flödet för virtuella datorer som kör olika arbets belastningar, innan och efter att replikering har Aktiver ATS. Du kan observera att replikeringen inte har någon betydande inverkan.

![Resultat repliks effekter](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Slutsats

Resultaten visar tydligt att Site Recovery, tillsammans med Hyper-V-replikering, skalar väl med lägsta belastning för ett stort kluster. Site Recovery tillhandahåller enkel distribution, replikering, hantering och övervakning. Hyper-V-replikering tillhandahåller den infrastruktur som krävs för skalning av lyckade replikeringar. 

## <a name="test-environment-details"></a>Test miljö information

### <a name="primary-site"></a>Primär plats

* Den primära platsen har ett kluster som innehåller fem Hyper-V-servrar, som kör 470 virtuella datorer.
* De virtuella datorerna kör olika arbets belastningar och har Site Recovery skydd aktiverat.
* Lagring för klusternoden tillhandahålls av ett iSCSI SAN. Model – Hitachi HUS130.
* Varje kluster Server har fyra nätverkskort (NIC) på en Gbit/s.
* Två nätverkskort är anslutna till ett privat iSCSI-nätverk och två är anslutna till ett externt företags nätverk. Ett av de externa nätverken är reserverat för enbart kluster kommunikation.

![Primära maskin varu krav](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Modell | Processor | Antal processorer | NIC | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i kluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 har 256 |Dell™ PowerEdge-™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |4 |I Gbit/s x 4 |Windows Server Data Center 2012 R2 (x64) + Hyper-V-roll |
| VMM-server |2 | | |2 |1 Gbit/s |Windows Server-databas 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundär plats

* Den sekundära platsen har ett kluster med sex noder.
* Lagring för klusternoden tillhandahålls av ett iSCSI SAN. Model – Hitachi HUS130.

![Primär maskin varu specifikation](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Modell | Processor | Antal processorer | NIC | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i kluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge-™ R720 |Intel (R) Xeon (R) CPU E5-2630 0 \@ 2.30 GHz |2 |I Gbit/s x 4 |Windows Server Data Center 2012 R2 (x64) + Hyper-V-roll |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge-™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Data Center 2012 R2 (x64) + Hyper-V-roll |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge-™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Data Center 2012 R2 (x64) + Hyper-V-roll |
| VMM-server |2 | | |2 |1 Gbit/s |Windows Server-databas 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Server arbets belastningar

* I test syfte har vi valt arbets belastningar som ofta används i företags kund scenarier.
* Vi använder [IOMeter](http://www.iometer.org) med den arbets belastnings egenskap som sammanfattas i tabellen för simulering.
* Alla IOMeter-profiler är inställda på att skriva slumpmässiga bytes för att simulera värsta tänkbara Skriv mönster för arbets belastningar.

| Arbetsbelastning | I/O-storlek (KB) | % Åtkomst | % Läsning | Utestående I/o | I/O-mönster |
| --- | --- | --- | --- | --- | --- |
| Filserver |4<br />8<br />16<br />32<br />64 |60 %<br />20%<br />5 %<br />5 %<br />10 % |80 %<br />80 %<br />80 %<br />80 %<br />80 % |8<br />8<br />8<br />8<br />8 |Alla 100% slumpmässiga |
| SQL Server (volym 1)<br />SQL Server (volym 2) |8<br />64 |100 %<br />100 % |70 %<br />0 % |8<br />8 |100% slumpmässig<br />100% sekventiell |
| Exchange |32 |100 % |67 % |8 |100% slumpmässig |
| Arbets Station/VDI |4<br />64 |66%<br />34% |70 %<br />95 % |1<br />1 |Både 100% slumpmässig |
| Webb fil Server |4<br />8<br />64 |33%<br />34%<br />33% |95 %<br />95 %<br />95 % |8<br />8<br />8 |Alla 75% slumpmässiga |

### <a name="vm-configuration"></a>Konfiguration av virtuell dator

* 470 virtuella datorer i det primära klustret.
* Alla virtuella datorer med VHDX-diskar.
* Virtuella datorer som kör arbets belastningar sammanfattade i tabellen. Alla har skapats med VMM-mallar.

| Arbetsbelastning | Antal virtuella datorer | Minsta RAM-minne (GB) | Maximalt RAM-minne (GB) | Logisk disk storlek (GB) per virtuell dator | Högsta IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Filserver |50 |1 |2 |552 |22 |
| VDI |149 |0,5 |1 |80 |6 |
| Webbserver |149 |0,5 |1 |80 |6 |
| TOTALT |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery inställningar

* Site Recovery konfigurerades för lokalt skydd
* VMM-servern har fyra moln konfigurerade, som innehåller Hyper-V-kluster servrar och deras virtuella datorer.

| Primärt VMM-moln | Skyddade virtuella datorer | Replikeringsfrekvens | Ytterligare återställnings punkter |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minuter |Inget |
| PrimaryCloudRpo30s |47 |30 sekunder |Inget |
| PrimaryCloudRpo30sArp1 |47 |30 sekunder |1 |
| PrimaryCloudRpo5m |235 |5 min |Inget |

### <a name="performance-metrics"></a>Prestandamått

I tabellen sammanfattas de prestanda mått och räknare som mättes i distributionen.

| Metric | Räknare |
| --- | --- |
| Processor |\Processor(_Total)\% processortid |
| Tillgängligt minne |\Memory\Available MB |
| IOPS |\PhysicalDisk (_Total) \ disk överföringar/SEK |
| VM-Läs åtgärder/SEK |\Hyper-V Virtual Storage Device ( \<VHD> ) \Read-åtgärder/SEK |
| Åtgärder för VM-skrivning (IOPS)/SEK |\Hyper-V Virtual Storage Device ( \<VHD> ) \Write-åtgärder/S |
| Läst data flöde för virtuell dator |\Hyper-V virtuell lagrings enhet ( \<VHD> ) \Read byte/s |
| Skriv data flöde för virtuell dator |\Hyper-V virtuell lagrings enhet ( \<VHD> ) \Write byte/s |

## <a name="next-steps"></a>Nästa steg

[Konfigurera replikering](hyper-v-vmm-disaster-recovery.md)
