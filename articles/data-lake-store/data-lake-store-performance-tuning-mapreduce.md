---
title: Azure Data Lake Storage Gen1 prestanda justering – MapReduce
description: Lär dig mer om prestanda justering för MapReduce i Azure Data Lake Storage Gen1, inklusive parametrar, vägledning, exempel beräkning och begränsningar.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 2549413241e422fb1e0e5e1f079c287e0b7cf005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723736"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Vägledning för prestanda justering för MapReduce på HDInsight och Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage gen1-konto. Se [skapa ett HDInsight-kluster med data Lake Storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Se till att aktivera fjärr skrivbord för klustret.
* **Använda MapReduce på HDInsight**. Mer information finns i [använda MapReduce i Hadoop på HDInsight](../hdinsight/hadoop/hdinsight-use-mapreduce.md)
* **Granska rikt linjer för prestanda justering för data Lake Storage gen1**. Allmänna prestanda koncept finns i [vägledning för data Lake Storage gen1 prestanda justering](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametrar

När du kör MapReduce-jobb är här de viktigaste parametrarna som du kan konfigurera för att öka prestandan på Data Lake Storage Gen1:

|Parameter      | Beskrivning  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Mängden minne som ska allokeras till varje mapp.  |
|`Mapreduce.job.maps`     |  Antalet kart aktiviteter per jobb.  |
|`Mapreduce.reduce.memory.mb`     |  Mängden minne som ska allokeras till varje minskning.  |
|`Mapreduce.job.reduces`    |   Antalet minska aktiviteter per jobb.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>MapReduce. map. Memory/MapReduce. reduce. Memory

Justera det här numret baserat på hur mycket minne som krävs för kartan och/eller minska uppgiften. Du kan visa standardvärdena för `mapreduce.map.memory` och `mapreduce.reduce.memory` i Ambari via garn konfigurationen. I Ambari navigerar du till garn och visar fliken **configs** . GARN minnet visas.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>MapReduce. job. Maps/MapReduce. job. minskar

Detta fastställer det maximala antalet mappningar eller reducerare som ska skapas. Antalet delningar bestämmer hur många mappningar som skapas för MapReduce-jobbet. Därför kan du få färre mappningar än du begärt om det finns färre delningar än vad som begärts av mapparna.

## <a name="guidance"></a>Vägledning

### <a name="step-1-determine-number-of-jobs-running"></a>Steg 1: Bestäm antalet jobb som körs

Som standard kommer MapReduce att använda hela klustret för jobbet. Du kan använda mindre av klustret med färre mappningar än vad som finns tillgängliga behållare. Vägledningen i det här dokumentet förutsätter att ditt program är det enda program som körs i klustret.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Steg 2: Ange MapReduce. map. Memory/MapReduce. reduce. Memory

Storleken på minnet för mappning och minskning av aktiviteter kommer att vara beroende av ditt speciella jobb. Du kan minska minnes storleken om du vill öka samtidigheten. Antalet aktiviteter som körs samtidigt beror på antalet behållare. Genom att minska mängden minne per mapp eller minskning, kan fler behållare skapas, vilket gör det möjligt att köra fler mappningar eller minimerare för körning samtidigt. Att minska mängden minne för mycket kan leda till att det inte tar slut på minne. Om du får ett heap-fel när du kör jobbet ska du öka mängden minne per mapp eller minskning. Överväg att lägga till fler behållare lägger till extra kostnader för varje ytterligare behållare, vilket kan försämra prestanda. Ett annat alternativ är att få mer minne genom att använda ett kluster som har större mängder minne eller ökar antalet noder i klustret. Mer minne gör det möjligt att använda fler behållare, vilket innebär mer samtidighet.

### <a name="step-3-determine-total-yarn-memory"></a>Steg 3: Fastställ totalt garn minne

Om du vill justera MapReduce. job. Maps/MapReduce. job. dereduceras, bör du överväga hur mycket det totala garn minne som är tillgängligt för användning. Den här informationen finns i Ambari. Navigera till garn och Visa fliken **configs** . GARN minnet visas i det här fönstret. Multiplicera garn minnet med antalet noder i klustret för att hämta det totala garn minnet.

`Total YARN memory = nodes * YARN memory per node`

Om du använder ett tomt kluster kan minnet vara det totala garn minnet för klustret. Om andra program använder minnet kan du välja att bara använda en del av klustrets minne genom att minska antalet mappningar eller minska antalet till de behållare som du vill använda.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Steg 4: beräkna antalet garn behållare

GARN behållare bestämmer mängden samtidighet som är tillgänglig för jobbet. Ta totalt garn minne och dividera det med MapReduce. map. Memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Steg 5: Ange MapReduce. job. Maps/MapReduce. job. dereducerar

Ange MapReduce. job. Maps/MapReduce. job. minskar till minst antalet tillgängliga behållare. Du kan experimentera ytterligare genom att öka antalet mappningar och reducerare för att se om du får bättre prestanda. Kom ihåg att fler mappningar kommer att ha ytterligare kostnader så att för många mappningar kan försämra prestanda.

CPU-schemaläggning och CPU-isolering är inaktiverade som standard så att antalet garn behållare begränsas av minnet.

## <a name="example-calculation"></a>Exempel beräkning

Anta att du för närvarande har ett kluster bestående av 8 D14-noder och att du vill köra ett I/O-intensivt jobb. Följande är de beräkningar som du bör utföra:

### <a name="step-1-determine-number-of-jobs-running"></a>Steg 1: Bestäm antalet jobb som körs

I vårt exempel förutsätter vi att vårt jobb är det enda som körs.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Steg 2: Ange MapReduce. map. Memory/MapReduce. reduce. Memory

I vårt exempel kör du ett I/O-intensivt jobb och bestämmer att 3 GB minne för kart aktiviteter räcker.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Steg 3: Fastställ totalt garn minne

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Steg 4: beräkna antalet garn behållare

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Steg 5: Ange MapReduce. job. Maps/MapReduce. job. dereducerar

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Begränsningar

**Data Lake Storage Gen1 begränsning**

Som en tjänst för flera innehavare Data Lake Storage Gen1 anger bandbredds gränser på konto nivå. Om du når dessa gränser börjar du se aktivitets felen. Du kan identifiera detta genom att iaktta begränsnings fel i aktivitets loggarna. Om du behöver mer bandbredd för ditt jobb kan du kontakta oss.

Om du vill kontrol lera om du får en begränsning måste du aktivera fel söknings loggning på klient sidan. Så här kan du göra:

1. Placera följande egenskap i egenskaperna log4j i Ambari > garn > config > Advanced garn-log4j: log4j. loggfil. com. Microsoft. Azure. datalake. Store = DEBUG

2. Starta om alla noder/tjänster för att konfigurationen ska börja gälla.

3. Om du får en begränsning visas HTTP 429-felkoden i garn logg filen. GARN logg filen finns i/tmp/ &lt; User &gt; /yarn.log

## <a name="examples-to-run"></a>Exempel för att köra

För att demonstrera hur MapReduce körs på Data Lake Storage Gen1, är följande exempel kod som kördes på ett kluster med följande inställningar:

* 16-nods D14v2
* Hadoop-kluster som kör HDI 3,6

Här är några exempel kommandon för att köra MapReduce Teragen, Terasort och Teravalidate för en start punkt. Du kan justera de här kommandona baserat på dina resurser.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```