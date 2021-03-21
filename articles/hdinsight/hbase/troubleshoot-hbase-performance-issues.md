---
title: Felsöka problem med Apache HBase-prestanda Azure HDInsight
description: Olika rikt linjer för prestanda justering av Apache HBase och tips för att få optimala prestanda i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942894"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Felsöka problem med Apache HBase-prestanda Azure HDInsight

I den här artikeln beskrivs olika rikt linjer för prestanda justering av Apache-HBase och tips för att få optimala prestanda i Azure HDInsight. Många av de här tipsen beror på arbets belastningen och Läs-/skriv-/skannings mönstret. Testa dem noggrant innan du tillämpar konfigurations ändringar i en produktions miljö.

## <a name="hbase-performance-insights"></a>HBase Performance Insights

Den övre Flask halsen i de flesta HBase-arbetsbelastningar är Skriv loggs loggen (WAL). Det påverkar kraftigt skriv prestanda. HDInsight-HBase har en avgränsad lagrings beräknings modell. Data lagras på distans i Azure Storage, även om virtuella datorer är värdar för region servrar. Tills det nyligen skrevs WAL skrevs även till Azure Storage. I HDInsight förstärkte detta beteende denna Flask hals. Funktionen [accelererade skrivningar](./apache-hbase-accelerated-writes.md) är utformad för att lösa det här problemet. Den skriver WAL till Azure Premium SSD-hanterade diskar. Det här fantastiska fördelarna med att skriva prestanda och det hjälper många problem med vissa av de Skriv intensiva arbets belastningarna.

Om du vill få en betydande förbättring av Läs åtgärder använder du [Premium Block Blob Storage-konto](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) som Fjärrlagring. Det här alternativet är bara möjligt om funktionen WAL är aktive rad.

## <a name="compaction"></a>Komprimerings

Komprimering är en annan potentiell Flask hals som huvudsakligen överenskommits i communityn. Som standard är stor komprimering inaktiverat i HDInsight HBase-kluster. Komprimeringen är inaktive rad, även om det är en resurs intensiv process, har kunderna fullständig flexibilitet för att schemalägga den enligt deras arbets belastning. De kan till exempel schemalägga dem under låg belastnings tider. Det är inte heller något problem med data-platsen eftersom vår lagrings plats är fjärr (backas upp av Azure Storage) i stället för till en lokal Hadoop Distributed File System (HDFS).

Kunderna bör schemalägga stor komprimering vid deras bekvämlighet. Om de inte gör det här underhållet kommer komprimeringen att påverka prestandan i den långa körningen negativt.

För skannings åtgärder bör fördröjningar som är mycket högre än 100 millisekunder vara en anledning till betänkligheter. Kontrol lera om större komprimering har schemalagts korrekt.

## <a name="apache-phoenix-workload"></a>Apache Phoenix arbets belastning

Genom att besvara följande frågor får du hjälp att förstå din Apache Phoenix arbets belastning bättre:

* Översätts alla dina "läspaket"-översättning till genomsökningar?
    * I så fall, vilka är egenskaperna för dessa genomsökningar?
    * Har du optimerat ditt Phoenix Table-schema för de här genomsökningarna, inklusive lämplig indexering?
* Har du använt `EXPLAIN` instruktionen för att förstå frågan planerar "läsningar"-genereringen?
* Är dina skrivningar "upsert-väljer"?
    * I så fall kan de också göra genomsökningar. Förväntad svars tid för skanningar genomsnitt cirka 100 millisekunder, jämfört med 10 millisekunder för punkt får i HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Test metodik och mått övervakning

Om du använder benchmarks, till exempel Yahoo! Molnet som betjänar benchmark, JMeter eller Pherf för att testa och justera prestandan, se till att:

- Klient datorerna blir ingen Flask hals. Det gör du genom att kontrol lera CPU-användningen på klient datorer.

- Konfigurationer på klient sidan, t. ex. antalet trådar, justeras på lämpligt sätt för att fylla-klientens bandbredd.

- Test resultaten registreras korrekt och systematiskt.

Om dina frågor plötsligt börjar göra mycket sämre än tidigare, kan du söka efter potentiella buggar i program koden. Genererar den plötsligt stora mängder ogiltiga data? Om så är fallet kan den öka fördröjningen för läsning.

## <a name="migration-issues"></a>Migreringsproblem

Om du migrerar till Azure HDInsight bör du se till att migreringen utförs systematiskt och korrekt, helst via automatisering. Undvik manuell migrering. Se till att:

- Tabellattribut migreras korrekt. Attribut kan inkludera som komprimering, blomma filter och så vidare.

- Inställningarna för saltning i Phoenix-tabellerna mappas korrekt till den nya kluster storleken. Till exempel bör antalet salt buckets vara en multipel av antalet arbetsnoder i klustret. Och du bör använda en multipel som är faktor för mängden frekventa upptäcka.

## <a name="server-side-configuration-tunings"></a>Konfigurations justeringar på Server Sidan

I HDInsight-HBase lagras HFiles på Fjärrlagring. När det finns ett cache-missar är kostnaden för läsningar högre än lokala system eftersom data på lokala system backas upp av Local HDFS. I de flesta fall är intelligent användning av HBase-cache (block-cache och Bucket cache) utformad för att kringgå det här problemet. I de fall där problemet inte kringgås kan det här problemet uppstå om du använder ett Premium Block Blob-konto. Windows Azure Storage Blob-drivrutinen förlitar sig på vissa egenskaper, t. ex. `fs.azure.read.request.size` för att hämta data i block baserat på vad det bestämmer sig för att vara läsnings läge (sekventiella kontra slumpmässiga), så det kan fortsätta att vara instanser av högre latens med läsningar. Med empiriska experiment har vi funnit att ställa in block storleken för läsnings begär Anden ( `fs.azure.read.request.size` ) till 512 KB och matcha block storleken för HBase-tabellerna så att de får samma storlek som ger bäst resultat i praktiken.

För de flesta kluster med stora storleks noder tillhandahåller HDInsight-HBase `bucketcache` som en fil på en lokal Premium SSD som är kopplad till den virtuella datorn, som körs `regionservers` . Att använda cache utanför heap i stället kan ge en viss förbättring. Den här lösningen har begränsningen att använda tillgängligt minne och kan vara mindre än filbaserad cache, så det är inte alltid det bästa valet.

Följande är några av de andra speciella parametrarna som vi har justerat, och som verkade hjälpa till med varierande grader:

- Öka `memstore` storleken från standard 128 MB till 256 MB. Normalt rekommenderas den här inställningen för tunga Skriv scenarier.

- Öka antalet trådar som är dedikerade för komprimering, från standardinställningen **1** till **4**. Den här inställningen är relevant om vi iakttar frekventa smärre komprimeringar.

- Undvik att blockera `memstore` tömning på grund av en lagrings gräns. Öka `Hbase.hstore.blockingStoreFiles` inställningen till **100** för att tillhandahålla den här bufferten.

- Använd följande inställningar om du vill kontrol lera tömningar:

    - `Hbase.regionserver.maxlogs`: **140** (förhindrar tömningar på grund av Wal-gränser)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Phoenix-/regionsspecifika konfigurationer för inställning av trådpoolen:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Andra Phoenix-/regionsspecifika konfigurationer:

    - `Phoenix.rpc.index.handler.count`: **50** (om det finns stora eller många indexs ökningar)

    - `Phoenix.stats.updateFrequency`: **1 timme**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 timme**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC-tidsgräns: **3 minuter**

   - RPC-timeout inkluderar HBase RPC-timeout, timeout för klient skanner och Phoenix-tidsgräns. 
   - Kontrol lera att `hbase.client.scanner.caching` parametern har angetts till samma värde både på Server sidan och klienten slutar. Om de inte är samma, leder den här inställningen till klient slut fel som är relaterade till `OutOfOrderScannerException` . Den här inställningen ska vara inställd på ett lågt värde för stora genomsökningar. Vi anger värdet **100**.

## <a name="other-considerations"></a>Ytterligare överväganden

Följande är ytterligare parametrar för att överväga fin justering:

- `Hbase.rs.cacheblocksonwrite` – som standard på HDI är den här inställningen inställd på **True**.

- Inställningar som gör det möjligt att skjuta upp mindre komprimering för senare.

- Experimentella inställningar, till exempel justera procent andelar av köer som är reserverade för läsnings-och skriv förfrågningar.

## <a name="next-steps"></a>Nästa steg

Om problemet inte är löst kan du gå till någon av följande kanaler för mer information:

- Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

- Anslut till [@AzureSupport](https://twitter.com/azuresupport) . Detta är det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Den ansluter Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Din Microsoft Azure prenumeration innehåller åtkomst till prenumerations hantering och fakturerings support, och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).