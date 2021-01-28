---
title: Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0
description: Lär dig hur du migrerar Apache Hive-arbetsbelastningar i HDInsight 3,6 till HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 93dc565055c6eb413a0c277a9891e5fcfab50345
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941346"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0

Det här dokumentet visar hur du migrerar Apache Hive-och LLAP-arbetsbelastningar på HDInsight 3,6 till HDInsight 4,0. HDInsight 4,0 innehåller nyare Hive-och LLAP-funktioner som materialiserade vyer och cachelagring av frågeresultat. När du migrerar dina arbets belastningar till HDInsight 4,0 kan du använda många nyare funktioner i Hive 3 som inte är tillgängliga på HDInsight 3,6.

Den här artikeln beskriver följande ämnen:

* Migrering av Hive-metadata till HDInsight 4,0
* Säker migrering av syror och icke-syror tabeller
* Bevarande av Hive-säkerhets principer i HDInsight-versioner
* Frågekörning och fel sökning från HDInsight 3,6 till HDInsight 4,0

En fördel med Hive är möjligheten att exportera metadata till en extern databas (kallas Hive-Metaarkiv). **Hive-metaarkiv** ansvarar för att lagra tabell statistik, inklusive tabell lagrings plats, kolumn namn och tabell index information. HDInsight 3,6 och HDInsight 4,0 kräver olika metaarkiv-scheman och kan inte dela en enda metaarkiv. Det rekommenderade sättet att uppgradera Hive-metaarkiv säkert är att uppgradera en kopia i stället för den ursprungliga i den aktuella produktions miljön. Det här dokumentet kräver att det ursprungliga och nya klustret har åtkomst till samma lagrings konto. Därför omfattar den inte migrering av data till en annan region.

## <a name="migrate-from-external-metastore"></a>Migrera från externa metaarkiv

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. kör stor komprimering på syror-tabeller i HDInsight 3,6

HDInsight 3,6-och HDInsight 4,0 sur-tabeller förstår sur delta på olika sätt. Den enda åtgärd som krävs innan migrering är att köra "MAJOR"-komprimering mot varje sur tabell i 3,6-klustret. Mer information om komprimering finns i [hand boken för Hive-språket](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

### <a name="2-copy-sql-database"></a>2. Kopiera SQL-databas
Skapa en ny kopia av din externa metaarkiv. Om du använder en extern metaarkiv är ett av de säkraste och enkla sätten att göra en kopia av metaarkiv att [återställa databasen](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) med ett annat namn med hjälp av `RESTORE` funktionen.  Mer information om hur du kopplar en extern metaarkiv till ett HDInsight-kluster finns i [använda externa metadata butiker i Azure HDInsight](../hdinsight-use-external-metadata-stores.md) .

### <a name="3-upgrade-metastore-schema"></a>3. uppgradera metaarkiv-schemat
När metaarkiv- **kopieringen** är klar kör du ett skript för schema uppgradering i [skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md) på det befintliga HDInsight 3,6-klustret för att uppgradera det nya metaarkiv till Hive 3-schemat. (Det här steget kräver inte att den nya metaarkiv är ansluten till ett kluster.) Detta gör att databasen kan anslutas som HDInsight 4,0 metaarkiv.

Använd värdena i tabellen nedan. Ersätt `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` med lämpliga värden för Hive-metaarkiv **kopian**, avgränsade med blank steg. Ta inte med ". database.windows.net" när du anger SQL Server-namnet.

|Egenskap | Värde |
|---|---|
|Skript typ|– Anpassad|
|Name|Hive-uppgradering|
|Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Node-typ (er)|Head|
|Parametrar|LÖSEN ORD FÖR SQLSERVERNAME DATABASENAME USERNAME|

> [!Warning]  
> Uppgraderingen som konverterar HDInsight 3,6-metadata-schemat till HDInsight 4,0-schemat kan inte ångras.

Du kan verifiera uppgraderingen genom att köra följande SQL-fråga mot databasen:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. distribuera ett nytt HDInsight 4,0-kluster

1. Ange det uppgraderade metaarkiv som det nya klustrets Hive-metaarkiv.

1. De faktiska data från tabellerna är dock inte tillgängliga förrän klustret har åtkomst till de nödvändiga lagrings kontona.
Kontrol lera att Hive-tabellerna "lagrings konton i HDInsight 3,6-klustret" har angetts som antingen primära eller sekundära lagrings konton för det nya HDInsight 4,0-klustret.
Mer information om hur du lägger till lagrings konton i HDInsight-kluster finns i [lägga till ytterligare lagrings konton i HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. Slutför migreringen med ett verktyg efter uppgradering i HDInsight 4,0

Hanterade tabeller måste vara sur-kompatibla i HDInsight 4,0 som standard. När du har slutfört migreringen av metaarkiv kör du ett verktyg efter uppgradering för att göra tidigare icke-sur-hanterade tabeller som är kompatibla med HDInsight 4,0-klustret. Det här verktyget kommer att använda följande konvertering:

|3,6 |4,0 |
|---|---|
|Externa tabeller|Externa tabeller|
|Icke-sur hanterade tabeller|Externa tabeller med egenskapen ' external. Table. Rensa ' = ' true '|
|SUR hanterade tabeller|SUR hanterade tabeller|

Kör Hive-verktyget efter uppgradering från HDInsight 4,0-klustret med SSH-gränssnittet:

1. Anslut till klustrets huvudnoden med SSH. Instruktioner finns i [ansluta till HDInsight med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Öppna ett inloggnings gränssnitt som Hive-användare genom att köra `sudo su - hive`
1. Kör följande kommando från gränssnittet.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

När verktyget är klart är ditt Hive-lager klart för HDInsight 4,0.

## <a name="migrate-from-internal-metastore"></a>Migrera från interna metaarkiv

Om ditt HDInsight 3,6-kluster använder en intern Hive-metaarkiv följer du stegen nedan för att köra ett skript, som genererar Hive-frågor för att exportera objekt definitioner från metaarkiv.

HDInsight 3,6-och 4,0-klustren måste använda samma lagrings konto.

> [!NOTE]
>
> * Om det gäller sur tabeller skapas en ny kopia av de data som finns under tabellen.
>
> * Det här skriptet stöder endast migrering av Hive-databaser, tabeller och partitioner. Andra metadataobjekt, som vyer, UDF: er och tabell begränsningar, kommer att kopieras manuellt.
>
> * När skriptet har slutförts förutsätts det att det gamla klustret inte längre kommer att användas för att komma åt de tabeller eller databaser som anges i skriptet.
>
> * Alla hanterade tabeller kommer att bli transaktionella i HDInsight 4,0. Du kan också behålla tabellen icke-transaktionell genom att exportera data till en extern tabell med egenskapen ' external. Table. Rensa ' = ' true '. Exempel:
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Anslut till HDInsight 3,6-klustret med hjälp av en [SSH-klient (Secure Shell)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Från den öppna SSH-sessionen laddar du ned följande skript fil för att skapa en fil med namnet **alltables. HQL**.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * För ett vanligt HDInsight-kluster, utan ESP, kör du bara `exporthive_hdi_3_6.sh` .

    * För ett kluster med ESP, kinit och ändra argumenten till Beeline: kör följande, definiera användare och domän för Azure AD-användare med fullständig Hive-behörighet.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Avsluta SSH-sessionen. Ange sedan ett SCP-kommando för att ladda ned **alltables. HQL** lokalt.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Ladda upp **alltables. HQL** till det *nya* HDInsight-klustret.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Använd sedan SSH för att ansluta till det *nya* HDInsight 4,0-klustret. Kör följande kod från en SSH-session till det här klustret:

    Utan ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    Med ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

Verktyget efter uppgradering för extern metaarkiv-migrering gäller inte här, eftersom icke-sur-hanterade tabeller från HDInsight 3,6 omvandla till sur-hanterade tabeller i HDInsight 4,0.

> [!Important]  
> Hanterade tabeller i HDInsight 4,0 (inklusive tabeller som migrerats från 3,6) bör inte nås av andra tjänster eller program, inklusive HDInsight 3,6-kluster.

## <a name="secure-hive-across-hdinsight-versions"></a>Säker Hive i HDInsight-versioner

Eftersom HDInsight 3,6 integrerar HDInsight med Azure Active Directory som använder HDInsight Enterprise Security Package (ESP). ESP använder Kerberos och Apache Ranger för att hantera behörigheter för vissa resurser i klustret. Ranger-principer som distribueras mot Hive i HDInsight 3,6 kan migreras till HDInsight 4,0 med följande steg:

1. Navigera till fönstret Ranger Service Manager i ditt HDInsight 3,6-kluster.
2. Navigera till principen med namnet **HIVE** och exportera principen till en JSON-fil.
3. Se till att alla användare som anges i den exporterade princip-JSON finns i det nya klustret. Om en användare refereras till i princip-JSON men inte finns i det nya klustret kan du antingen lägga till användaren i det nya klustret eller ta bort referensen från principen.
4. Navigera till fönstret **Ranger Service Manager** i ditt HDInsight 4,0-kluster.
5. Navigera till principen med namnet **HIVE** och importera Ranger policy JSON från steg 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Kontrol lera kompatibilitet och ändra koder efter behov i appen test

När du migrerar arbets belastningar som befintliga program och frågor bör du läsa viktig information och dokumentation för ändringar och tillämpa ändringarna vid behov. Om ditt HDInsight 3,6-kluster använder en delad Spark-och Hive-metaarkiv krävs [ytterligare konfiguration med hjälp av Hive Warehouse Connector](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Distribuera ny app för produktion

För att växla till det nya klustret, t. ex. kan du installera ett nytt klient program och använda det som en ny produktions miljö, eller så kan du uppgradera det befintliga klient programmet och växla till HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Växla HDInsight 4,0 till produktion

Om skillnaderna skapades i metaarkiv vid testning måste du uppdatera ändringarna precis innan du växlar. I så fall kan du exportera & importera metaarkiv och sedan uppgradera igen.

## <a name="remove-the-old-production"></a>Ta bort den gamla produktionen

När du har bekräftat att versionen är slutförd och fullt fungerande kan du ta bort version 3,6 och föregående metaarkiv. Kontrol lera att allt är migrerat innan du tar bort miljön.

## <a name="query-execution-across-hdinsight-versions"></a>Frågekörningen i HDInsight-versioner

Det finns två sätt att köra och felsöka Hive/LLAP-frågor i ett HDInsight 3,6-kluster. HiveCLI tillhandahåller en kommando rad upplevelse och [vyn Tez View/Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) innehåller ett GUI-baserat arbets flöde.

I HDInsight 4,0 har HiveCLI ersatts med Beeline. Vyn Tez View/Hive innehåller ett GUI-baserat arbets flöde. HiveCLI är en Thrift-klient för Hiveserver 1 och Beeline är en JDBC-klient som ger åtkomst till Hiveserver 2. Beeline kan också användas för att ansluta till en annan JDBC-kompatibel databas slut punkt. Beeline är tillgängligt i HDInsight 4,0 utan någon installation.

## <a name="next-steps"></a>Nästa steg

* [HDInsight 4,0-meddelande](../hdinsight-version-release.md)
* [HDInsight 4,0-djup](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabeller för Hive 3-syror](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)