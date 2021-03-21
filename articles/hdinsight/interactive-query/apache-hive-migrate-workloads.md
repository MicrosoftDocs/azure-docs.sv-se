---
title: Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0
description: Lär dig hur du migrerar Apache Hive-arbetsbelastningar i HDInsight 3,6 till HDInsight 4,0.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: b13e8e088eff95071247a53ad1a4a18879f94053
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742202"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0

HDInsight 4,0 har flera fördelar jämfört med HDInsight 3,6. Här är en [Översikt över vad som är nytt i HDInsight 4,0](../hdinsight-version-release.md).

Den här artikeln beskriver hur du migrerar Hive-arbetsbelastningar från HDInsight 3,6 till 4,0, inklusive

* Hive-metaarkiv kopiering och schema uppgradering
* Säker migrering för sur kompatibilitet
* Bevarande av Hive säkerhets principer

De nya och gamla HDInsight-klustren måste ha åtkomst till samma lagrings konton.

Migrering av Hive-tabeller till ett nytt lagrings konto måste göras som ett separat steg. Se [Hive-migrering över lagrings konton](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>Steg för att uppgradera

### <a name="1-prepare-the-data"></a>1. Förbered data

* HDInsight 3,6 som standard stöder inte syror-tabeller. Om det finns sur-tabeller kör du dock "MAJOR"-komprimeringen på dem. Mer information om komprimering finns i [hand boken för Hive-språket](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

* Om du använder [Azure Data Lake Storage gen1](../overview-data-lake-storage-gen1.md)är Hive-tabellens platser förmodligen beroende av KLUSTRETs HDFS-konfigurationer. Kör följande skript åtgärd för att göra dessa platser portabla till andra kluster. Se [skript åtgärd för ett kluster som körs](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

    |Egenskap | Värde |
    |---|---|
    |Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Node-typ (er)|Head|
    |Parametrar||

### <a name="2-copy-the-sql-database"></a>2. Kopiera SQL-databasen

* Om klustret använder en standard Hive-metaarkiv, följ den här [guiden](./hive-default-metastore-export-import.md) för att exportera metadata till en extern metaarkiv. Skapa sedan en kopia av den externa metaarkiv för uppgradering.

* Om klustret använder en extern Hive-metaarkiv skapar du en kopia av den. Alternativen omfattar [export/import](../../azure-sql/database/database-export.md) och [återställning av tidpunkter](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore).

### <a name="3-upgrade-the-metastore-schema"></a>3. uppgradera metaarkiv-schemat

I det här steget används [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) från HDInsight 4,0 för att uppgradera metaarkiv-schemat.

> [!Warning]
> Det här steget går inte att ångra. Kör bara det på en kopia av metaarkiv.

1. Skapa ett tillfälligt HDInsight 4,0-kluster för att få åtkomst till 4,0 Hive `schematool` . Du kan använda [standard Hive-metaarkiv](../hdinsight-use-external-metadata-stores.md#default-metastore) för det här steget.

1. Från HDInsight 4,0-klustret kör `schematool` du för att uppgradera målet HDInsight 3,6 metaarkiv:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Det här verktyget använder klienten `beeline` för att köra SQL-skript i `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > SQL-syntaxen i dessa skript är inte nödvändigt vis kompatibel med andra klient verktyg. Till exempel kräver [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [Frågeredigeraren på Azure Portal](../../azure-sql/database/connect-query-portal.md) nyckelord `GO` efter varje kommando.
    >
    > Om ett skript Miss lyckas på grund av resurs kapacitet eller transaktions-timeout, skala upp SQL Database.

1. Verifiera den slutgiltiga versionen med Query `select schema_version from dbo.version` .

    Utdata ska överensstämma med följande bash-kommando från HDInsight 4,0-klustret.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Ta bort det tillfälliga HDInsight 4,0-klustret.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. distribuera ett nytt HDInsight 4,0-kluster

Skapa ett nytt HDInsight 4,0-kluster och [Välj den uppgraderade Hive-metaarkiv](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) och samma lagrings konton.

* Det nya klustret kräver inte samma standard fil system.

* Om metaarkiv innehåller tabeller som finns i flera lagrings konton, måste du lägga till dessa lagrings konton i det nya klustret för att få åtkomst till dessa tabeller. Se [lägga till ytterligare lagrings konton i HDInsight](../hdinsight-hadoop-add-storage.md).

* Om Hive-jobben inte kan köras på grund av lagrings tillgänglighet kontrollerar du att tabell platsen finns i ett lagrings konto som läggs till i klustret.

    Använd följande Hive-kommando för att identifiera tabell plats:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. konvertera tabeller för sur efterlevnad

Hanterade tabeller måste vara sur-kompatibla i HDInsight 4,0. Kör `strictmanagedmigration` på HDInsight 4,0 för att konvertera alla icke-sur hanterade tabeller till externa tabeller med egenskapen `'external.table.purge'='true'` . Kör från huvudnoden:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Säker Hive i HDInsight-versioner

HDInsight kan integreras med Azure Active Directory med hjälp av HDInsight Enterprise Security Package (ESP). ESP använder Kerberos och Apache Ranger för att hantera behörigheter för vissa resurser i klustret. Ranger-principer som distribueras mot Hive i HDInsight 3,6 kan migreras till HDInsight 4,0 med följande steg:

1. Navigera till fönstret Ranger Service Manager i ditt HDInsight 3,6-kluster.
2. Navigera till principen med namnet **HIVE** och exportera principen till en JSON-fil.
3. Se till att alla användare som anges i den exporterade princip-JSON finns i det nya klustret. Om en användare refereras till i princip-JSON men inte finns i det nya klustret kan du antingen lägga till användaren i det nya klustret eller ta bort referensen från principen.
4. Navigera till fönstret **Ranger Service Manager** i ditt HDInsight 4,0-kluster.
5. Navigera till principen med namnet **HIVE** och importera Ranger policy JSON från steg 2.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>Hive-ändringar i HDInsight 4,0 som kan kräva program ändringar

* Mer information finns i avsnittet om att [använda Hive](./apache-hive-warehouse-connector.md) -metaarkiv för att dela mellan Spark och HIVE för sur tabeller.

* HDInsight 4,0 använder [Storage-baserad auktorisering](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server). Om du ändrar fil behörigheter eller skapar mappar som en annan användare än Hive, kommer du troligen att behöva Hive-fel baserat på lagrings behörigheter. För att åtgärda detta ger du `rw-` åtkomst till användaren. Se [behörighets guide för HDFS](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` ersätts med `Beeline` .

Se [HDInsight 4,0-meddelande](../hdinsight-version-release.md) för ytterligare ändringar.

## <a name="further-reading"></a>Ytterligare läsning

* [HDInsight 4,0-meddelande](../hdinsight-version-release.md)
* [HDInsight 4,0-djup](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabeller för Hive 3-syror](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
