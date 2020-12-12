---
title: HDInsight 4,0 – översikt – Azure
description: Jämför HDInsight 3.6 med HDInsight 4.0 när det gäller funktioner, begränsningar och uppgraderingsrekommendationer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: b8f9759d19089f74d62def41b205d862ce9a7d43
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359735"
---
# <a name="azure-hdinsight-40-overview"></a>Översikt över Azure HDInsight 4,0

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för Apache Hadoop och Apache Spark. HDInsight 4,0 är en moln distribution av Apache Hadoop-komponenter. Den här artikeln innehåller information om den senaste versionen av Azure HDInsight och hur du uppgraderar.

## <a name="whats-new-in-hdinsight-40"></a>Vad är nytt i HDInsight 4,0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3,0 och analytisk bearbetning i låg latens

Apache Hive (analys av låg latens) använder beständiga fråge servrar och minnes intern cachelagring. Den här processen levererar snabba SQL-frågeresultat om data i Fjärrlagring av molnet. Hive-LLAP använder en uppsättning bestående daemonar som kör fragment av Hive-frågor. Frågekörningen på LLAP liknar Hive utan LLAP, med arbetaruppgifter som körs i LLAP-daemons i stället för containrar.

Fördelar med Hive LLAP:

* Möjlighet att göra djup SQL-analys utan att offra prestanda och anpassning. Till exempel komplexa kopplingar, under frågor, fönster funktioner, sortering, användardefinierade funktioner och komplexa agg regeringar.

* Du kan köra interaktiva frågor mot data i samma lagring som data förbereds i. Det gör att data inte behöver flyttas från lagringen till en annan motor för analysbearbetning.

* Genom att cachelagra frågeresultat kan tidigare beräknade frågeresultat återanvändas. I den här cachen sparas tid och resurser som krävs för att köra de kluster uppgifter som krävs för frågan.

### <a name="hive-dynamic-materialized-views"></a>Dynamiska materialiserade vyer i Hive

Hive stöder nu dynamiska, materialiserade vyer eller för beräkning av relevanta sammanfattningar. Vyerna påskyndar bearbetningen av frågor i informations lager. Materialiserade vyer kan lagras internt i Hive, och kan använda LLAP-acceleration sömlöst.

### <a name="hive-transactional-tables"></a>Transaktionstabeller i Hive

HDI 4,0 innehåller Apache Hive 3. Hive 3 kräver Atomicitet, konsekvens, isolering och livs längd för transaktionella tabeller som är aktiva i Hive-lagret. ACID-kompatibla tabeller och tabelldata nås och hanteras av Hive. Data i Create-, Hämta-, Update-och Delete (CRUD)-tabeller måste vara i fil formatet optimerad rad kolumn (ORC). Tabeller med endast infogning stöder alla fil format. 

> [!Note]
> Stöd för syra/transaktionella fungerar endast för hanterade tabeller och inte för externa tabeller. Hive externa tabeller är utformade så att externa parter kan läsa och skriva tabell data, utan Hive perfoming eventuella ändringar av underliggande data. I sur tabeller kan Hive ändra underliggande data med komprimering och transaktioner.

Några av fördelarna med sur tabeller är följande:

* ACID v2 har prestandaförbättringar både för lagringsformat och för körningsmotorn.

* ACID är aktiverat som standard för att ge fullständigt stöd för datauppdateringar.

* Förbättrade ACID-funktioner innebär att du kan göra uppdateringar och borttagningar på radnivå.

* Inga kostnader för prestanda.

* Ingen bucketgruppering krävs.

* Spark kan läsa och skriva till Hive ACID-tabeller via Hive Warehouse-anslutningsappen.

Läs mer om [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark hämtar uppdaterbara tabeller och ACID-transaktioner med Hive Warehouse-anslutningsappen. Med Hive Warehouse-anslutningsappen kan du registrera Hive-transaktionstabeller som externa tabeller i Spark för att få tillgång till fullständiga transaktionsfunktioner. Tidigare versioner hade endast stöd för manipulering av tabellpartitioner. Hive-lager Connector stöder också strömning av DataFrames.  Den här processen strömmar läser och skriver till transaktions-och strömmande Hive-tabeller från Spark.

Spark-utförare kan ansluta direkt till Hive LLAP-daemons för att hämta och uppdatera data transaktionsmässigt, vilket låter Hive behålla kontrollen över data.

Med Apache Spark i HDInsight 4.0 kan du:

* köra träning av maskininlärningsmodeller över samma transaktionstabell som används för rapportering
* använda ACID-transaktioner för att lägga till kolumner från Spark ML i en Hive-tabell på ett säkert sätt
* köra ett Spark-strömningsjobb på ändringsflödet från en Hive-strömningstabell
* skapa ORC-filer direkt från ett strukturerat Spark-strömningsjobb.

Du behöver inte längre bekymra dig om oavsiktlig försök att komma åt Hive-transaktionella tabeller direkt från Spark. Resulterar i inkonsekventa resultat, duplicerade data eller skadade data. I HDInsight 4,0 behålls Spark-tabeller och Hive-tabeller i separata Metastores. Använd anslutningsappen för Hive-informationslager för att registrera Hive-transaktionstabeller som externa Spark-tabeller.

Läs mer om [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 ingår i HDI 4.0 med följande ändringar:

* Oozie kör inte längre Hive-åtgärder. Hive CLI har tagits bort och ersatts med BeeLine.

* Du kan exkludera oönskade beroenden från ”share lib” genom att ta med ett exkluderingsmönster i din **job.properties**-fil.

Läs mer om [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Så här uppgraderar du till HDInsight 4,0

Testa dina komponenter noggrant innan du implementerar den senaste versionen i en produktions miljö. HDInsight 4,0 är tillgängligt för att du ska kunna starta uppgraderings processen. HDInsight 3,6 är standard alternativet för att förhindra oavsiktlig mishaps.

Det finns ingen uppgraderings väg som stöds från tidigare versioner av HDInsight till HDInsight 4,0. Eftersom Metaarkiv-och blob-dataformat har ändrats är 4,0 inte kompatibelt med tidigare versioner. Det är viktigt att du behåller din nya HDInsight 4,0-miljö separat från din aktuella produktions miljö. Om du distribuerar HDInsight 4,0 till din aktuella miljö, kommer din Metaarkiv att uppgraderas permanent.  

## <a name="limitations"></a>Begränsningar

* HDInsight 4,0 har inte stöd för MapReduce för Apache Hive. Använd Apache Tez i stället. Läs mer om [Apache Tez](https://tez.apache.org/).
* HDInsight 4,0 har inte stöd för Apache Storm.
* HDInsight 4,0 har inte stöd för kluster typen ML-tjänster.
* Hive-vyn är bara tillgänglig i HDInsight 4,0-kluster med ett versions nummer som är lika med eller större än 4,1. Det här versions numret är tillgängligt i Ambari admin->-versioner.
* Shell-tolken i Apache Zeppelin stöds inte i Spark-och Interactive Query-kluster.
* Det går inte att *inaktivera* LLAP i ett Spark-LLAP-kluster. Du kan bara inaktivera LLAP.
* Azure Data Lake Storage Gen2 kan inte spara Jupyter-anteckningsböcker i ett Spark-kluster.
* Apache gris körs på Tez som standard, men du kan ändra det till MapReduce
* Spark SQL Ranger-integrering för rad-och kolumn säkerhet är föråldrad
* Spark 2,4 och Kafka 2,1 är tillgängliga i HDInsight 4,0, så Spark 2,3 och Kafka 1,1 stöds inte längre. Vi rekommenderar att du använder Spark 2,4 & Kafka 2,1 och senare i HDInsight 4,0.

## <a name="next-steps"></a>Nästa steg

* [Guide för HBase-migrering](./hbase/apache-hbase-migrate-new-version.md)
* [Guide för Hive-migrering](./interactive-query/apache-hive-migrate-workloads.md)
* [Guide för Kafka-migrering](./kafka/migrate-versions.md)
* [Guide för Spark-migrering](./spark/migrate-versions.md)
* [Dokumentation om Azure HDInsight](index.yml)
* [Viktig information](hdinsight-release-notes.md)
