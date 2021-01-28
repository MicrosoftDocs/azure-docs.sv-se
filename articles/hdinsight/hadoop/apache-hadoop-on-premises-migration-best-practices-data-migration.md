---
title: 'Datamigrering: lokala Apache Hadoop till Azure HDInsight'
description: Lär dig metod tips för datamigrering för migrering av lokala Hadoop-kluster till Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cdb028bcd608aa7a19a7c83e5e2c1129386928a0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939788"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – metod tips för data migrering

Den här artikeln innehåller rekommendationer för datamigrering till Azure HDInsight. Den ingår i en serie som ger bästa praxis för att hjälpa till att migrera lokala Apache Hadoop system till Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrera lokala data till Azure

Det finns två huvud alternativ för att migrera data från lokala datorer till Azure-miljön:

* Överföra data över nätverk med TLS
    * Via Internet kan du överföra data till Azure Storage via en vanlig Internet anslutning med något av flera verktyg som: Azure Storage Explorer, AzCopy, Azure PowerShell och Azure CLI. Mer information finns i [Flytta data till och från Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md).

    * Express Route-ExpressRoute är en Azure-tjänst som gör att du kan skapa privata anslutningar mellan Microsoft-datacenter och infrastruktur som finns lokalt eller i en samplacerings anläggning. ExpressRoute-anslutningar går inte via det offentliga Internet och ger högre säkerhet, tillförlitlighet och hastighet med lägre fördröjning än vanliga anslutningar via Internet. Mer information finns i [skapa och ändra en ExpressRoute-krets](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Data Box-enhet data överföring online – Data Box Edge och Data Box Gateway är data överförings produkter online som fungerar som nätverks lagrings-gatewayer för att hantera data mellan din plats och Azure. Data Box Edge en lokal nätverks enhet, överför data till och från Azure och använder artificiell intelligens (AI)-aktiverade Edge Compute för att bearbeta data. Data Box Gateway är en virtuell installation med Storage Gateway-funktioner. Mer information finns i [Azure Data Box dokumentation – online-överföring](../../databox-online/index.yml).

* Leverera data offline

    Data Box-enhet data överföring offline – Data Box-enhet, Data Box Disk och Data Box Heavy enheter kan du överföra stora mängder data till Azure när nätverket inte är ett alternativ. De här data överförings enheterna i frånkopplat läge levereras mellan din organisation och Azure-datacentret. De använder AES-kryptering för att hjälpa till att skydda dina data under överföringen och de genomgår en grundlig och mer sanerad rensnings process för att ta bort data från enheten. Mer information om Data Box-enhet frånkopplade överförings enheter finns i [Azure Data Box dokumentation – offline-överföring](../../databox/index.yml). Mer information om migrering av Hadoop-kluster finns i [använda Azure Data box för att migrera från en lokal HDFS-lagring till Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

Följande tabell innehåller ungefärlig data överförings tid baserat på data volymen och nätverks bandbredden. Använd en data ruta om datamigreringen förväntas ta mer än tre veckor.

|Data mängd | Nätverks bandbredd<br>av<br>**45 Mbit/s (T3)**|Nätverks bandbredd<br>av<br>**100 Mbit/s**|Nätverks bandbredd<br>av<br>**1 Gbit/s**|Nätverks bandbredd<br>av<br>**10 Gbit/s**|
|---|:---:|:---:|:---:|:---:|
|1 TB|2 dagar|1 dag| 2 timmar|14 minuter|
|10 TB|22 dagar|10 dagar|1 dag|2 timmar|
|35 TB|76 dagar|34 dagar|3 dagar|8 timmar|
|80 TB|173 dagar|78 dagar|8 dagar|19 timmar|
|100 TB|216 dagar|97 dagar|10 dagar|1 dag|
|200 TB|1 år|194 dagar|19 dagar|2 dagar|
|500 TB|3 år|1 år|49 dagar|5 dagar|
|1 PB|6 år|3 år|97 dagar|10 dagar|
|2 PB|12 år|5 år|194 dagar|19 dagar|

Verktyg som är inbyggda i Azure, t. ex. Apache Hadoop DistCp, Azure Data Factory och AzureCp, kan användas för att överföra data över nätverket. Verktyget WANDisco kan också användas för samma ändamål. Apache Kafka MirrorMaker och Apache Sqoop kan användas för pågående data överföring från lokala datorer till Azure Storage-System.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Prestanda överväganden när du använder Apache Hadoop DistCp

DistCp är ett Apache-projekt som använder ett MapReduce-kart jobb för att överföra data, hantera fel och återställa från dessa fel. En lista med källfiler tilldelas varje kart aktivitet. Map-aktiviteten kopierar sedan alla tilldelade filer till målet. Det finns flera metoder som kan förbättra prestandan för DistCp.

### <a name="increase-the-number-of-mappers"></a>Öka antalet mappningar

DistCp försöker skapa kart aktiviteter så att var och en kopierar ungefär samma antal byte. Som standard använder DistCp-jobb 20 mapper. Om du använder fler Mapper för Distcp (med parametern ' x på kommando raden) ökar parallellitet under data överförings processen och minskar data överföringens längd. Det finns dock två saker att tänka på när du ökar antalet mappningar:

* DistCp för den lägsta precisionen är en enskild fil. Det går inte att ange ett antal fler Mapper än antalet källfiler och de tillgängliga kluster resurserna kommer att tas bort.

* Överväg det tillgängliga garn minnet på klustret för att fastställa antalet mappningar. Varje kart aktivitet startas som en garn behållare. Förutsatt att inga andra tunga arbets belastningar körs i klustret, kan antalet Mapper bestämmas av följande formel: m = (antalet arbetsnoder \* garn minne för varje arbetsnod)/storlek på garn behållare. Men om andra program använder minne väljer du att bara använda en del av garn minnet för DistCp-jobb.

### <a name="use-more-than-one-distcp-job"></a>Använd mer än ett DistCp-jobb

När storleken på data uppsättningen som ska flyttas är större än 1 TB använder du mer än ett DistCp-jobb. Om du använder mer än ett jobb begränsas effekten av felen. Om ett jobb Miss lyckas behöver du bara starta om det aktuella jobbet i stället för alla jobb.

### <a name="consider-splitting-files"></a>Överväg att dela filer

Om det finns ett litet antal stora filer kan du överväga att dela upp dem i 256 MB fil segment för att få mer potentiell samtidighet med fler mappningar.

### <a name="use-the-strategy-command-line-parameter"></a>Använda kommando rads parametern "strategi"

Överväg att använda `strategy = dynamic` parameter på kommando raden. Standardvärdet för `strategy` parametern är `uniform size` , i vilket fall varje karta kopierar ungefär samma antal byte. När den här parametern ändras till `dynamic` delas list filen upp i flera "segment-filer". Antalet segment-filer är en multipel av antalet kartor. Varje kart aktivitet tilldelas en av segment-filerna. När alla sökvägar i ett segment har bearbetats raderas det aktuella segmentet och ett nytt segment förvärvas. Processen fortsätter tills inga fler segment är tillgängliga. Med den här metoden "dynamisk" kan du snabbare mappa aktiviteter för att förbruka fler sökvägar än långsammare, vilket påskyndar DistCp-jobbet.

### <a name="increase-the-number-of-threads"></a>Öka antalet trådar

Se om ökningen av `-numListstatusThreads` parametern ger bättre prestanda. Den här parametern styr antalet trådar som ska användas för att skapa fil listor och 40 är det maximala värdet.

### <a name="use-the-output-committer-algorithm"></a>Använda committer-algoritmen för utdata

Se om du överför parametern `-Dmapreduce.fileoutputcommitter.algorithm.version=2` förbättrar DistCp-prestanda. Den här utdata committer-algoritmen har optimeringar för att skriva utdatafilerna till målet. Följande kommando är ett exempel som visar användningen av olika parametrar:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migrering av metadata

### <a name="apache-hive"></a>Apache Hive

Hive-metaarkiv kan migreras antingen med hjälp av skripten eller med hjälp av DB-replikeringen.

#### <a name="hive-metastore-migration-using-scripts"></a>Hive-metaarkiv migrering med skript

1. Generera Hive-DDLs från lokalt Hive-metaarkiv. Det här steget kan göras med hjälp av ett [wrapper bash-skript](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Redigera den genererade DDL: en för att ersätta HDFS-URL med WASB/ADLS/ABFS-URL: er.
1. Kör den uppdaterade DDL: en på metaarkiv från HDInsight-klustret.
1. Kontrol lera att den Hive-metaarkiv versionen är kompatibel mellan lokalt och molnet.

#### <a name="hive-metastore-migration-using-db-replication"></a>Hive-metaarkiv migrering med DB-replikering

- Konfigurera databasreplikering mellan lokala Hive-metaarkiv DB-och HDInsight metaarkiv DB.
- Använd "Hive-MetaTool" för att ersätta HDFS-URL med WASB/ADLS/ABFS-URL: er, till exempel:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Exportera lokala Ranger-principer till XML-filer.
- Transformera lokala HDFS-baserade sökvägar till WASB/ADLS med ett verktyg som XSLT.
- Importera principerna på Ranger som körs på HDInsight.

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Säkerhets-och DevOps metod tips för lokal att Azure HDInsight Hadoop migrering](apache-hadoop-on-premises-migration-best-practices-security-devops.md)