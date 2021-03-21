---
title: Migrera standard Hive-metaarkiv till externa metaarkiv i Azure HDInsight
description: Migrera standard Hive-metaarkiv till externa metaarkiv i Azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 825204fe40125a65e8e6f27c6973417813700a9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746358"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Migrera standard Hive-metaarkiv DB till extern metaarkiv-databas

Den här artikeln visar hur du migrerar metadata från en [standard-metaarkiv-databas](../hdinsight-use-external-metadata-stores.md#default-metastore) för Hive till en extern SQL Database i HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Varför migrera till extern metaarkiv DB

* Standard metaarkiv-databasen är begränsad till Basic SKU och kan inte hantera arbets belastningar för produktions skala.

* Med den externa metaarkiv DB kan kunden skala Hive-dataresurser horisontellt genom att lägga till nya HDInsight-kluster som delar samma metaarkiv DB.

* För HDInsight 3,6 till 4,0-migrering är det obligatoriskt att migrera metadata till externa metaarkiv DB innan du uppgraderar Hive-schemats version. Se [migrera arbets belastningar från hdinsight 3,6 till hdinsight 4,0](./apache-hive-migrate-workloads.md).

Eftersom standard-metaarkiv DB har begränsad beräknings kapacitet rekommenderar vi att du använder andra jobb på klustret för att få en låg belastning när du migrerar metadata.

Käll-och mål-databaser måste använda samma HDInsight-version och samma lagrings konton. Om du uppgraderar HDInsight-versioner från 3,6 till 4,0 slutför du stegen i den här artikeln först. Följ sedan de officiella uppgraderings stegen [här](./apache-hive-migrate-workloads.md).

## <a name="prerequisites"></a>Förutsättningar

Om du använder [Azure Data Lake Storage gen1](../overview-data-lake-storage-gen1.md)är Hive-tabellens platser förmodligen beroende av KLUSTRETs HDFS-konfigurationer för Azure Data Lake Storage gen1. Kör följande skript åtgärd för att göra dessa platser portabla till andra kluster. Se [skript åtgärd för ett kluster som körs](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Åtgärden liknar att ersätta symlinks med sina fullständiga sökvägar.

|Egenskap | Värde |
|---|---|
|Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Node-typ (er)|Head|
|Parametrar|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Migrera med export/import med sqlpackage

An-HDInsight kluster som skapats förrän 2020-10-15 har stöd för SQL-export/import för Hive-standardmetaarkiv-databasen med hjälp av `sqlpackage` .

1. Installera [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) i klustret.

2. Exportera standard metaarkiv DB till BACPAC-filen genom att köra följande kommando.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Spara filen BACPAC. Nedan finns ett alternativ.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Importera BACPAC-filen till en ny databas med stegen som anges [här](../../azure-sql/database/database-import.md).

5. Den nya databasen är redo att [konfigureras som extern metaarkiv-databas på ett nytt HDInsight-kluster](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Migrera med Hive-skript

Kluster som skapats före 2020-10-15 har inte stöd för export/import av standard-metaarkiv-databasen.

För sådana kluster följer du guiden [Kopiera Hive-tabeller över lagrings konton](./hive-migration-across-storage-accounts.md)med hjälp av ett andra kluster med en [extern Hive-metaarkiv databas](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Det andra klustret kan använda samma lagrings konto men måste använda ett nytt standard fil system.

### <a name="option-to-shallow-copy"></a>Alternativ till "lite"-kopia
Lagrings förbrukningen skulle dubbleras när tabellerna är "djup" kopieras med hjälp av hand boken ovan. Du måste manuellt rensa data i käll lagrings behållaren.
Vi kan i stället "ytlig" Kopiera tabellerna om de inte är transaktionella. Alla Hive-tabeller i HDInsight 3,6 är icke-transaktionella som standard, men endast externa tabeller är icke-transaktionella i HDInsight 4,0. Transaktions tabeller måste vara djup kopierade. Följ dessa steg om du vill ha lite kopiering av icke-transaktionella tabeller:

1. Kör skript [Hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) på käll klustrets primära huvudnoden för att generera DDL för varje Hive-tabell.
2. DDL: en skrivs till ett lokalt Hive-skript med namnet `/tmp/hdi_hive_ddls.hql` . Kör detta på mål klustret som använder en extern Hive-metaarkiv databas.

## <a name="verify-that-all-hive-tables-are-imported"></a>Kontrol lera att alla Hive-tabeller har importer ATS

Följande kommando använder en SQL-fråga på metaarkiv-databasen för att skriva ut alla Hive-tabeller och deras data platser. Jämför utdata mellan nya och gamla kluster för att kontrol lera att inga tabeller saknas i den nya metaarkiv-databasen.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Ytterligare läsning

* [Migrera arbets belastningar från HDInsight 3,6 till 4,0](./apache-hive-migrate-workloads.md)
* [Migrering av Hive-arbetsbelastning över lagrings konton](./hive-migration-across-storage-accounts.md)
* [Ansluta till Beeline i HDInsight](../hadoop/connect-install-beeline.md)
* [Felsöka behörighets fel skapa tabell](./interactive-query-troubleshoot-permission-error-create-table.md)
