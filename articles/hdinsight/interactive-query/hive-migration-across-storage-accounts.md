---
title: Migrering av Hive-arbetsbelastning till nytt konto i Azure Storage
description: Migrering av Hive-arbetsbelastning till nytt konto i Azure Storage
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747225"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Migrering av Hive-arbetsbelastning till nytt konto i Azure Storage

Lär dig hur du använder skript åtgärder för att kopiera Hive-tabeller över lagrings konton i HDInsight. Detta kan vara användbart när du migrerar till [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Om du vill kopiera en enskild Hive-tabell manuellt i HDInsight 4,0, se [Hive export/import](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Förutsättningar

* Ett nytt HDInsight-kluster med följande konfigurationer:
  * Dess standard fil system finns på mål lagrings kontot. Se [använda Azure Storage med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md).
  * Dess kluster version måste matcha käll klustrets.
  * Den använder en ny extern Hive-metaarkiv databas. Se [Använd externa metadata-Arkiv](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).
* Ett lagrings konto som är tillgängligt för både original och nya kluster. Se [lägga till ytterligare lagrings konton till HDInsight](../hdinsight-hadoop-add-storage.md) och [lagrings typer och-funktioner](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) för tillåtna sekundära lagrings typer.

    Här följer några alternativ:
  * Lägg till mål lagrings kontot i det ursprungliga klustret.
  * Lägg till det ursprungliga lagrings kontot i det nya klustret.
  * Lägg till ett mellanliggande lagrings konto i både det ursprungliga och nya klustret.

## <a name="how-it-works"></a>Så här fungerar det

Vi kör en skript åtgärd för att exportera Hive-tabeller från det ursprungliga klustret till en angiven HDFS-katalog. Se [skript åtgärd för ett kluster som körs](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Sedan kör vi en annan skript åtgärd på det nya klustret för att importera Hive-tabellerna från HDFS-katalogen.

Skriptet kommer att återskapa tabellerna till det nya klustrets standard fil system. Inbyggda tabeller kopierar även sina data i lagrings utrymmen. Icke-interna tabeller kopieras endast per definition. Se [Hive-lagrings hanterare](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) för information om icke-ursprungliga tabeller.

Sökvägen till externa tabeller som inte är i Hive-katalogen för data lager kommer att bevaras. Andra tabeller kopieras till mål klustrets standard Sök väg för Hive. Se Hive-egenskaper `hive.metastore.warehouse.external.dir` och `hive.metastore.warehouse.dir` .

Skripten kommer inte att bevara anpassade fil behörigheter i mål klustret.

> [!NOTE]
>
> Den här guiden stöder kopiering av metadata-objekt relaterade till Hive-databaser, tabeller och partitioner. Andra metadata-objekt måste skapas på nytt manuellt.
>
> * För `Views` stöder Hive `SHOW VIEWS` kommando som Hive-2.2.0 på HDInsight 4,0. Används `SHOW CREATE TABLE` för vydefinition. För tidigare versioner av Hive, fråga metaarkiv SQL DB för att Visa vyer.
> * För `Materialized Views` använder du kommandon `SHOW MATERIALIZED VIEWS` , `DESCRIBE FORMATTED` och `CREATE MATERIALIZED VIEW` . Mer information finns i [materialiserade vyer](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) .
> * För `Constraints` , stöds som Hive-2.1.0 på HDInsight 4,0, Använd `DESCRIBE EXTENDED` för att lista begränsningar för en tabell och Använd `ALTER TABLE` för att lägga till begränsningar. Se [ändra tabell begränsningar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) för mer information.

## <a name="copy-hive-tables"></a>Kopiera Hive-tabeller

1. Använd skript åtgärden "Exportera" på det ursprungliga klustret med följande fält.

    Detta genererar och kör mellanliggande Hive-skript. De kommer att sparas till den angivna `<hdfs-export-path>` .

    Du kan också använda `--run-script=false` för att anpassa dem innan du kör manuellt.

    |Egenskap | Värde |
    |---|---|
    |Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Node-typ (er)|Head|
    |Parametrar|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. När du har slutfört exporten använder du skript åtgärden "Importera" på det nya klustret med följande fält.

    |Egenskap | Värde |
    |---|---|
    |Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Node-typ (er)|Head|
    |Parametrar|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Verifiering

Hämta och kör skriptet som rot användare [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) på den primära noden i varje kluster och Jämför innehållet i utdatafilen `/tmp/hive_contents.out` . Se [ansluta till HDInsight (Apache Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="cleanup-additional-storage-usage"></a>Rensa ytterligare lagrings användning

När lagringsmigrering har slutförts och verifierats kan du ta bort data i den angivna HDFS-export Sök vägen.

Alternativet är att använda HDFS-kommandot `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>Alternativ: minska användningen av ytterligare lagrings utrymme

Åtgärden exportera skript dubblerar förmodligen lagrings användningen på grund av Hive. Det är dock möjligt att begränsa den ytterligare lagrings användningen genom att migrera manuellt, en databas eller tabell i taget.

1. Ange `--run-script=false` om du vill hoppa över körningen av det genererade Hive-skriptet. Hive-skript för export och import sparas fortfarande i export Sök vägen.

2. Köra kodfragment från registrerings-och import skripten databas-by-Database eller Table-by-Table, och rensa export Sök vägen manuellt efter varje migrerad databas eller tabell.

## <a name="next-steps"></a>Nästa steg

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Använda extern metadatalagring](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Lagrings typer och funktioner](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Skript åtgärd för ett kluster som körs](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
