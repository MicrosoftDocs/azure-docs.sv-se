---
title: Skala klusterstorlekar – Azure HDInsight
description: Skala ett Apache Hadoop-kluster elastiskt för att matcha din arbetsbelastning i Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1c388cb070c66fc3a2322c358bc4113ed2106c77
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761857"
---
# <a name="scale-azure-hdinsight-clusters"></a>Skala Azure HDInsight kluster

HDInsight ger elasticitet med alternativ för att skala upp och skala ned antalet arbetsnoder i dina kluster. Den här elasticiteten gör att du kan krympa ett kluster efter timmar eller på helger. Och expandera den vid höga affärsbehov.

Skala upp klustret innan periodisk batchbearbetning så att klustret har tillräckliga resurser.  När bearbetningen är klar och användningen går ned skalar du ned HDInsight-klustret till färre arbetsnoder.

Du kan skala ett kluster manuellt med någon av metoderna som beskrivs nedan. Du kan också använda [autoskalningsalternativ](hdinsight-autoscale-clusters.md) för att automatiskt skala upp och ned som svar på vissa mått.

> [!NOTE]  
> Endast kluster med HDInsight version 3.1.3 eller senare stöds. Om du är osäker på vilken version av klustret du har kan du gå till sidan Egenskaper.

## <a name="utilities-to-scale-clusters"></a>Verktyg för att skala kluster

Microsoft tillhandahåller följande verktyg för att skala kluster:

|Verktyg | Beskrivning|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az_hdinsight_resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Klassisk Azure CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure-portalen](https://portal.azure.com)|Öppna HDInsight-klusterfönstret,  välj Klusterstorlek på menyn till vänster och skriv sedan antalet arbetsnoder i fönstret Klusterstorlek och välj Spara.|  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Azure Portal för skalningskluster":::

Med någon av dessa metoder kan du skala upp eller ned HDInsight-klustret inom några minuter.

> [!IMPORTANT]  
> * Klassiska Azure CLI är inaktuellt och bör endast användas med den klassiska distributionsmodellen. För alla andra distributioner använder du [Azure CLI](/cli/azure/).
> * PowerShell AzureRM-modulen är inaktuell.  Använd [Az-modulen när](/powershell/azure/new-azureps-module-az) det är möjligt.

## <a name="impact-of-scaling-operations"></a>Effekt av skalningsåtgärder

När du **lägger** till noder i det HDInsight-kluster som körs (skala upp) påverkas inte jobben. Nya jobb kan skickas på ett säkert sätt medan skalningsprocessen körs. Om skalningsåtgärden misslyckas lämnar felet klustret i ett funktionellt tillstånd.

Om du **tar** bort noder (skala ned) misslyckas väntande eller pågående jobb när skalningsåtgärden har slutförts. Det här felet beror på att vissa tjänster startas om under skalningsprocessen. Klustret kan fastna i felsäkert läge under en manuell skalningsåtgärd.

Effekten av att ändra antalet datanoder varierar för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop

    Du kan sömlöst öka antalet arbetsnoder i ett Hadoop-kluster som körs utan att påverka några jobb. Nya jobb kan också skickas medan åtgärden pågår. Fel i en skalningsåtgärd hanteras korrekt. Klustret lämnas alltid i ett funktionellt tillstånd.

    När ett Hadoop-kluster skalas ned med färre datanoder startas vissa tjänster om. Det här beteendet gör att alla jobb som körs och väntar misslyckas när skalningsåtgärden har slutförts. Du kan dock skicka jobben igen när åtgärden har slutförts.

* Apache HBase

    Du kan sömlöst lägga till eller ta bort noder i ditt HBase-kluster medan det körs. Regionala servrar balanseras automatiskt inom några minuter efter att skalningsåtgärden har avslutats. Du kan dock balansera de regionala servrarna manuellt. Logga in på klustrets huvudnod och kör följande kommandon:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Mer information om hur du använder HBase-gränssnittet finns i [Kom igång med ett Apache HBase-exempel i HDInsight.](hbase/apache-hbase-tutorial-get-started-linux.md)

* Apache Storm

    Du kan sömlöst lägga till eller ta bort datanoder när Storm körs. Men när skalningsåtgärden har slutförts måste du balansera om topologin. Ombalansering gör att topologin kan justera [parallellitetsinställningarna](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) baserat på det nya antalet noder i klustret. Om du vill balansera om topologier som körs använder du något av följande alternativ:

  * Storm-webbgränssnitt

    Använd följande steg för att balansera om en topologi med hjälp av Storm-användargränssnittet.

    1. Öppna `https://CLUSTERNAME.azurehdinsight.net/stormui` i webbläsaren, där `CLUSTERNAME` är namnet på Ditt Storm-kluster. Om du uppmanas till det anger du namnet och lösenordet för HDInsight-klusteradministratören (admin) som du angav när du skapade klustret.

    1. Välj den topologi som du vill balansera om och välj **sedan knappen Balansera** om. Ange fördröjningen innan ombalanseringsåtgärden är klar.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="Ombalansering av HDInsight Storm-skalning":::

  * Kommandoradsgränssnittsverktyg (CLI)

    Anslut till servern och använd följande kommando för att balansera om en topologi:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Du kan också ange parametrar för att åsidosätta parallellitetstipsen som ursprungligen tillhandahölls av topologin. Koden nedan konfigurerar till exempel om topologin till 5 arbetsprocesser, 3 utförare för komponenten blue-spout och 10 utförare för `mytopology` komponenten yellow-bolt.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Du bör balansera om partitionsrepliker efter skalningsåtgärder. Mer information finns i dokumentet [Hög tillgänglighet för data med Apache Kafka i HDInsight.](./kafka/apache-kafka-high-availability.md)

* Apache Hive LLAP

    Efter skalning till `N` arbetsnoder ställer HDInsight automatiskt in följande konfigurationer och startar om Hive.

  * Maximalt antal samtidiga frågor: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Antal noder som används av Hives LLAP: `num_llap_nodes  = N`
  * Antal noder för att köra Hive LLAP-daemon: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Så här skalar du ned ett kluster på ett säkert sätt

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skala ned ett kluster med jobb som körs

Om du vill undvika att jobben som körs misslyckas under en nedskalningsåtgärd kan du prova tre saker:

1. Vänta tills jobben har slutförts innan du skalar ned klustret.
1. Avsluta jobben manuellt.
1. Skicka jobben igen när skalningsåtgärden har avslutats.

Om du vill se en lista över väntande och körande jobb kan du använda YARN **Resource Manager användargränssnitt** genom att följa dessa steg:

1. Från [Azure Portal](https://portal.azure.com/)väljer du klustret.  Klustret öppnas på en ny portalsida.
2. Från huvudvyn går du till **Ambari-startsidan för**  >  **klusterinstrumentpaneler.** Ange autentiseringsuppgifterna för klustret.
3. I Ambari-användargränssnittet **väljer du YARN** i listan över tjänster på den vänstra menyn.  
4. Från YARN-sidan väljer du **Snabblänkar och** hovrar över den aktiva huvudnoden och väljer **sedan Resource Manager användargränssnitt.**

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Apache Ambari-snabblänkar Resource Manager användargränssnitt":::

Du kan komma åt Resource Manager användargränssnittet direkt med `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

Du ser en lista över jobb, tillsammans med deras aktuella tillstånd. På skärmbilden körs ett jobb:

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Resource Manager användargränssnittsprogram":::

Om du vill köra programmet manuellt kör du följande kommando från SSH-gränssnittet:

```bash
yarn application -kill <application_id>
```

Exempel:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Fastna i felsäkert läge

När du skalar ned ett kluster använder HDInsight Apache Ambari-hanteringsgränssnitt för att först inaktivera de extra arbetsnoderna. Noderna replikerar sina HDFS-block till andra arbetsnoder online. Därefter skalar HDInsight klustret ned på ett säkert sätt. HDFS förs i felsäkert läge under skalningsåtgärden. HDFS ska komma ut när skalningen är klar. Men i vissa fall fastnar HDFS i felsäkert läge under en skalningsåtgärd på grund av underreplikering av filblock.

Som standard konfigureras HDFS med inställningen 1, som styr hur många `dfs.replication` kopior av varje filblock som är tillgängliga. Varje kopia av ett filblock lagras på en annan nod i klustret.

När det förväntade antalet blockkopior inte är tillgängligt går HDFS in i felsäkert läge och Ambari genererar aviseringar. HDFS kan gå in i felsäkert läge för en skalningsåtgärd. Klustret kan fastna i felsäkert läge om det nödvändiga antalet noder inte har identifierats för replikering.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exempelfel när felsäkert läge är aktiverat

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Du kan granska namnnodloggarna från mappen, nära den tidpunkt då klustret skalades, för att se när `/var/log/hadoop/hdfs/` det gick in i felsäkert läge. Loggfilerna heter `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

Rotorsaken var att Hive är beroende av temporära filer i HDFS när frågor körs. När HDFS går in i felsäkert läge kan Hive inte köra frågor eftersom det inte kan skriva till HDFS. Temporära filer i HDFS finns på den lokala enheten som monterats på de enskilda virtuella arbetsnoderna. Filerna replikeras minst mellan andra arbetsnoder med tre repliker.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Så här förhindrar du att HDInsight fastnar i felsäkert läge

Det finns flera sätt att förhindra att HDInsight lämnas i felsäkert läge:

* Stoppa alla Hive-jobb innan du skalar ned HDInsight. Du kan också schemalägga nedskalningsprocessen för att undvika konflikter med hive-jobb som körs.
* Rensa bort Hives `tmp` scratch-katalogfiler manuellt i HDFS innan du skalar ned.
* Skala bara ned HDInsight till minst tre arbetsnoder. Undvik att gå så lågt som en arbetsnod.
* Kör kommandot för att lämna felsäkert läge om det behövs.

I följande avsnitt beskrivs dessa alternativ.

#### <a name="stop-all-hive-jobs"></a>Stoppa alla Hive-jobb

Stoppa alla Hive-jobb innan du skalar ned till en arbetsnod. Om din arbetsbelastning har schemalagts kör du nedskalningsarbetet när Hive-arbetet är klart.

Genom att stoppa Hive-jobben före skalning kan du minimera antalet scratch-filer i tmp-mappen (om det finns några).

#### <a name="manually-clean-up-hives-scratch-files"></a>Rensa Hives scratch-filer manuellt

Om Hive har lämnat tillfälliga filer kvar kan du rensa filerna manuellt innan du skalar ned för att undvika felsäkert läge.

1. Kontrollera vilken plats som används för tillfälliga Hive-filer genom att titta på `hive.exec.scratchdir` konfigurationsegenskapen. Den här parametern anges i `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Stoppa Hive-tjänster och se till att alla frågor och jobb har slutförts.

1. Visa en lista över innehållet i den scratch-katalog som finns `hdfs://mycluster/tmp/hive/` ovan för att se om det innehåller några filer:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Här är ett exempel på utdata när det finns filer:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Om du vet att Hive är klar med de här filerna kan du ta bort dem. Se till att Hive inte har några frågor som körs genom att titta på yarn-Resource Manager användargränssnittssidan.

    Exempel på kommandorad för att ta bort filer från HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skala HDInsight till tre eller flera arbetsnoder

Om klustren ofta fastnar i felsäkert läge när du skalar ned till färre än tre arbetsnoder behåller du minst tre arbetsnoder.

Det är dyrare att ha tre arbetsnoder än att bara skala ned till en arbetsnod. Den här åtgärden förhindrar dock att klustret fastnar i felsäkert läge.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Skala ned HDInsight till en arbetsnod

Även om klustret skalas ned till en nod kommer arbetsnod 0 fortfarande att överleva. Arbetsnod 0 kan aldrig inaktiveras.

#### <a name="run-the-command-to-leave-safe-mode"></a>Kör kommandot för att lämna felsäkert läge

Det sista alternativet är att köra kommandot lämna felsäkert läge. Om HDFS övergick i felsäkert läge på grund av Hive-fil under replikering kör du följande kommando för att lämna felsäkert läge:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skala ned ett Apache HBase-kluster

Regionservrar balanseras automatiskt inom några minuter efter att en skalningsåtgärd har slutförts. Om du vill balansera regionservrar manuellt utför du följande steg:

1. Anslut till HDInsight-klustret med hjälp av SSH. Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Starta HBase-gränssnittet:

    ```bash
    hbase shell
    ```

3. Använd följande kommando för att balansera regionservrarna manuellt:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Nästa steg

* [Skala Azure HDInsight-kluster automatiskt](hdinsight-autoscale-clusters.md)

Specifik information om hur du skalar ditt HDInsight-kluster finns i:

* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)