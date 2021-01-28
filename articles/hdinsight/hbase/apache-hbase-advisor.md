---
title: Optimera för rekommendationer för kluster rådgivare
titleSuffix: Azure HDInsight
description: Optimera Apache HBase för kluster klassificerings rekommendationer i Azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943012"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Apache HBase-rådgivare i Azure HDInsight

I den här artikeln beskrivs flera rekommendationer som hjälper dig att optimera Apache HBase-prestanda i Azure HDInsight. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>Optimera HBase för att läsa de senast skrivna data

Om din UseCase inbegriper läsning av de senaste skrivna data från HBase, kan den här rekommendationen hjälpa dig. För hög prestanda är det optimalt att HBase läsningar ska hanteras från memstores, i stället för Fjärrlagring.

Frågans råd anger att för en given kolumn serie i en tabell > 75% läsningar som hanteras från memstores. Den här indikatorn innebär att även om en tömning sker på memstores, måste den senaste filen vara tillgänglig och att den måste finnas i cache. Data skrivs först för att memstores systemet kommer åt de senaste data där. Det finns en risk att de interna HBase-tömnings trådarna identifierar att en specifik region har nått 128M (standard) och kan utlösa en tömning. Det här scenariot inträffar till och med de senaste data som skrevs när memstores var runt 128M i storlek. En senare läsning av de senaste posterna kan därför kräva en fil läsning i stället för från memstores. Därför är det bäst att optimera att även de senaste data som nyligen har rensats kan finnas i cacheminnet.

Överväg följande konfigurations inställningar för att optimera de senaste data i cachen:

1. Ange `hbase.rs.cacheblocksonwrite` till `true` . Den här standard konfigurationen i HDInsight HBase är `true` , så kontrol lera att den inte återställs till `false` .

2. Öka `hbase.hstore.compactionThreshold` värdet så att du kan undvika att komprimera i. Det här värdet är som standard `3`. Du kan öka det till ett högre värde `10` , t. ex..

3. Om du följer steg 2 och anger compactionThreshold ändrar `hbase.hstore.compaction.max` du till ett högre värde till exempel `100` och ökar även värdet för värdet config `hbase.hstore.blockingStoreFiles` till högre, till exempel `300` .

4. Om du är säker på att du behöver läsa den senaste informationen, ställer du in `hbase.rs.cachecompactedblocksonwrite` konfigurationen på **på**. Den här konfigurationen talar om för systemet att även om komprimeringen sker förblir datan i cache. Konfigurationerna kan ställas in på familje nivå också. 

   I HBase-gränssnittet kör du följande kommando för att konfigurera `hbase.rs.cachecompactedblocksonwrite` konfigurationen:
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. Block-cache kan inaktive ras för en specifik familj i en tabell. Se till att **den är aktive rad för** familjer som har senaste data läsningar. Som standard är block-cache aktiverat för alla familjer i en tabell. Om du har inaktiverat block-cache för en familj och behöver aktivera det använder du kommandot Alter från HBase-gränssnittet.

   Dessa konfigurationer hjälper till att säkerställa att data är tillgängliga i cache och att de senaste data inte genomgår komprimering. Om ett TTL-värde är möjligt i ditt scenario kan du överväga att använda datum nivå komprimering. Mer information finns i [referens hand boken för Apache HBase: datum nivå komprimering](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>Optimera tömnings kön

Detta meddelande anger att HBase-tömningar kan behöva justeras. Den aktuella konfigurationen för tömnings hanterare kanske inte är tillräckligt hög för att hantera med Skriv trafik som kan leda till långsam tömning av tömningar.

I användar gränssnittet för regions servern ser du om rensnings kön växer över 100. Det här tröskelvärdet anger att tömningarna är långsamma och du kan behöva justera   `hbase.hstore.flusher.count` konfigurationen. Som standard är värdet 2. Se till att de maximala tömnings trådarna inte ökar med 6.

Se även om du har en rekommendation för justering av regioner. Om vi ja rekommenderar vi att du provar regions justeringen för att se om det bidrar till snabbare tömningar. Annars kan du med hjälp av justera rensnings trådarna.

## <a name="region-count-tuning"></a>Justering av region antal

Rekommendationen för att justera regioner anger att HBase har blockerat uppdateringar och att regions antalet kan vara mer än den Heap-storlek som stöds bäst. Du kan finjustera Heap-storlek, memstores storlek och regions antalet.

Som exempel scenario:

- Anta att heap-storleken för region servern är 10 GB. Som standard `hbase.hregion.memstore.flush.size` är `128M` . Standardvärdet för `hbase.regionserver.global.memstore.size` är `0.4` . Det innebär att 4 GB allokeras för memstores (Globalt).

- Anta att det finns en jämn fördelning av Skriv belastningen i alla regioner och förutsatt att varje region växer upp till 128 MB. det maximala antalet regioner i den här konfigurationen är `32` regioner. Om en specifik region Server har kon figurer ATS för att ha 32 regioner undviker systemet att blockera uppdateringar.

- När de här inställningarna är på plats är antalet regioner 100. Den globala memstores i 4 GB delas nu över 100 regioner. I praktiken får varje region bara 40 MB för memstores. När skrivningarna är enhetliga, rensas frekvent och mindre storlek i ordningen < 40 MB. Att ha många rensnings trådar kan öka tömnings hastigheten `hbase.hstore.flusher.count` .

Råd givandet innebär att det skulle vara bra att överväga antalet regioner per server, Heap-storlek och global konfiguration av memstores storlek tillsammans med justeringen av tömnings trådar för att undvika att uppdateringar blockeras.

## <a name="compaction-queue-tuning"></a>Justering av kompakta köer

Om HBase-komprimerings kön växer till mer än 2000 och inträffar regelbundet, kan du öka komprimerings trådarna till ett större värde.

Om det finns ett högt antal filer för komprimering kan det leda till mer heap-användning som är relaterad till hur filerna interagerar med Azure-filsystemet. Det är därför bättre att slutföra komprimeringen så snabbt som möjligt. Vissa gånger i äldre kluster kan de komprimerade konfigurationerna som är relaterade till begränsningen leda till långsammare komprimerings frekvens.

Kontrol lera konfigurationerna `hbase.hstore.compaction.throughput.lower.bound` och `hbase.hstore.compaction.throughput.higher.bound` . Om de redan har ställts in på 50 miljoner och 100 miljoner låter du dem vara det. Men om du har konfigurerat inställningarna till ett lägre värde (vilket är fallet med äldre kluster) ändrar du gränserna till 50 miljoner respektive 100 miljoner.

Konfigurationerna är `hbase.regionserver.thread.compaction.small` och `hbase.regionserver.thread.compaction.large` (standardvärdena är 1 var).
Cap Max värdet för den här konfigurationen är mindre än 3.

## <a name="full-table-scan"></a>Fullständig tabells ökning

Den fullständiga tabell genomsökningen visar att över 75% av de utgivna genomsökningarna är fullständig sökning i tabell/region. Du kan gå tillbaka på det sätt som koden anropar genomsökningarna för att förbättra frågans prestanda. Överväg följande metoder:

* Ange rätt start-och stopp rad för varje genomsökning.

* Använd **MultiRowRangeFilter** -API: et så att du kan fråga olika intervall i ett skannings anrop. Mer information finns i [MULTIROWRANGEFILTER API-dokumentation](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* I de fall där du behöver en fullständig genomsökning av en tabell eller region, kontrollerar du om det finns en möjlighet att undvika cache-användning för dessa frågor, så att andra frågor som använder cachen kanske inte tar bort de blockerade blocken. För att säkerställa att genomsökningarna inte använder cache, använder du API: et för **genomsökning** med alternativet **setCaching (false)** i koden: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Nästa steg

[Optimera Apache HBase med Ambari](../optimize-hbase-ambari.md)
