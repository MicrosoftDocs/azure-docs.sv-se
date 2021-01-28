---
title: Använda Apache Hive replikering i Azure HDInsight-kluster
description: Lär dig hur du använder Hive-replikering i HDInsight-kluster för att replikera Hive-metaarkiv och Azure Data Lake Storage gen 2 data Lake.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 3dd894a46b666703f64e44336c0bf022da8a063a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941335"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Använda Apache Hive replikering i Azure HDInsight-kluster

I samband med databaser och lager, är replikering processen att duplicera entiteter från ett lager till ett annat. Duplicering kan tillämpas på en hel databas eller på en mindre nivå, till exempel en tabell eller partition. Målet är att ha en replik som ändras när den grundläggande entiteten ändras. Replikering på Apache Hive fokuserar på haveri beredskap och erbjuder enkelriktad primär kopierings replikering. I HDInsight-kluster kan Hive-replikering användas för att på ett enkelriktat replikera Hive-metaarkiv och den associerade underliggande data Lake på Azure Data Lake Storage Gen2.  

Hive-replikering har utvecklats under åren med nyare versioner som ger bättre funktionalitet och är snabbare och mindre resurs krävande. I den här artikeln diskuterar vi Hive-replikering (Replv2) som stöds i båda kluster typerna HDInsight 3,6 och HDInsight 4,0.

## <a name="advantages-of-replv2"></a>Fördelar med Replv2

[Hive-ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) eller (Replv2) har följande fördelar jämfört med den första versionen av Hive-replikering som använde Hive [import-export](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport):

- Händelse-baserad stegvis replikering
- Replikering vid tidpunkt  
- Lägre krav för bandbredd  
- Minskning av antalet mellanliggande kopior  
- Replikeringstillståndet upprätthålls
- Begränsad replikering  
- Stöd för en nav-och eker-modell  
- Stöd för sur tabeller (i HDInsight 4,0)

## <a name="replication-phases"></a>Steg för replikering

Den händelsebaserade Hive-replikeringen konfigureras mellan de primära och sekundära klustren. Den här replikeringen består av två distinkta faser: Start och stegvisa körningar.

### <a name="bootstrapping"></a>Start

Start är avsedd att köras en gång för att replikera databasens grund tillstånd från primärt till sekundärt. Du kan konfigurera start vid behov för att inkludera en delmängd av de tabeller i mål databasen där replikering måste aktive ras.

### <a name="incremental-runs"></a>Stegvisa körningar

Efter start är de stegvisa körningarna automatiserade på det primära klustret och händelser som genereras under dessa stegvisa körningar spelas upp på det sekundära klustret. När det sekundära klustret fångar upp med det primära klustret, blir den sekundära konsekvent med huvudets händelser.

## <a name="replication-commands"></a>Kommandon för replikering

Hive innehåller en uppsättning REPL-kommandon – `DUMP` , `LOAD` och `STATUS` -för att dirigera händelse flödet. `DUMP`Kommandot genererar en lokal logg över alla DDL/DML-händelser på det primära klustret. `LOAD`Kommandot är en metod för att Lazy Kopiera metadata och data som loggas till utdata från den extraherade replikeringen och körs i mål klustret. `STATUS`Kommandot körs från mål klustret för att ange det senaste händelse-ID som den senaste replikeringen har repliker ATS till.

### <a name="set-replication-source"></a>Ange källa för replikering

Innan du börjar med replikering måste du se till att den databas som ska replikeras anges som replikeringskälla. Du kan använda `DESC DATABASE EXTENDED <db_name>` kommandot för att avgöra om parametern `repl.source.for` har angetts med namnet på principen.

Om principen är schemalagd och `repl.source.for` parametern inte har angetts måste du först ange den här parametern med hjälp av `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` .

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Dumpa metadata till data Lake

`REPL DUMP [database name]. => location / event_id`Kommandot används i bootstrap-fasen för att dumpa relevanta metadata till Azure Data Lake Storage Gen2. `event_id`Anger den minsta händelse som relevanta metadata har lagts till i Azure Data Lake Storage Gen2. 
 
```sql
repl dump tpcds_orc; 
```
Exempel på utdata:

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>Läs in data i mål klustret

`REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }`Kommandot används för att läsa in data i mål klustret både för start och stegvis replikering. `[database name]`Kan vara samma som källan eller ett annat namn i mål klustret. `[location]`Representerar platsen från utdata från tidigare `REPL DUMP` kommando. Det innebär att mål klustret ska kunna kommunicera med käll klustret. `WITH`-Satsen lades i första hand för att förhindra att mål klustret startas om, vilket möjliggör replikering.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>Utdata från senaste replikerade händelse-ID

`REPL STATUS [database name]`Kommandot körs på mål kluster och ger den senaste replikerade informationen `event_id` . Kommandot gör det också möjligt för användarna att veta vilket tillstånd deras mål kluster har repliker ATS till. Du kan använda utdata från det här kommandot för att skapa nästa `REPL DUMP` kommando för stegvis replikering.

```sql
repl status tpcds_orc;
```

Exempel på utdata:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>Dumpa relevanta data och metadata till data Lake

`REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }`Kommandot används för att dumpa relevanta metadata och data till Azure Data Lake Storage. Det här kommandot används i den stegvisa fasen och körs på käll lagret. `FROM [event-id]`Krävs för den stegvisa fasen och värdet `event-id` kan härledas genom att köra `REPL STATUS [database name]` kommandot på mål lagret.

```sql
repl dump tpcds_orc from 2925;
```

Exempel på utdata:

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Process för Hive-replikering

Följande steg är de sekventiella händelser som inträffar under Hive-replikeringen.

1. Kontrol lera att tabellerna som ska replikeras anges som replikeringskälla för en viss princip.

1. `REPL_DUMP`Kommandot utfärdas till det primära klustret med associerade begränsningar som databas namn, händelse-ID-intervall och Azure Data Lake Storage Gen2 lagrings-URL.

1. Systemet serialiserar en dumpning av alla spårade händelser från metaarkiv till den senaste. Den här dumpningen lagras i Azure Data Lake Storage Gen2 lagrings konto på det primära klustret på den URL som anges av `REPL_DUMP` .  

1. Det primära klustret behåller metadata för replikeringen till det primära klustrets Azure Data Lake Storage Gen2 lagring. Sökvägen kan konfigureras i konfigurations gränssnittet för Hive i Ambari. Processen tillhandahåller sökvägen där metadata lagras och ID: t för den senaste spårade DML/DDL-händelsen.

1. `REPL_LOAD`Kommandot utfärdas från det sekundära klustret. Kommandot pekar på den sökväg som konfigurerades i steg 3.

1. Det sekundära klustret läser metadatafilen med spårade händelser som skapades i steg 3. Se till att det sekundära klustret har nätverks anslutning till Azure Data Lake Storage Gen2 lagring av det primära klustret där spårade händelser från `REPL_DUMP` lagras.  

1. Det sekundära klustret skapar Distributed Copy ( `DistCP` )-beräkning.

1. Det sekundära klustret kopierar data från det primära klustrets lagrings utrymme.  

1. Metaarkiv på det sekundära klustret uppdateras.  

1. Det senaste spårade händelse-ID: t lagras i den primära metaarkiv.

Stegvis replikering följer samma process och kräver det sista replikerade händelse-ID: t som indatamängd. Detta leder till en stegvis kopia sedan händelsen senaste replikering. Stegvisa replikeringar automatiseras vanligt vis med en fördefinierad frekvens för att uppnå nödvändiga återställnings punkt mål.

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Hive-replikeringsschema":::

## <a name="replication-patterns"></a>Replik mönster  

Replikering konfigureras vanligt vis på ett enkelriktat sätt mellan den primära och den sekundära, där den primära består för läsnings-och skriv förfrågningar. Det sekundära klustret tillgodoser endast Läs begär Anden. Skrivningar tillåts på den sekundära om det finns en katastrof, men omvänd replikering måste konfigureras tillbaka till den primära.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Mönster för Hive-replikering":::

Det finns många mönster som lämpar sig för Hive-replikering, inklusive primär – sekundär, hubb och eker och relä.

I HDInsight Active Primary – sekundär vänte läge är ett gemensamt mönster för affärs kontinuitet och haveri beredskap (BCDR) och HiveReplicationV2 kan använda det här mönstret med regionalt åtskilda HDInsight Hadoop-kluster med VNet-peering. En gemensam virtuell dator som peer-kopplas till båda klustren kan användas som värd för automatiserings skripten för replikering. Mer information om möjliga HDInsight BCDR-mönster finns i [dokumentationen för HDInsight-affärs kontinuitet](../hdinsight-business-continuity.md).  

### <a name="hive-replication-with-enterprise-security-package"></a>Hive-replikering med Enterprise Security Package  

I de fall där Hive-replikering planeras på HDInsight Hadoop-kluster med Enterprise Security Package, måste du räkna med replikeringstrafik för Ranger metaarkiv och Azure Active Directory Domain Services (AD DS).  

Använd funktionen Azure AD DS-repliker för att skapa mer än en Azure AD DS-replik uppsättning per Azure AD-klient i flera regioner. Varje enskild replik uppsättning måste vara peer-kopplad med HDInsight-virtuella nätverk i sina respektive regioner. I den här konfigurationen ändras ändringar av Azure AD DS, inklusive konfiguration, användar identitet och autentiseringsuppgifter, grupper, grup princip objekt, dator objekt och andra ändringar som tillämpas på alla replik uppsättningar i den hanterade domänen med Azure AD DS-replikering.
  
Ranger-principer kan regelbundet säkerhets kopie ras och replikeras från den primära till den sekundära med Ranger Import-Export funktion. Du kan välja att replikera alla eller en delmängd av Ranger-principer beroende på vilken nivå av auktoriseringar du försöker implementera i det sekundära klustret.  

## <a name="sample-code"></a>Exempelkod  

Följande kod serie innehåller ett exempel på hur du kan implementera start och stegvis replikering i en exempel tabell som kallas `tpcds_orc` .  

1. Ange tabellen som källa för en replikeringsprincip.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. Start dumpning vid det primära klustret.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   Exempel på utdata:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. Bootstrap-belastning på det sekundära klustret. 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. Kontrol lera `REPL` status i det sekundära klustret.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. Stegvis dump i det primära klustret.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   Exempel på utdata:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. Stegvis inläsning på ett sekundärt kluster.  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. Kontrol lera `REPL` status på sekundärt kluster.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>Nästa steg

Mer information om de objekt som beskrivs i den här artikeln finns i:

- [Affärs kontinuitet i Azure HDInsight](../hdinsight-business-continuity.md)
- [Azure HDInsight affärs kontinuitets arkitekturer](../hdinsight-business-continuity-architecture.md)
- [Fallstudie av hög tillgänglighet för Azure HDInsight-lösning](../hdinsight-high-availability-case-study.md)
- [Vad är Apache Hive och HiveQL på Azure HDInsight?](../hadoop/hdinsight-use-hive.md)