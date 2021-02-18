---
title: Felsöka prestanda för virtuella Azure-datorer i Linux eller Windows
description: I den här artikeln beskrivs den virtuella datorn (VM) allmän prestanda fel sökning genom övervakning och övervakning av flask halsar och ger möjlighet till reparation av problem som kan uppstå.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: cde84a018d307bada6a6b6f3b5467b8addd007b1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571445"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Felsöka prestanda för virtuella Azure-datorer i Linux eller Windows

I den här artikeln beskrivs den virtuella datorn (VM) allmän prestanda fel sökning genom övervakning och övervakning av flask halsar och ger möjlighet till reparation av problem som kan uppstå. Förutom övervakning kan du också använda Perfinsights som kan ge en rapport med bästa praxis rekommendationer och nyckel Flask halsar runt i/o/CPU/minne. Perfinsights är tillgängligt för virtuella [Windows](./how-to-use-perfinsights.md) -och [Linux](./how-to-use-perfinsights-linux.md) -datorer i Azure.

Den här artikeln går igenom hur du använder övervakning för att diagnostisera Flask halsar i prestanda.

## <a name="enabling-monitoring"></a>Aktiverar övervakning

### <a name="azure-iaas-virtual-machine-monitoring"></a>Övervakning av virtuella Azure IAAS-datorer

Om du vill övervaka den virtuella gäst datorn använder du övervakning av virtuella Azure-datorer, som varnar dig om vissa resurs villkor på hög nivå. Om du vill kontrol lera om VM-diagnostik är aktiverat, se [Översikt över Azure resurs loggar](../../azure-monitor/essentials/tutorial-resource-logs.md). Om du ser följande har du förmodligen inte aktiverat diagnostiken:

![Övervakning har inte Aktiver ATS](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Aktivera diagnostik för virtuella datorer via Microsoft Azure Portal

Så här aktiverar du VM-diagnostik:

1. Gå till den virtuella datorn
2. Klicka på **Inställningar för diagnostik**
3. Välj lagrings kontot och klicka på **Aktivera övervakning på gästnivå**.

   ![Klicka på Inställningar och sedan diagnostik](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Du kan kontrol lera det lagrings konto som används för att konfigurera diagnostik på fliken **agent** under **diagnostikinställningar**.

![Kontrol lera lagrings kontot](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Aktivera diagnostik för lagrings konto via Azure Portal

Storage är en mycket viktig nivå när vi planerar att analysera IO-prestanda för en virtuell dator i Azure. För Storage-relaterade mått måste vi aktivera diagnostik som ett ytterligare steg. Detta kan också vara aktiverat om vi bara vill analysera de räknare som är relaterade till lagring.

1. Identifiera vilket lagrings konto (eller konton) som den virtuella datorn använder genom att välja den virtuella datorn. Klicka på **Inställningar** och sedan på **diskar**:

   ![Klicka på Inställningar, sedan på diskar](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. I portalen går du till lagrings kontot (eller kontona) för den virtuella datorn och arbetar med följande steg:

   1. Klicka på Översikt för det lagrings konto som du hittade i steg ovan.
   2. Standard mått visas. 

    ![Standard mått](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Klicka på någon av måtten som visar ett annat blad med fler alternativ för att konfigurera och lägga till mått.

   ![Lägg till mått](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Konfigurera följande alternativ:

1.  Välj **Mått**.
2.  Välj **resursen** (lagrings kontot).
3.  Välj **namn området**
4.  Välj **mått**.
5.  Välj typ av **agg regering**
6.  Du kan fästa den här vyn på instrument panelen.

## <a name="observing-bottlenecks"></a>Att iaktta Flask halsar

När vi har slutfört den inledande installations processen för nödvändiga mått, och post aktiverar diagnostik för virtuell dator och relaterat lagrings konto, kan vi flytta till analys fasen.

### <a name="accessing-the-monitoring"></a>Åtkomst till övervakning

Välj den virtuella Azure-dator som du vill undersöka och välj **övervakning**.

![Välj övervakning](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Observations linjer

Granska dina data för att identifiera om du har några resurs Flask halsar. Om du upptäcker att datorn har körts bra, men den har rapporter ATS att prestandan nyligen försämras, kan du granska ett tidsintervall med data som omfattar prestanda mått data innan de rapporterade ändringarna, under och efter problemet.

### <a name="check-for-cpu-bottleneck"></a>Sök efter processor Flask hals

![Sök efter processor Flask hals](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Redigera grafen.
2. Ange tidsintervallet.
3. Du måste lägga till i räknaren: processor procent gäst operativ system
4. Spara.

### <a name="cpu-observe-trends"></a>Trender för CPU-observation

När du tittar på prestanda problem bör du vara medveten om trender och förstå om de påverkar dig. I nästa avsnitt ska vi använda övervaknings diagrammen från portalen för att visa trender. De kan också vara användbara för att referera till kors referenser till resurs beteenden i samma tids period. Om du vill anpassa graferna klickar du på [Azure Monitor data plattform](../../azure-monitor/data-platform.md).

Toppar – toppar kan relateras till en schemalagd aktivitet/känd händelse. Om du kan identifiera uppgiften bestämmer du om aktiviteten körs på den prestanda nivå som krävs. Om prestanda är acceptabel kanske du inte behöver öka resurserna.

Insamling och konstant – ofta indikerar en ny arbets belastning. Om det inte är en känd arbets belastning aktiverar du övervakning i den virtuella datorn för att ta reda på vilken process (eller vilka processer) som orsakar beteendet. När processen har identifierats kontrollerar du om den ökade förbrukningen orsakas av ineffektiv kod eller normal förbrukning. Om du använder normal förbrukning bestämmer du om processen fungerar på den prestanda nivå som krävs.

Konstant – Bestäm om den virtuella datorn alltid har körts på den här nivån eller om den bara har körts på den nivån sedan diagnostiken har Aktiver ATS. I så fall, identifiera processen (eller processerna) som orsakar problemet och Överväg att lägga till mer av resursen.

Ständigt ökande – en konstant ökning av förbrukningen är ofta en ineffektiv kod eller en process som tar på fler användar arbets belastningar.

### <a name="high-cpu-utilization-remediation"></a>Hög processor användnings reparation

Om programmet eller processen inte körs på rätt prestanda nivå och du ser 95% + konstant processor belastning kan du utföra någon av följande uppgifter:

* För omedelbar reducering – öka storleken på den virtuella datorn till en storlek med fler kärnor
* Förstå problemet – hitta program/process och Felsök detta.

Om du har ökat den virtuella datorn och processorn fortfarande kör 95% avgör du om den här inställningen erbjuder bättre prestanda eller högre program data flöde till en acceptabel nivå. Annars kan du felsöka det enskilda application\process.

Du kan använda Perfinsights för [Windows](./how-to-use-perfinsights.md) eller [Linux](./how-to-use-perfinsights-linux.md) för att analysera vilken process som kör CPU-förbrukningen. 

## <a name="check-for-memory-bottleneck"></a>Sök efter minnes Flask hals

Så här visar du måtten:

1. Lägg till ett avsnitt.
2. Lägg till en panel.
3. Öppna galleriet.
4. Välj minnes användning och dra. När panelen är dockad högerklickar du och väljer **6x4**.

### <a name="memory-observe-trends"></a>Trender för minnes observationer

Minnes användningen visar hur mycket minne som förbrukas med den virtuella datorn. Förstå trenden och om den mappar till den tidpunkt då du ser problem. Du bör alltid ha mer än 100 MB tillgängligt minne.

Insamling och konstant/konstant stadig konsumtion – hög minnes användning kan inte vara orsaken till dåliga prestanda, eftersom vissa program, till exempel Relations databas motorer tilldelar en stor mängd minne, och den här användningen kanske inte är signifikant. Men om det finns flera minnes krävande program kan det hända att du får dåliga prestanda från minnes konkurrens genom att orsaka trimning och växling/växling till disk. Den här dåliga prestandan är ofta en märkbar orsak till program prestanda påverkan.

Att öka konsumtionen ständigt – ett möjligt program som är igång, är den här förbrukningen vanligt bland databas motorer som startar. Det kan dock också vara ett tecken på en minnesläcka i ett program. Identifiera programmet och förstå om beteendet förväntas.

Användning av växlings fil eller växlings fil – kontrol lera om du använder växlings filen för Windows (finns på D: \) eller Linux-växlings filen (finns på `/dev/sdb` ) används mycket. Om du inte har något på dessa volymer förutom de här filerna kan du söka efter hög läsning/skrivning på dessa diskar. Det här problemet är en indikation på låga minnes förhållanden.

### <a name="high-memory-utilization-remediation"></a>Hög minnes användnings reparation

Utför någon av följande uppgifter för att lösa hög minnes användning:

* För omedelbar reducering eller växling av växlings fil, öka storleken på den virtuella datorn till ett med mer minne och övervaka sedan.
* Förstå problem – hitta program/processer och Felsök för att identifiera hög förbrukning av minnes program.
* Om du känner till programmet kan du se om minnesallokering kan vara ett tak.

Om du när du har uppgraderat till en större virtuell dator upptäcker du att du fortfarande har en konstant stadig ökning fram till 100%, identifierar programmet/processen och felsöker.

Du kan använda Perfinsights för [Windows](./how-to-use-perfinsights.md) eller [Linux](./how-to-use-perfinsights-linux.md) för att analysera vilken process som kör minnes användningen. 

## <a name="check-for-disk-bottleneck"></a>Sök efter disk Flask hals

Om du vill kontrol lera underlag rings systemet för den virtuella datorn kontrollerar du diagnostiken på nivån för virtuella Azure-datorer med räknarna i VM-diagnostik och även lagrings kontots diagnostik.

I fel sökning av virtuell dator kan du använda Perfinsights för [Windows](./how-to-use-perfinsights.md) eller [Linux](./how-to-use-perfinsights-linux.md), vilket kan hjälpa till att analysera vilken process som kör i/o. 

Observera att vi inte har räknare för zonens redundanta och Premium Storage konton. För problem som rör dessa räknare ska du generera ett support ärende.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Visa lagrings kontots diagnostik i övervakning

Om du vill arbeta med nedanstående objekt går du till lagrings kontot för den virtuella datorn i portalen:

![Visa lagrings kontots diagnostik i övervakning](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Redigera övervaknings diagrammet.
2. Ange tidsintervallet.
3. Lägg till de räknare som beskrivs i stegen nedan.
4. Spara ändringarna.

### <a name="disk-observe-trends-standard-storage-only"></a>Trender för disk observationer (endast standard lagring)

Du kan identifiera problem med lagringen genom att titta på prestanda måtten från lagrings kontots diagnostik och VM-diagnostik.

För varje kontroll nedan kan du söka efter viktiga trender när problemen inträffar inom det här ärende intervallet.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Kontrol lera tillgänglighet för Azure Storage – Lägg till lagrings kontots mått: tillgänglighet

Om du ser en minskning av tillgängligheten kan det uppstå ett problem med plattformen, kontrol lera [Azure-status](https://azure.microsoft.com/status/). Om inget problem visas där kan du generera en ny supportbegäran.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Sök efter timeout för Azure Storage – Lägg till lagrings konto måtten:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Värden i * TimeOutError-mått indikerar att en IO-åtgärd tog för lång tid och tids gränsen nåddes. Att gå igenom nästa steg hjälper dig att identifiera potentiella orsaker.

AverageServerLatency ökar samtidigt som TimeOutErrors kan vara ett plattforms problem. Utlös en ny supportbegäran i den här situationen.

AverageE2ELatency representerar klientens svars tid. Kontrol lera hur IOPS utförs av programmet. Leta efter en ökning eller ständigt hög TotalRequests mått. Måttet representerar IOPS. Om du börjar nå gränserna för lagrings kontot eller en enskild virtuell hård disk kan svars tiden vara relaterad till begränsning.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Sök efter Azure Storage-begränsning – Lägg till lagrings konto måtten: ThrottlingError

Värden för begränsning anger att du är begränsad till lagrings konto nivå, vilket innebär att du påträffar kontots IOPS-gräns. Du kan avgöra om du träffar IOPs-tröskelvärdet genom att kontrol lera måttet **TotalRequests**.

Observera att varje virtuell hård disk har en gräns på 500 IOPS eller 60 Mbit, men är kopplad till den kumulativa gränsen på 20000 IOPS per lagrings konto.

Med det här måttet kan du inte se vilken blob som orsakar begränsningen och som påverkas av den. Du kan dock antingen trycka på IOPS-eller ingångs-eller utgångs gränser för lagrings kontot.

Om du vill ta reda på om du påträffar IOPS-gränsen går du till lagrings kontots diagnostik och kontrollerar TotalRequests och ser om du närmar dig 20000 TotalRequests. Identifiera antingen en ändring i mönstret, oavsett om du ser gränsen för första gången eller om den här gränsen inträffar vid en viss tidpunkt.

Med nya disk erbjudanden under standard lagring kan IOPS-och data flödes gränserna variera, men den kumulativa gränsen för standard lagrings kontot är 20000 IOPS (Premium Storage har olika gränser på konto-eller disk nivå). Läs mer om de olika disk gränserna för standard lagring och disk utrymme:

* [Skalbarhets-och prestanda mål för virtuella dator diskar i Windows](../disks-scalability-targets.md).

#### <a name="references"></a>Referenser

* [Skalbarhets-och prestanda mål för Premium Page Blob Storage-konton](../../storage/blobs/scalability-targets-premium-page-blobs.md)

Lagrings kontots bandbredd mäts av lagrings konto måtten: total ingress och TotalEgress. Du har olika tröskelvärden för bandbredd beroende på typen av redundans och regioner.

* [Skalbarhets-och prestanda mål för standard lagrings konton](../../storage/common/scalability-targets-standard-account.md)

Kontrol lera total ingress och TotalEgress mot ingångs-och utgående gränser för lagrings kontots redundans typ och region.

Kontrol lera data flödes gränserna för de virtuella hård diskar som är anslutna till den virtuella datorn Lägg till den virtuella datorns mått disk Läs och skriv.

Nya disk erbjudanden under standard lagring har olika IOPS-och data flödes gränser (IOPS exponeras inte per VHD). Titta på data för att se om du är klar med gränserna för kombinerat data flöde MB på den virtuella hård disken på VM-nivå med Läs-och skriv åtgärder, och optimera sedan din VM Storage-konfiguration för att skala de tidigare enskilda VHD-gränserna. Läs mer om de olika disk gränserna för standard lagring och disk utrymme:

* [Skalbarhets-och prestanda mål för virtuella dator diskar i Windows](../disks-scalability-targets.md).

### <a name="high-disk-utilizationlatency-remediation"></a>Hög disk användning/latens reparation

Minska klientens fördröjning och optimera VM-IO för att skala de tidigare VHD-gränserna

* [Optimera IO för Windows i Azure](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md?toc=/azure/virtual-machines/windows/toc.json)

* [Optimera IO för Linux i Azure](/archive/blogs/igorpag/azure-storage-secrets-and-linux-io-optimizations)

#### <a name="reduce-throttling"></a>Minska begränsningen

Om du träffar i de övre gränserna för lagrings konton kan du balansera om de virtuella hård diskarna mellan lagrings konton. Se [Azure Storage skalbarhets-och prestanda mål](../../storage/common/scalability-targets-standard-account.md).

### <a name="increase-throughput-and-reduce-latency"></a>Öka data flödet och minska svars tiden

Om du har ett svars tids känsligt program och kräver högt data flöde migrerar du dina virtuella hård diskar till Azure Premium Storage med hjälp av den virtuella datorn DS och GS serien

De här artiklarna beskriver de olika scenarierna:

* [Migrera till Azure Premium Storage](../windows/migrate-to-managed-disks.md)

* [Använda Azure Premium Storage med SQL Server](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-premium-storage)

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/).

Du kan också fil en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
