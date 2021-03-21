---
title: Operationalisera en pipeline för data analys – Azure
description: Konfigurera och kör en exempel data pipeline som utlöses av nya data och ger kortfattade resultat.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/25/2019
ms.openlocfilehash: a306890560497b0c7196f1286de3f73039821ea2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939516"
---
# <a name="operationalize-a-data-analytics-pipeline"></a>Operationalisera en pipeline för dataanalys

*Datapipelines* med många lösningar för data analys. Som namnet antyder tar en datapipeline i rå data, rengörs och omformerar den efter behov och utför vanligt vis beräkningar eller agg regeringar innan de bearbetade data lagras. Bearbetade data används av klienter, rapporter eller API: er. En datapipeline måste ge upprepnings bara resultat, oavsett om ett schema eller när den utlöses av nya data.

Den här artikeln beskriver hur du operationalisera data pipelines för repeterbarhet med Oozie som körs på HDInsight Hadoop-kluster. Exempel scenariot vägleder dig genom en datapipeline som förbereder och bearbetar data i flyg planens flyg tid.

I följande scenario är indata en platt fil som innehåller en grupp med flyg data i en månad. Dessa flyg data innehåller information som till exempel platsens ursprung och målplats, mil flödet, avresa och ankomst tider, och så vidare. Målet med den här pipelinen är att sammanfatta dagliga flyg bolags prestanda, där varje flyg bolag har en rad för varje dag med genomsnittlig avvikelse och ankomst fördröjningar på några minuter, och det totala antalet mil i km som den dagen.

| YEAR | MONTH | DAY_OF_MONTH | BÄRVÅG |AVG_DEP_DELAY | AVG_ARR_DELAY |TOTAL_DISTANCE |
| --- | --- | --- | --- | --- | --- | --- |
| 2017 | 1 | 3 | ' | 10,142229 | 7,862926 | 2644539 |
| 2017 | 1 | 3 | AS | 9,435449 | 5,482143 | 572289 |
| 2017 | 1 | 3 | DL | 6,935409 | – 2,1893024 | 1909696 |

Exempel pipelinen väntar tills en ny tids period har inkommer och lagrar sedan den detaljerade flyg informationen i Apache Hive data lagret för långsiktiga analyser. Pipelinen skapar också en mycket mindre data uppsättning som sammanfattar bara de dagliga flyg uppgifterna. Den här dagliga informationen om flyg sammanfattningen skickas till en SQL Database för att tillhandahålla rapporter, till exempel för en webbplats.

Följande diagram illustrerar exempel pipelinen.

![Översikt över HDI Flight exempel data pipeline](./media/hdinsight-operationalize-data-pipeline/flight-pipeline-overview.png)

## <a name="apache-oozie-solution-overview"></a>Översikt över Apache Oozie-lösning

Den här pipelinen använder Apache Oozie som körs på ett HDInsight Hadoop-kluster.

Oozie beskriver sina pipeliner vad gäller *åtgärder*, *arbets flöden* och *koordinatorer*. Åtgärder fastställer det faktiska arbetet som ska utföras, till exempel köra en Hive-fråga. Arbets flöden definierar ordningen på åtgärder. Koordinatorer definierar schemat för när arbets flödet ska köras. Koordinatorer kan också vänta på tillgängligheten för nya data innan du startar en instans av arbets flödet.

Följande diagram visar den övergripande designen av det här exemplet Oozie pipeline.

![Oozie flyg exempel data pipeline](./media/hdinsight-operationalize-data-pipeline/pipeline-overview-oozie.png)

## <a name="provision-azure-resources"></a>Etablera Azure-resurser

Den här pipelinen kräver ett Azure SQL Database och ett HDInsight Hadoop-kluster på samma plats. Azure SQL Database lagrar både sammanfattnings data som skapats av pipelinen och Oozie metadata.

### <a name="provision-azure-sql-database"></a>Etablera Azure SQL Database

1. Skapa en Azure SQL Database. Se [skapa ett Azure SQL Database i Azure Portal](../azure-sql/database/single-database-create-quickstart.md).

1. För att se till att ditt HDInsight-kluster har åtkomst till den anslutna Azure SQL Database konfigurerar du Azure SQL Database brand Väggs regler för att tillåta Azure-tjänster och-resurser åtkomst till servern. Du kan aktivera det här alternativet i Azure Portal genom att välja **Ange server brand vägg** och välja **på** under **Tillåt Azure-tjänster och-resurser för att få åtkomst till den här servern** för Azure SQL Database. Mer information finns i [skapa och hantera IP-brandväggens regler](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

1. Använd [Frågeredigeraren](../azure-sql/database/single-database-create-quickstart.md#query-the-database) för att köra följande SQL-uttryck för att skapa den `dailyflights` tabell som ska lagra de sammanfattade data från varje körning av pipelinen.

    ```sql
    CREATE TABLE dailyflights
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER CHAR(2),
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    GO

    CREATE CLUSTERED INDEX dailyflights_clustered_index on dailyflights(YEAR,MONTH,DAY_OF_MONTH,CARRIER)
    GO
    ```

Din Azure SQL Database är nu klar.

### <a name="provision-an-apache-hadoop-cluster"></a>Etablera ett Apache Hadoop kluster

Skapa ett Apache Hadoop-kluster med en anpassad metaarkiv. När du skapar kluster från portalen, på fliken **lagring** , se till att du väljer SQL Database under **Inställningar för metaarkiv**. Mer information om hur du väljer en metaarkiv finns i [Välj en anpassad metaarkiv när du skapar klustret](./hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Mer information om hur du skapar kluster finns i [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="verify-ssh-tunneling-set-up"></a>Verifiera konfiguration av SSH-tunnlar

Om du vill använda Oozie-webbkonsolen för att visa status för koordinatorn och arbets flödes instanser konfigurerar du en SSH-tunnel till HDInsight-klustret. Mer information finns i [SSH-tunnel](hdinsight-linux-ambari-ssh-tunnel.md).

> [!NOTE]  
> Du kan också använda Chrome med [Foxy proxy](https://getfoxyproxy.org/) -tillägget för att söka i klustrets webb resurser i SSH-tunneln. Konfigurera den så att den proxy all begäran skickas via värden `localhost` i tunnelns port 9876. Den här metoden är kompatibel med Windows-undersystemet för Linux, även kallat bash i Windows 10.

1. Kör följande kommando för att öppna en SSH-tunnel till klustret, där `CLUSTERNAME` är namnet på klustret:

    ```cmd
    ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kontrol lera att tunneln fungerar genom att gå till Ambari på Head-noden genom att bläddra till:

    `http://headnodehost:8080`

1. Om du vill komma åt **Oozie-webbkonsolen** från Ambari går du till **Oozie**  >  **snabb länkar** > [Active Server] > **Oozie Web UI**.

## <a name="configure-hive"></a>Konfigurera Hive

### <a name="upload-data"></a>Ladda upp data

1. Hämta en exempel-CSV-fil som innehåller Flight-data för en månad. Hämta ZIP-filen `2017-01-FlightData.zip` från [HDInsight GitHub-lagringsplatsen](https://github.com/hdinsight/hdinsight-dev-guide) och zippa upp den till CSV-filen `2017-01-FlightData.csv` .

1. Kopiera CSV-filen till det Azure Storage konto som är kopplat till ditt HDInsight-kluster och placera det i `/example/data/flights` mappen.

    1. Använd SCP för att kopiera filerna från den lokala datorn till den lokala lagringen av din HDInsight-klusters huvud nod.

        ```cmd
        scp ./2017-01-FlightData.csv sshuser@CLUSTERNAME-ssh.azurehdinsight.net:2017-01-FlightData.csv
        ```

    1. Använd [SSH-kommandot](./hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret och ange sedan kommandot:

        ```cmd
        ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
        ```

    1. Från din SSH-session använder du kommandot HDFS för att kopiera filen från din Head-nods lokala lagring till Azure Storage.

        ```bash
        hadoop fs -mkdir /example/data/flights
        hdfs dfs -put ./2017-01-FlightData.csv /example/data/flights/2017-01-FlightData.csv
        ```

### <a name="create-tables"></a>Skapa tabeller

Exempel data är nu tillgängliga. Pipelinen kräver dock två Hive-tabeller för bearbetning, en för inkommande data ( `rawFlights` ) och en för sammanfattade data ( `flights` ). Skapa tabellerna i Ambari på följande sätt.

1. Logga in på Ambari genom att gå till `http://headnodehost:8080` .

2. I listan över tjänster väljer du **Hive**.

    ![Apache Ambari Services-lista Välj Hive](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive.png)

3. Välj **gå till vy** bredvid Hive-vyns 2,0-etikett.

    ![Ambari Apache Hive sammanfattnings lista](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-summary.png)

4. Klistra in följande instruktioner i fråge text avsnittet för att skapa `rawFlights` tabellen. `rawFlights`Tabellen innehåller en schema för läsning av CSV-filer i `/example/data/flights` mappen i Azure Storage.

    ```sql
    CREATE EXTERNAL TABLE IF NOT EXISTS rawflights (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    )
    LOCATION '/example/data/flights'
    ```

5. Välj **Kör** för att skapa tabellen.

    ![HDI Ambari Services Hive-fråga](./media/hdinsight-operationalize-data-pipeline/hdi-ambari-services-hive-query.png)

6. Om du vill skapa `flights` tabellen ersätter du texten i fråge textområdet med följande instruktioner. `flights`Tabellen är en Hive-hanterad tabell som partitionerar data som läses in i den efter år, månad och dag i månaden. Den här tabellen innehåller alla historiska flyg data, med den lägsta kornig het som finns i käll informationen för en rad per flygning.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    CREATE TABLE flights
    (
        FL_DATE STRING,
        CARRIER STRING,
        FL_NUM STRING,
        ORIGIN STRING,
        DEST STRING,
        DEP_DELAY FLOAT,
        ARR_DELAY FLOAT,
        ACTUAL_ELAPSED_TIME FLOAT,
        DISTANCE FLOAT
    )
    PARTITIONED BY (YEAR INT, MONTH INT, DAY_OF_MONTH INT)
    ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
    WITH SERDEPROPERTIES 
    (
        "separatorChar" = ",",
        "quoteChar"     = "\""
    );
    ```

7. Välj **Kör** för att skapa tabellen.

## <a name="create-the-oozie-workflow"></a>Skapa Oozie-arbetsflödet

Pipelines bearbetar vanligt vis data i batchar med ett angivet tidsintervall. I det här fallet bearbetar pipelinen flyg data varje dag. Den här metoden gör att CSV-filerna för indatafiler tas emot varje dag, varje vecka, varje månad eller varje år.

Exempel arbets flödet bearbetar flyg data dag för dag i tre viktiga steg:

1. Kör en Hive-fråga för att extrahera data för datum intervallet för den dagen från käll-CSV-filen som representeras av `rawFlights` tabellen och infoga data i `flights` tabellen.
2. Kör en Hive-fråga för att dynamiskt skapa en mellanlagringsplats i Hive för dagen, som innehåller en kopia av flyg data som sammanfattas per dag och operatör.
3. Använd Apache Sqoop för att kopiera alla data från den dagliga mellanlagrings tabellen i Hive till mål `dailyflights` tabellen i Azure SQL Database. Sqoop läser käll raderna från data bakom Hive-tabellen som finns i Azure Storage och läser in dem i SQL Database med hjälp av en JDBC-anslutning.

Dessa tre steg koordineras av ett Oozie-arbetsflöde.

1. Skapa en fil med namnet i den lokala arbets stationen `job.properties` . Använd texten nedan som start innehåll för filen.
Uppdatera sedan värdena för din speciella miljö. I tabellen nedanför texten sammanfattas alla egenskaper och det visar var du kan hitta värdena för din egen miljö.

    ```text
    nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
    jobTracker=[ACTIVERESOURCEMANAGER]:8050
    queueName=default
    oozie.use.system.libpath=true
    appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
    oozie.wf.application.path=${appBase}/load_flights_by_day
    hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
    hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
    hiveDailyTableName=dailyflights${year}${month}${day}
    hiveDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/${year}/${month}/${day}
    sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
    sqlDatabaseTableName=dailyflights
    year=2017
    month=01
    day=03
    ```

    | Egenskap | Värde källa |
    | --- | --- |
    | nameNode | Den fullständiga sökvägen till den Azure Storage behållare som är kopplad till ditt HDInsight-kluster. |
    | jobTracker | Det interna värd namnet för det aktiva klustrets garn huvud-nod. På Start sidan för Ambari väljer du garn i listan över tjänster och väljer sedan aktiv Resource Manager. Hostname-URI: n visas överst på sidan. Lägg till porten 8050. |
    | queueName | Namnet på den garn kö som används vid schemaläggning av Hive-åtgärder. Lämna som standard. |
    | oozie.use.system. lib-sökväg | Lämna som sant. |
    | Program bas | Sökvägen till undermappen i Azure Storage där du distribuerar arbets flödet för Oozie och stödfiler. |
    | Oozie. WF. Application. Path | Platsen för det Oozie-arbetsflöde `workflow.xml` som ska köras. |
    | hiveScriptLoadPartition | Sökvägen i Azure Storage till Hive-mallfilen `hive-load-flights-partition.hql` . |
    | hiveScriptCreateDailyTable | Sökvägen i Azure Storage till Hive-mallfilen `hive-create-daily-summary-table.hql` . |
    | hiveDailyTableName | Det dynamiskt genererade namnet som ska användas för mellanlagrings tabellen. |
    | hiveDataFolder | Sökvägen i Azure Storage till de data som finns i mellanlagrings tabellen. |
    | sqlDatabaseConnectionString | Anslutnings strängen för JDBC-syntax till din Azure SQL Database. |
    | sqlDatabaseTableName | Namnet på tabellen i Azure SQL Database som sammanfattnings raderna infogas i. Lämna som `dailyflights` . |
    | år | Års komponenten för den dag då flyg sammanfattningar beräknas. Lämna det som är. |
    | månad | Månads komponenten på den dag för vilken flyg sammanfattningar beräknas. Lämna det som är. |
    | day | Dag i månads komponenten på den dag för vilken flyg sammanfattningar beräknas. Lämna det som är. |

1. Skapa en fil med namnet i den lokala arbets stationen `hive-load-flights-partition.hql` . Använd koden nedan som filens innehåll.

    ```sql
    SET hive.exec.dynamic.partition.mode=nonstrict;

    INSERT OVERWRITE TABLE flights
    PARTITION (YEAR, MONTH, DAY_OF_MONTH)
    SELECT 
        FL_DATE,
        CARRIER,
        FL_NUM,
        ORIGIN,
        DEST,
        DEP_DELAY,
        ARR_DELAY,
        ACTUAL_ELAPSED_TIME,
        DISTANCE,
        YEAR,
        MONTH,
        DAY_OF_MONTH
    FROM rawflights
    WHERE year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Oozie variabler använder syntaxen `${variableName}` . Dessa variabler anges i `job.properties` filen. Oozie ersätter de faktiska värdena vid körning.

1. Skapa en fil med namnet i den lokala arbets stationen `hive-create-daily-summary-table.hql` . Använd koden nedan som filens innehåll.

    ```sql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}
    (
        YEAR INT,
        MONTH INT,
        DAY_OF_MONTH INT,
        CARRIER STRING,
        AVG_DEP_DELAY FLOAT,
        AVG_ARR_DELAY FLOAT,
        TOTAL_DISTANCE FLOAT
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName}
    SELECT  year, month, day_of_month, carrier, avg(dep_delay) avg_dep_delay, 
            avg(arr_delay) avg_arr_delay, sum(distance) total_distance 
    FROM flights
    GROUP BY year, month, day_of_month, carrier 
    HAVING year = ${year} AND month = ${month} AND day_of_month = ${day};
    ```

    Den här frågan skapar en mellanlagringsplats som endast lagrar de summerade data i en dag, och noterar den SELECT-instruktion som beräknar genomsnitts fördröjningar och totalt avstånds flöde per dag. De data som infogas i den här tabellen lagras på en känd plats (den sökväg som anges av variabeln hiveDataFolder) så att den kan användas som källa för Sqoop i nästa steg.

1. Skapa en fil med namnet i den lokala arbets stationen `workflow.xml` . Använd koden nedan som filens innehåll. De här stegen ovan uttrycks som separata åtgärder i Oozie-arbetsflöde.

    ```xml
    <workflow-app name="loadflightstable" xmlns="uri:oozie:workflow:0.5">
        <start to = "RunHiveLoadFlightsScript"/>
        <action name="RunHiveLoadFlightsScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptLoadPartition}</script>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
            </hive>
            <ok to="RunHiveCreateDailyFlightTableScript"/>
            <error to="fail"/>
        </action>
    
        <action name="RunHiveCreateDailyFlightTableScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScriptCreateDailyTable}</script>
                <param>hiveTableName=${hiveDailyTableName}</param>
                <param>year=${year}</param>
                <param>month=${month}</param>
                <param>day=${day}</param>
                <param>hiveDataFolder=${hiveDataFolder}/${year}/${month}/${day}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>
    
        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}/${year}/${month}/${day}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>
        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

De två Hive-frågorna används av sökvägen i Azure Storage och de återstående variabel värdena tillhandahålls av `job.properties` filen. Den här filen konfigurerar arbets flödet som ska köras för datumet 3 januari 2017.

## <a name="deploy-and-run-the-oozie-workflow"></a>Distribuera och köra Oozie-arbetsflödet

Använd SCP från bash-sessionen för att distribuera Oozie-arbetsflödet ( `workflow.xml` ), Hive-frågorna ( `hive-load-flights-partition.hql` och `hive-create-daily-summary-table.hql` ) och jobb konfigurationen ( `job.properties` ).  I Oozie `job.properties` kan bara filen finnas i den lokala lagringen av huvudnoden. Alla andra filer måste lagras i HDFS, i det här fallet Azure Storage. Sqoop-åtgärden som används av arbets flödet beror på en JDBC-drivrutin för kommunikation med din SQL Database, som måste kopieras från Head-noden till HDFS.

1. Skapa `load_flights_by_day` undermappen under användarens sökväg i den lokala lagringen av Head-noden. Kör följande kommando från din öppna SSH-session:

    ```bash
    mkdir load_flights_by_day
    ```

1. Kopiera alla filer i den aktuella katalogen ( `workflow.xml` filerna och `job.properties` ) upp till `load_flights_by_day` undermappen. Kör följande kommando från din lokala arbets Station:

    ```cmd
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:load_flights_by_day
    ```

1. Kopiera arbets flödes filen till HDFS. Kör följande kommandon från den öppna SSH-sessionen:

    ```bash
    cd load_flights_by_day
    hadoop fs -mkdir -p /oozie/load_flights_by_day
    hdfs dfs -put ./* /oozie/load_flights_by_day
    ```

1. Kopiera `mssql-jdbc-7.0.0.jre8.jar` från den lokala Head-noden till arbets flödes mappen i HDFS. Ändra kommandot efter behov om klustret innehåller en annan jar-fil. Ändra `workflow.xml` efter behov för att avspegla en annan jar-fil. Kör följande kommando från din öppna SSH-session:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /oozie/load_flights_by_day
    ```

1. Kör arbets flödet. Kör följande kommando från din öppna SSH-session:

    ```bash
    oozie job -config job.properties -run
    ```

1. Observera statusen med hjälp av webb konsolen för Oozie. I Ambari väljer du **Oozie**, **snabb länkar** och **Oozie webb konsol**. Under fliken **arbets flödes jobb** väljer du **alla jobb**.

    ![HDI Oozie webb konsol arbets flöden](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-workflows.png)

1. När statusen är klar frågar du SQL Database-tabellen om du vill visa de infogade raderna. Använd Azure Portal, navigera till fönstret för din SQL Database, Välj **verktyg** och öppna **Frågeredigeraren**.

    ```sql
    SELECT * FROM dailyflights
    ```

Nu när arbets flödet körs för den enskilda test dagen kan du omsluta det här arbets flödet med en koordinator som schemalägger arbets flödet så att det körs dagligen.

## <a name="run-the-workflow-with-a-coordinator"></a>Kör arbets flödet med en koordinator

Om du vill schemalägga det här arbets flödet så att det körs dagligen (eller alla dagar i ett datum intervall) kan du använda en koordinator. En koordinator definieras av en XML-fil, till exempel `coordinator.xml` :

```xml
<coordinator-app name="daily_export" start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" timezone="UTC" xmlns="uri:oozie:coordinator:0.4">
    <datasets>
        <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
            <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
            <done-flag></done-flag>
        </dataset>
    </datasets>
    <input-events>
        <data-in name="event_input1" dataset="ds_input1">
            <instance>${coord:current(0)}</instance>
        </data-in>
    </input-events>
    <action>
        <workflow>
            <app-path>${appBase}/load_flights_by_day</app-path>
            <configuration>
                <property>
                    <name>year</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'yyyy')}</value>
                </property>
                <property>
                    <name>month</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'MM')}</value>
                </property>
                <property>
                    <name>day</name>
                    <value>${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveScriptLoadPartition</name>
                    <value>${hiveScriptLoadPartition}</value>
                </property>
                <property>
                    <name>hiveScriptCreateDailyTable</name>
                    <value>${hiveScriptCreateDailyTable}</value>
                </property>
                <property>
                    <name>hiveDailyTableNamePrefix</name>
                    <value>${hiveDailyTableNamePrefix}</value>
                </property>
                <property>
                    <name>hiveDailyTableName</name>
                    <value>${hiveDailyTableNamePrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}${coord:formatTime(coord:nominalTime(), 'MM')}${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>hiveDataFolderPrefix</name>
                    <value>${hiveDataFolderPrefix}</value>
                </property>
                <property>
                    <name>hiveDataFolder</name>
                    <value>${hiveDataFolderPrefix}${coord:formatTime(coord:nominalTime(), 'yyyy')}/${coord:formatTime(coord:nominalTime(), 'MM')}/${coord:formatTime(coord:nominalTime(), 'dd')}</value>
                </property>
                <property>
                    <name>sqlDatabaseConnectionString</name>
                    <value>${sqlDatabaseConnectionString}</value>
                </property>
                <property>
                    <name>sqlDatabaseTableName</name>
                    <value>${sqlDatabaseTableName}</value>
                </property>
            </configuration>
        </workflow>
    </action>
</coordinator-app>
```

Som du kan se skickar majoriteten av koordinatorn bara konfigurations information till arbets flödes instansen. Det finns dock några viktiga objekt att anropa.

* Punkt 1: `start` `end` attributen och i `coordinator-app` själva elementet styr tidsintervallet som koordinatorn kör.

    ```
    <coordinator-app ... start="2017-01-01T00:00Z" end="2017-01-05T00:00Z" frequency="${coord:days(1)}" ...>
    ```

    En koordinator ansvarar för schemaläggnings åtgärder inom `start` och `end` datum intervallet, enligt intervallet som anges av `frequency` attributet. Varje schemalagd åtgärd i Turn kör arbets flödet enligt konfigurationen. I koordinator definitionen ovan är koordinatorn konfigurerad att köra åtgärder från 1 januari 2017 till 5 januari 2017. Frekvensen anges till en dag i uttryck för [Oozie-uttryckets språk](https://oozie.apache.org/docs/4.2.0/CoordinatorFunctionalSpec.html#a4.4._Frequency_and_Time-Period_Representation) frekvens `${coord:days(1)}` . Detta resulterar i att koordinatorn schemalägger en åtgärd (och därmed arbets flödet) en gång per dag. För datum intervall som redan har passerat, som i det här exemplet, kommer åtgärden att schemaläggas att köras utan fördröjning. Början på det datum då en åtgärd är schemalagd att köras kallas den *nominella tiden*. Om du till exempel vill bearbeta data för den 1 januari 2017 schemalägger koordinator åtgärden med en nominell tid på 2017-01-01T00:00:00 GMT.

* Punkt 2: inom arbets flödets datum intervall `dataset` anger elementet var du vill söka i HDFS för data för ett visst datum intervall och konfigurerar hur Oozie avgör om data är tillgängliga ännu för bearbetning.

    ```xml
    <dataset name="ds_input1" frequency="${coord:days(1)}" initial-instance="2016-12-31T00:00Z" timezone="UTC">
        <uri-template>${sourceDataFolder}${YEAR}-${MONTH}-FlightData.csv</uri-template>
        <done-flag></done-flag>
    </dataset>
    ```

    Sökvägen till data i HDFS skapas dynamiskt enligt uttrycket som anges i `uri-template` elementet. I den här koordinatorn används även en frekvens på en dag med data uppsättningen. Start-och slutdatumen för koordinator elementet styr när åtgärderna schemaläggs (och definierar deras nominella tider), `initial-instance` och `frequency` på data uppsättningen styr beräkningen av det datum som används för att skapa `uri-template` . I det här fallet ställer du in den första instansen på en dag innan koordinatorn börjar med att se till att den första dagen är data källa (1/1/2017). Datum beräkningen för data uppsättningen rullar framåt från värdet för `initial-instance` (12/31/2016) i steg om data uppsättnings frekvens (en dag) tills den hittar det senaste datumet som inte klarar den nominella tid som angetts av koordinatorn (2017-01-01T00:00:00 GMT för den första åtgärden).

    Det tomma `done-flag` elementet anger att när Oozie söker efter indata vid den angivna tiden, identifierar Oozie data om de är tillgängliga i närvaro av en katalog eller fil. I det här fallet är det förekomsten av en CSV-fil. Om det finns en CSV-fil antar Oozie att data är klara och startar en arbets flödes instans för att bearbeta filen. Om det inte finns någon CSV-fil, förutsätter Oozie att data ännu inte är klara och att körningen av arbets flödet försätts i ett vänte läge.

* Punkt 3: `data-in` elementet anger den särskilda tidstämpeln som ska användas som den nominella tiden när värdena i `uri-template` för den associerade data mängden ersätts.

    ```xml
    <data-in name="event_input1" dataset="ds_input1">
        <instance>${coord:current(0)}</instance>
    </data-in>
    ```

    I det här fallet ställer du in instansen till uttrycket `${coord:current(0)}` , som översätts till att använda den nominella tiden för åtgärden som ursprungligen schemalagts av koordinatorn. Med andra ord, när koordinatorn schemalägger åtgärden att köras med en nominell tid på 01/01/2017, så är 01/01/2017 det som används för att ersätta år (2017) och månad (01) variabler i URI-mallen. När URI-mallen har beräknats för den här instansen kontrollerar Oozie om den förväntade katalogen eller filen är tillgänglig och schemalägger nästa körning av arbets flödet.

De tre föregående punkterna kombinerar för att ge en situation där koordinatorn schemalägger bearbetningen av data källan på en dag per dag.

* Punkt 1: koordinatorn börjar med ett nominellt datum på 2017-01-01.

* Punkt 2: Oozie letar efter tillgängliga data i `sourceDataFolder/2017-01-FlightData.csv` .

* Punkt 3: när Oozie hittar filen schemaläggs en instans av arbets flödet som bearbetar data för 2017-01-01. Oozie fortsätter sedan att bearbeta för 2017-01-02. Den här utvärderingen upprepas till men inte inklusive 2017-01-05.

Precis som med arbets flöden definieras konfigurationen för en koordinator i en `job.properties` fil som har en supermängd av de inställningar som används av arbets flödet.

```text
nameNode=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net
jobTracker=[ACTIVERESOURCEMANAGER]:8050
queueName=default
oozie.use.system.libpath=true
appBase=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie
oozie.coord.application.path=${appBase}
sourceDataFolder=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/
hiveScriptLoadPartition=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-load-flights-partition.hql
hiveScriptCreateDailyTable=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/oozie/load_flights_by_day/hive-create-daily-summary-table.hql
hiveDailyTableNamePrefix=dailyflights
hiveDataFolderPrefix=wasbs://[CONTAINERNAME]@[ACCOUNTNAME].blob.core.windows.net/example/data/flights/day/
sqlDatabaseConnectionString="jdbc:sqlserver://[SERVERNAME].database.windows.net;user=[USERNAME];password=[PASSWORD];database=[DATABASENAME]"
sqlDatabaseTableName=dailyflights
```

De enda nya egenskaperna som introduceras i den här `job.properties` filen är:

| Egenskap | Värde källa |
| --- | --- |
| Oozie. Coord. Application. Path | Anger platsen för den `coordinator.xml` fil som innehåller Oozie-koordinatorn som ska köras. |
| hiveDailyTableNamePrefix | Det prefix som används när tabell namnet för mellanlagrings tabellen skapas dynamiskt. |
| hiveDataFolderPrefix | Prefixet för den sökväg där alla mellanlagrings tabeller kommer att lagras. |

## <a name="deploy-and-run-the-oozie-coordinator"></a>Distribuera och köra Oozie-koordinatorn

Om du vill köra pipelinen med en koordinator fortsätter du på liknande sätt som för arbets flödet, förutom att du arbetar från en mapp på en nivå ovanför mappen som innehåller ditt arbets flöde. Denna mappsökväg separerar koordinatorerna från arbets flöden på disk, så att du kan associera en koordinator med olika underordnade arbets flöden.

1. Använd SCP från den lokala datorn för att kopiera koordinator filerna upp till den lokala lagringen av Head-noden i klustret.

    ```bash
    scp ./* sshuser@CLUSTERNAME-ssh.azurehdinsight.net:~
    ```

2. SSH till Head-noden.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Kopiera koordinator-filerna till HDFS.

    ```bash
    hdfs dfs -put ./* /oozie/
    ```

4. Kör koordinatorn.

    ```bash
    oozie job -config job.properties -run
    ```

5. Kontrol lera statusen med Oozie-webbkonsolen, den här gången väljer du fliken **koordinator jobb** och sedan  **alla jobb**.

    ![Oozie-webbkonsolens koordinator jobb](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-jobs.png)

6. Välj en koordinator instans för att visa listan över schemalagda åtgärder. I så fall bör du se fyra åtgärder med nominella tider i intervallet från 1/1/2017 till 1/4/2017.

    ![Oozie-webbkonsol koordinator jobb](./media/hdinsight-operationalize-data-pipeline/hdi-oozie-web-console-coordinator-instance.png)

    Varje åtgärd i den här listan motsvarar en instans av arbets flödet som bearbetar en dags värde, där början av dagen anges av den nominella tiden.

## <a name="next-steps"></a>Nästa steg

[Apache Oozie-dokumentation](https://oozie.apache.org/docs/4.2.0/index.html)
