---
title: Skala kluster storlekar – Azure HDInsight
description: Skala ett Apache Hadoop kluster elastiskt för att matcha din arbets belastning i Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 6e6c692e8fc13d1703df44c99e9969ba4db5f119
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872106"
---
# <a name="scale-azure-hdinsight-clusters"></a>Skala Azure HDInsight-kluster

HDInsight ger elastiskhet med alternativ för att skala upp och ned antalet arbetsnoder i klustren. Med den här elastiskheten kan du minska ett kluster efter timmar eller helger. Och expandera den under topp verksamhets kraven.

Skala upp klustret före periodisk batchbearbetning så att klustret har tillräckligt med resurser.  När bearbetningen är klar och användningen går nedåt, skala HDInsight-klustret till färre arbetsnoder.

Du kan skala ett kluster manuellt med någon av de metoder som beskrivs nedan. Du kan också använda alternativ för automatisk [skalning](hdinsight-autoscale-clusters.md) för att automatiskt skala upp och ned i svar på vissa mått.

> [!NOTE]  
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av klustret du har kan du kontrol lera sidan Egenskaper.

## <a name="utilities-to-scale-clusters"></a>Verktyg för att skala kluster

Microsoft tillhandahåller följande verktyg för att skala kluster:

|Verktyg | Beskrivning|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Klassisk Azure CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure-portalen](https://portal.azure.com)|Öppna fönstret HDInsight-kluster, Välj **kluster storlek** på den vänstra menyn och skriv sedan antalet arbetsnoder i rutan kluster storlek och välj Spara.|  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Alternativet Azure Portal skalnings kluster":::

Med någon av dessa metoder kan du skala ditt HDInsight-kluster upp eller ned på några minuter.

> [!IMPORTANT]  
> * Den klassiska Azure CLI är inaktuell och bör endast användas med den klassiska distributions modellen. Använd [Azure CLI](/cli/azure/)för alla andra distributioner.
> * PowerShell-modulen AzureRM är föråldrad.  Använd AZ- [modulen](/powershell/azure/new-azureps-module-az) när det är möjligt.

## <a name="impact-of-scaling-operations"></a>Effekt av skalnings åtgärder

När du **lägger till** noder i det pågående HDInsight-klustret (skala upp) påverkas inte jobben. Nya jobb kan skickas på ett säkert sätt medan skalnings processen körs. Om skalnings åtgärden Miss lyckas lämnar klustret ett fungerande tillstånd.

Om du **tar bort** noder (skala ned) kommer väntande eller pågående jobb att Miss lyckas när skalnings åtgärden slutförs. Det här felet beror på att några av tjänsterna har startats om under skalnings processen. Klustret kan fastna i fel säkert läge under en manuell skalnings åtgärd.

Effekten av att ändra antalet datanoder varierar för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop

    Du kan sömlöst öka antalet arbetsnoder i ett Hadoop-kluster som körs utan att påverka några jobb. Nya jobb kan också skickas medan åtgärden pågår. Felen i en skalnings åtgärd hanteras på ett smidigt sätt. Klustret lämnas alltid i ett fungerande tillstånd.

    När ett Hadoop-kluster skalas ned med färre datanoder, startas vissa tjänster om. Detta innebär att alla pågående och väntande jobb inte kan köras vid slutförandet av skalnings åtgärden. Du kan dock skicka jobben igen när åtgärden har slutförts.

* Apache HBase

    Du kan enkelt lägga till eller ta bort noder i HBase-klustret medan det körs. Regionala servrar uppdelas automatiskt inom några minuter efter att du har slutfört skalnings åtgärden. Du kan dock balansera de regionala servrarna manuellt. Logga in på klustrets huvudnoden och kör följande kommandon:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Mer information om hur du använder HBase-gränssnittet finns i [Kom igång med ett Apache HBase-exempel i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Du kan enkelt lägga till eller ta bort datanoder medan Storm körs. Men när skalnings åtgärden har slutförts måste du balansera om topologin. Med ombalansering kan topologin justera [Inställningar för parallellitet](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) baserat på det nya antalet noder i klustret. Använd något av följande alternativ för att balansera om topologier som körs:

  * Webb gränssnitt för Storm

    Använd följande steg för att balansera om en topologi med storm-ANVÄNDARGRÄNSSNITTET.

    1. Öppna `https://CLUSTERNAME.azurehdinsight.net/stormui` i webbläsaren, där `CLUSTERNAME` är namnet på ditt Storm-kluster. Om du uppmanas till det anger du namnet på HDInsight-klusterresursen (admin) och lösen ordet du angav när du skapade klustret.

    1. Välj den topologi du vill balansera om och välj sedan knappen **balansera** om. Ange fördröjningen innan ombalanserings åtgärden utförs.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="Återbalansering av HDInsight Storm-skala":::

  * Kommando rads gränssnitt (CLI)

    Anslut till servern och Använd följande kommando för att balansera om en topologi:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Du kan också ange parametrar för att åsidosätta de parallella tips som ursprungligen tillhandahölls av topologin. Koden nedan konfigurerar till exempel om `mytopology` topologin till 5 arbets processer, 3 körningar för Blue-kanalen-komponenten och 10 körningar för den gula-bult-komponenten.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Du bör balansera om partition repliker efter skalnings åtgärder. Mer information finns i dokumentet [med hög tillgänglighet för data med Apache Kafka på HDInsight](./kafka/apache-kafka-high-availability.md) -dokument.

* Apache Hive LLAP

    Efter skalning till `N` arbetsnoder ställer HDInsight automatiskt in följande konfigurationer och startar om Hive.

  * Maximalt antal samtidiga frågor: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Antal noder som används av Hive-LLAP: `num_llap_nodes  = N`
  * Antal noder för att köra Hive LLAP daemon: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Så här skalar du ned ett kluster på ett säkert sätt

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skala ned ett kluster med jobb som körs

Du kan prova tre saker för att undvika att köra jobb som inte körs under en nedskalning-åtgärd:

1. Vänta tills jobben har slutförts innan du skalar ned klustret.
1. Avsluta jobben manuellt.
1. Skicka jobben igen när skalnings åtgärden har slutförts.

Om du vill se en lista över väntande och pågående jobb kan du använda garn **Resource Manager-gränssnittet** med hjälp av följande steg:

1. Välj ditt kluster från [Azure Portal](https://portal.azure.com/).  Klustret öppnas på en ny Portal sida.
2. I huvudvyn navigerar du till **kluster instrument paneler**  >  **Ambari start**. Ange dina autentiseringsuppgifter för klustret.
3. Välj **garn** i listan över tjänster på den vänstra menyn i AMBARI-användargränssnittet.  
4. På sidan garn väljer du **snabb länkar** och hovring över den aktiva Head-noden och väljer sedan **Resource Manager-användargränssnittet**.

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Apache Ambari snabb länkar Resource Manager UI":::

Du får direkt åtkomst till Resource Manager-ANVÄNDARGRÄNSSNITTET med `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

Du ser en lista över jobb, tillsammans med deras aktuella status. I skärm bilden finns ett jobb som körs för tillfället:

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Resource Manager UI-program":::

Om du vill avsluta programmet manuellt kör du följande kommando från SSH-gränssnittet:

```bash
yarn application -kill <application_id>
```

Exempel:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Fastna i felsäkert läge

När du skalar ned ett kluster använder HDInsight Apache Ambari Management Interfaces för att först inaktivera de extra arbetsnoderna. Noderna replikerar sina HDFS-block till andra online Worker-noder. Därefter skalar HDInsight säkert klustret. HDFS hamnar i fel säkert läge under skalnings åtgärden. HDFS ska komma ut när skalningen är färdig. I vissa fall fastnar HDFS i fel säkert läge under en skalnings åtgärd på grund av fil block under replikering.

Som standard konfigureras HDFS med `dfs.replication` inställningen 1, som styr hur många kopior av varje fil block som är tillgängliga. Varje kopia av ett fil block lagras på en annan nod i klustret.

När det förväntade antalet block kopior inte är tillgängligt, anger HDFS fel säkert läge och Ambari genererar aviseringar. HDFS kan ange fel säkert läge för en skalnings åtgärd. Klustret kan fastna i fel säkert läge om antalet noder som krävs inte har identifierats för replikering.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exempel fel när fel säkert läge är aktiverat

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Du kan granska namn-nodens loggar från `/var/log/hadoop/hdfs/` mappen, vid den tidpunkt då klustret skalades, för att se när det angivits i fel säkert läge. Loggfilerna heter `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

Den bakomliggande orsaken var att Hive är beroende av temporära filer i HDFS vid körning av frågor. När HDFS går in i fel säkert läge kan Hive inte köra frågor eftersom det inte går att skriva till HDFS. Temporära filer i HDFS finns på den lokala enheten som monteras på de enskilda arbetsnoderna VM. Filerna replikeras bland andra arbetsnoder vid tre repliker, minst.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Förhindra att HDInsight får fastna i fel säkert läge

Det finns flera sätt att förhindra att HDInsight lämnas i fel säkert läge:

* Stoppa alla Hive-jobb innan du skalar ned HDInsight. Du kan också schemalägga skalnings processen för att undvika konflikter med körning av Hive-jobb.
* Rensa Hives tillfälliga katalogfiler manuellt `tmp` i HDFS innan du skalar ned.
* Skala bara ned HDInsight till tre arbetsnoder, minimum. Undvik att gå så lågt som en arbetsnod.
* Kör kommandot för att lämna fel säkert läge vid behov.

I följande avsnitt beskrivs de här alternativen.

#### <a name="stop-all-hive-jobs"></a>Stoppa alla Hive-jobb

Stoppa alla Hive-jobb innan du skalar ned till en arbets nod. Om din arbets belastning har schemalagts ska du köra en nedskalning när Hive-jobbet är färdigt.

Om du stoppar Hive-jobb före skalning, bidrar till att minimera antalet virtuella installationsfiler i mappen tmp (om det finns några).

#### <a name="manually-clean-up-hives-scratch-files"></a>Rensa Hive-startfiler manuellt

Om Hive har lämnat kvar temporära filer kan du rensa filerna manuellt innan du skalar ned för att undvika fel säkert läge.

1. Kontrol lera vilken plats som används för temporära Hive-filer genom att titta på `hive.exec.scratchdir` konfigurations egenskapen. Den här parametern anges inom `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Stoppa Hive-tjänster och se till att alla frågor och jobb är slutförda.

1. Ange innehållet i den tillfälliga katalogen som finns ovan `hdfs://mycluster/tmp/hive/` för att se om den innehåller några filer:

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

1. Om du vet att Hive har gjorts med de här filerna kan du ta bort dem. Se till att Hive inte har några frågor som körs genom att titta på sidan garn Resource Manager UI.

    Exempel på kommando rad för att ta bort filer från HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skala HDInsight till tre eller flera arbetsnoder

Om klustret blir fastnat i fel säkert läge när du skalar ned till färre än tre arbetsnoder, behåller du minst tre arbetsnoder.

Att ha tre arbetsnoder är dyrare än att skala ned till endast en arbetsnod. Den här åtgärden förhindrar dock att ditt kluster blir fastnat i fel säkert läge.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Skala HDInsight ned till en arbetsnod

Även om klustret skalas ned till en nod kommer arbetsnoden 0 fortfarande att överleva. Arbetsnoden 0 kan aldrig tas ur bruk.

#### <a name="run-the-command-to-leave-safe-mode"></a>Kör kommandot för att lämna fel säkert läge

Det sista alternativet är att köra kommandot lämna fel säkert läge. Om HDFS har angivit fel säkert läge på grund av Hive-filen under replikering, kör du följande kommando för att lämna fel säkert läge:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skala ned ett Apache HBase-kluster

Region servrar bal anse ras automatiskt inom några minuter efter att en skalnings åtgärd har slutförts. Slutför följande steg för att balansera region servrar manuellt:

1. Anslut till HDInsight-klustret med SSH. Mer information finns i [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Starta HBase-gränssnittet:

    ```bash
    hbase shell
    ```

3. Använd följande kommando för att balansera region servrarna manuellt:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Nästa steg

* [Skala Azure HDInsight-kluster automatiskt](hdinsight-autoscale-clusters.md)

För detaljerad information om skalning av HDInsight-klustret, se:

* [Hantera Apache Hadoop kluster i HDInsight med hjälp av Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Hantera Apache Hadoop kluster i HDInsight med hjälp av Azure CLI](hdinsight-administer-use-command-line.md#scale-clusters)