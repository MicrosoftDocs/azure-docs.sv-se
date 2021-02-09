---
title: Åtkomst Apache Hadoop garn program loggar – Azure HDInsight
description: Lär dig hur du kommer åt garn program loggar i ett Linux-baserat HDInsight-kluster (Apache Hadoop) med både kommando raden och en webbläsare.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 882384b5e57db27cff981f80e790dfd41b624c93
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980702"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Åtkomst Apache Hadoop garn program loggar på Linux-baserade HDInsight

Lär dig hur du kommer åt loggarna för [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (ännu andra resurs Negotiator) i ett Apache Hadoop kluster i Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Vad är Apache-garn?

GARN stöder flera programmerings modeller (Apache Hadoop MapReduce är en av dem) genom att koppla från resurs hantering från program schemaläggning/övervakning. GARN använder en global *`ResourceManager`* (RM), per Work-Node- *) nodemanagers* (NMS) och per-program- *ApplicationMasters* (AMS). Per program förhandlar om resurser (CPU, minne, disk, nätverk) för att köra ditt program med RM. RM fungerar med NMs för att tilldela dessa resurser, vilka beviljas som *behållare*. FM ansvarar för att spåra förloppet för de behållare som tilldelas den av RM. Ett program kan kräva många behållare beroende på programmets beskaffenhet.

Varje program kan bestå av flera *program försök*. Om ett program Miss lyckas kan det göras ett nytt försök. Varje försök körs i en behållare. I en mening tillhandahåller en behållare kontexten för grundläggande arbets enheter som har utförts av ett garn program. Allt arbete som utförs inom ramen för en behållare görs på den enskild arbetsnoden där behållaren angavs. Se [Hadoop: skriva garn program](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)eller [Apache Hadoop garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) för ytterligare referens.

Om du vill skala klustret så att det stöder större bearbetning av data flödet kan du använda [autoskalning](hdinsight-autoscale-clusters.md) eller [skala dina kluster manuellt med några olika språk](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>GARN tids linje server

[Apache HADOOP garn tids linje server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) innehåller allmän information om slutförda program

GARN tids linje server innehåller följande typ av data:

* Program-ID, en unik identifierare för ett program
* Användaren som startade programmet
* Information om försök som gjorts för att slutföra programmet
* De behållare som används av ett angivet program försök

## <a name="yarn-applications-and-logs"></a>GARN program och loggar

Program loggar (och tillhör ande behållar loggar) är viktiga vid fel sökning av problematiska Hadoop-program. GARN är ett bra ramverk för att samla in, aggregera och lagra program loggar med logg agg regering.

Funktionen för insamling av loggar ger åtkomst till program loggar mer deterministisk. Den sammanställer loggar över alla behållare på en arbetsnoden och lagrar dem som en sammanställd loggfil per arbets nod. Loggen lagras i standard fil systemet när ett program har slutförts. Ditt program kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnoden sammanställs alltid i en enda fil. Så det finns bara 1 logg per arbetsnoden som används av ditt program. Logg agg regering är aktiverat som standard i HDInsight-kluster version 3,0 och senare. Sammanställda loggar finns i standard lagrings utrymmet för klustret. Följande sökväg är HDFS-sökvägen till loggarna:

```
/app-logs/<user>/logs/<applicationId>
```

I sökvägen `user` är namnet på den användare som startade programmet. `applicationId`Är den unika identifierare som tilldelas ett program av garn RM.

De sammanställda loggarna går inte att läsa direkt, eftersom de skrivs i ett TFile, binärt format som indexeras av container. Använd form av garn `ResourceManager` loggar eller CLI-verktyg för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

## <a name="yarn-logs-in-an-esp-cluster"></a>Garn loggar i ett ESP-kluster

Två konfigurationer måste läggas till i den anpassade `mapred-site` i Ambari.

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret.

1. I Ambari-användargränssnittet navigerar du till **MapReduce2**  >  **configs**  >  **Advanced**  >  **Custom mapred-site**.

1. Lägg till *någon* av följande uppsättningar med egenskaper:

    **Ange 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Ange 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Spara ändringar och starta om alla berörda tjänster.

## <a name="yarn-cli-tools"></a>GARN CLI-verktyg

1. Använd [SSH-kommandot](./hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Visa en lista med alla program-ID: n för de kopplings program som körs för närvarande med följande kommando:

    ```bash
    yarn top
    ```

    Notera program-ID: t från `APPLICATIONID` kolumnen vars loggar ska laddas ned.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Du kan visa dessa loggar som oformaterad text genom att köra något av följande kommandon:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Ange &lt; applicationId->, &lt; användare-som-startat-program>, &lt; containerId> och &lt; Work-Node-Address> information när du kör dessa kommandon.

### <a name="other-sample-commands"></a>Andra exempel kommandon

1. Ladda ned garn behållare loggar för alla program hanterare med kommandot nedan. Det här steget skapar logg filen med namnet `amlogs.txt` i text format.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Hämta endast garn behållar loggar för den senaste program-huvud versionen med följande kommando:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Ladda ned garn behållar loggar för de första två program huvud med följande kommando:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Hämta alla garn container loggar med följande kommando:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Hämta en garn behållar logg för en viss behållare med följande kommando:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>GARN `ResourceManager` gränssnitt

GARN `ResourceManager` gränssnittet körs på klustrets huvudnoden. Den nås via Ambari-webbgränssnittet. Använd följande steg för att Visa garn loggarna:

1. Navigera till i webbläsaren `https://CLUSTERNAME.azurehdinsight.net` . Ersätt KLUSTERNAMN med namnet på ditt HDInsight-kluster.

2. I listan över tjänster till vänster väljer du **garn**.

    ![Apache Ambari garn-tjänsten vald](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. I list rutan **snabb länkar** väljer du en av klustrets huvud-noder och väljer sedan **`ResourceManager Log`** .

    ![Snabb Länkar för Apache Ambari garn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    En lista med länkar till garn loggar visas.

## <a name="next-steps"></a>Nästa steg

* [Apache Hadoop-arkitektur i HDInsight](hdinsight-hadoop-architecture.md)
* [Felsöka Apache Hadoop YARN med Azure HDInsight](hdinsight-troubleshoot-yarn.md)
