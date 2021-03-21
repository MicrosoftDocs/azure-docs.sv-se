---
title: Apache Oozie-arbetsflöden & Enterprise Security – Azure HDInsight
description: Skydda Apache Oozie-arbetsflöden med Azure HDInsight Enterprise Security Package. Lär dig hur du definierar ett Oozie-arbetsflöde och skickar ett Oozie-jobb.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: dc20a258028bb76351ae70362234e7c4c8c741a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699410"
---
# <a name="run-apache-oozie-in-azure-hdinsight-clusters-with-enterprise-security-package"></a>Kör Apache Oozie i Azure HDInsight-kluster med Enterprise Security Package

Apache Oozie är ett arbets flödes-och samordnings system som hanterar Apache Hadoop-jobb. Oozie är integrerat med Hadoop-stacken och stöder följande jobb:

- Apache MapReduce
- Apache gris
- Apache Hive
- Apache Sqoop

Du kan också använda Oozie för att schemalägga jobb som är speciella för ett system, t. ex. Java-program eller Shell-skript.

## <a name="prerequisite"></a>Förutsättning

Ett Azure HDInsight Hadoop kluster med Enterprise Security Package (ESP). Se [Konfigurera HDInsight-kluster med ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Detaljerade anvisningar om hur du använder Oozie på icke-ESP-kluster finns i [använda Apache Oozie-arbetsflöden i Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Ansluta till ett ESP-kluster

Mer information om SSH (Secure Shell) finns i [ansluta till HDInsight (Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Anslut till HDInsight-klustret med hjälp av SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Verifiera lyckad Kerberos-autentisering med hjälp av `klist` kommandot. Om inte, använder `kinit` du för att starta Kerberos-autentisering.

1. Logga in på HDInsight-gatewayen för att registrera OAuth-token som krävs för att få åtkomst till Azure Data Lake Storage:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    En status svars kod på **200 OK** visar att registreringen lyckades. Kontrol lera användar namnet och lösen ordet om ett obehörigt svar tas emot, till exempel 401.

## <a name="define-the-workflow"></a>Definiera arbets flödet

Oozie för arbets flödes definitioner skrivs i Apache Hadoop process Definition Language (hPDL). hPDL är ett XML-process definitions språk. Utför följande steg för att definiera arbets flödet:

1. Konfigurera en domän användares arbets yta:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Ersätt `DomainUser` med domänens användar namn.
   Ersätt `DomainUserPath` med sökvägen till arbets katalogen för domän användaren.
   Ersätt `ClusterVersion` med din kluster data plattforms version.

2. Använd följande instruktion för att skapa och redigera en ny fil:

   ```bash
   nano workflow.xml
   ```

3. När nano-redigeraren öppnas anger du följande XML som fil innehåll:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
    ```

4. Ersätt `clustername` med namnet på klustret.

5. Om du vill spara filen väljer du **CTRL + X**. Ange **Y**. Välj sedan **RETUR**.

    Arbets flödet är uppdelat i två delar:

   - **Certifiering.** Det här avsnittet tar med de autentiseringsuppgifter som används för att autentisera Oozie-åtgärder:

     I det här exemplet används autentisering för Hive-åtgärder. Mer information finns i [Action Authentication](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     Med tjänsten autentiseringsuppgifter kan Oozie-åtgärder personifiera användaren för att komma åt Hadoop-tjänster.

   - **Tgärd.** Det här avsnittet innehåller tre åtgärder: Map-reducera, Hive Server 2 och Hive Server 1:

     - Åtgärden mappa – minska kör ett exempel från ett Oozie-paket för kart minskning som utvärderar det sammanställda antalet ord.

     - Hive Server 2-och Hive Server 1-åtgärder kör en fråga i en Hive-tabell med HDInsight.

     Hive-åtgärderna använder de autentiseringsuppgifter som definierats i avsnittet autentiseringsuppgifter för autentisering med hjälp av nyckelordet `cred` i åtgärds elementet.

6. Använd följande kommando för att kopiera `workflow.xml` filen till `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` :

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Ersätt `domainuser` med ditt användar namn för domänen.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definiera egenskaps filen för Oozie-jobbet

1. Använd följande instruktion för att skapa och redigera en ny fil för jobb egenskaper:

    ```bash
    nano job.properties
    ```

2. När nano-redigeraren öppnas använder du följande XML som filens innehåll:

   ```bash
   nameNode=adl://home
   jobTracker=headnodehost:8050
   queueName=default
   examplesRoot=examples
   oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
   hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
   hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
   oozie.use.system.libpath=true
   user.name=[domainuser]
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Använd `adl://home` URI för `nameNode` egenskapen om du har Azure Data Lake Storage gen1 som primär kluster lagring. Om du använder Azure Blob Storage ändrar du till `wasb://home` . Om du använder Azure Data Lake Storage Gen2 ändrar du till `abfs://home` .
   - Ersätt `domainuser` med ditt användar namn för domänen.
   - Ersätt `ClusterShortName` med det korta namnet för klustret. Om kluster namnet till exempel är https:// *[exempel länk]* sechadoopcontoso.azurehdisnight.net, `clustershortname` är de första sex tecknen i klustret: **sechad**.
   - Ersätt `jdbcurlvalue` med JDBC-URL: en från Hive-konfigurationen. Ett exempel är JDBC: hive2://headnodehost: 10001/; transportMode = http.
   - Om du vill spara filen väljer du Ctrl + X, anger `Y` och väljer sedan **RETUR**.

   Den här egenskaps filen måste finnas lokalt när du kör Oozie-jobb.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Skapa anpassade Hive-skript för Oozie-jobb

Du kan skapa de två Hive-skripten för Hive Server 1 och Hive Server 2, som du ser i följande avsnitt.

### <a name="hive-server-1-file"></a>Hive Server 1-fil

1. Skapa och redigera en fil för Hive Server 1-åtgärder:

    ```bash
    nano countrowshive1.hql
    ```

2. Skapa skriptet:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Spara filen till Apache Hadoop Distributed File System (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive Server 2-fil

1. Skapa och redigera ett fält för Hive Server 2-åtgärder:

    ```bash
    nano countrowshive2.hql
    ```

2. Skapa skriptet:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Spara filen till HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Skicka Oozie-jobb

Sändning av Oozie-jobb för ESP-kluster är som att skicka Oozie-jobb i icke-ESP-kluster.

Mer information finns i [använda Apache Oozie med Apache Hadoop för att definiera och köra ett arbets flöde på Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Resultat från ett Oozie jobb

Oozie-jobb körs för användaren. Både Apache Hadoop garn och Apache Rangers gransknings loggar visar de jobb som körs som den personifierade användaren. Kommando rads gränssnittets utdata för ett Oozie-jobb ser ut som följande kod:

```output
Job ID : 0000015-180626011240801-oozie-oozi-W
------------------------------------------------------------------------------------------------
Workflow Name : map-reduce-wf
App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
Status        : SUCCEEDED
Run           : 0
User          : alicetest
Group         : -
Created       : 2018-06-26 19:25 GMT
Started       : 2018-06-26 19:25 GMT
Last Modified : 2018-06-26 19:30 GMT
Ended         : 2018-06-26 19:30 GMT
CoordAction ID: -

Actions
------------------------------------------------------------------------------------------------
ID                        Status    Ext ID            ExtStatus                 ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK    -                         OK             -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK    job_1529975666160_0051    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK    job_1529975666160_0053    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive    OK     job_1529975666160_0055    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end       OK     -                         OK             -
-----------------------------------------------------------------------------------------------
```

Ranger-gransknings loggarna för Hive Server 2-åtgärder visar Oozie som kör åtgärden för användaren. Vyerna Ranger och garn visas bara i kluster administratören.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurera användarauktorisering i Oozie

Oozie har en konfiguration för användarautentisering som kan blockera användare från att stoppa eller ta bort andra användares jobb. Om du vill aktivera den här konfigurationen ställer du in `oozie.service.AuthorizationService.security.enabled` på `true` . 

Mer information finns i avsnittet om [installation och konfiguration av Apache-Oozie](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

För komponenter som Hive-Server 1 där Ranger-plugin-programmet inte är tillgängligt eller stöds, är det bara en grov kornig HDFS-auktorisering. Detaljerad auktorisering är endast tillgängligt via Ranger-plugin-program.

## <a name="get-the-oozie-web-ui"></a>Hämta Oozie-webbgränssnittet

Oozie-webbgränssnittet innehåller en webbaserad vy över statusen för Oozie-jobb i klustret. För att hämta webb gränssnittet, utför följande steg i ESP-kluster:

1. Lägg till en [Edge-nod](../hdinsight-apps-use-edge-node.md) och aktivera [SSH Kerberos-autentisering](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Följ stegen i [Oozie-WEBBgränssnittet](../hdinsight-use-oozie-linux-mac.md) för att aktivera SSH-tunnlar till Edge-noden och få åtkomst till webb gränssnittet.

## <a name="next-steps"></a>Nästa steg

- [Använd Apache Oozie med Apache Hadoop för att definiera och köra ett arbets flöde på Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).
- [Anslut till HDInsight (Apache Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
