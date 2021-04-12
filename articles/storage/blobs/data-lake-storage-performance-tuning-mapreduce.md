---
title: 'Justera prestanda: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Lär dig mer om rikt linjer för att justera prestandan för kart minskning av jobb på Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b95d37e1725940799750dbd3c29174d9855390d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912934"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Justera prestanda: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Förstå de faktorer som du bör tänka på när du finjusterar prestandan för att mappa färre jobb. Den här artikeln beskriver ett antal rikt linjer för prestanda justering.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen2 konto**. Anvisningar om hur du skapar ett finns i [snabb start: skapa ett Azure Data Lake Storage Gen2 lagrings konto](../common/storage-account-create.md).
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage Gen2-konto. Se [använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **Använda MapReduce på HDInsight**.  Mer information finns i [använda MapReduce i Hadoop på HDInsight](../../hdinsight/hadoop/hdinsight-use-mapreduce.md)
* **Rikt linjer för prestanda justering på data Lake Storage Gen2**.  Allmänna prestanda koncept finns i [vägledning för data Lake Storage Gen2 prestanda justering](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametrar

När du kör MapReduce-jobb visas följande parametrar som du kan konfigurera för att öka prestandan för Data Lake Storage Gen2:

* **MapReduce. map. Memory. MB** – mängden minne som ska allokeras till varje mapp
* **MapReduce. job. Maps** – antalet kart aktiviteter per jobb
* **MapReduce. reduce. Memory. MB** – mängden minne som ska allokeras till varje minskning
* **MapReduce. job. minskar** – antalet minska uppgifter per jobb

**MapReduce. map. Memory/MapReduce. reduce. Memory** Antalet ska justeras baserat på hur mycket minne som krävs för kartan och/eller minska uppgiften.  Standardvärdena för MapReduce. map. Memory och MapReduce. reduce. Memory kan visas i Ambari via garn konfigurationen.  I Ambari navigerar du till garn och visar fliken configs.  GARN minnet visas.  

**MapReduce. job. Maps/MapReduce. job. minskar** Detta fastställer det maximala antalet mappade mappningar eller reducerare som ska skapas.  Antalet delningar avgör hur många mappningar som ska skapas för MapReduce-jobbet.  Därför kan du få färre mappningar än du begärt om det finns mindre delningar än antalet efterfrågade mappningar.       

## <a name="guidance"></a>Vägledning

> [!NOTE]
> Vägledningen i det här dokumentet förutsätter att ditt program är det enda program som körs i klustret.

**Steg 1: Bestäm antalet jobb som körs**

Som standard kommer MapReduce att använda hela klustret för jobbet.  Du kan använda mindre av klustret med hjälp av färre mappningar än det finns tillgängliga behållare.        

**Steg 2: Ange MapReduce. map. Memory/MapReduce. reduce. Memory**

Storleken på minnet för mappning och minskning av aktiviteter kommer att vara beroende av ditt speciella jobb.  Du kan minska minnes storleken om du vill öka samtidigheten.  Antalet aktiviteter som körs samtidigt beror på antalet behållare.  Genom att minska mängden minne per mapp eller minskning, kan fler behållare skapas, vilket gör det möjligt att köra fler mappningar eller minimerare för körning samtidigt.  Att minska mängden minne för mycket kan leda till att det inte tar slut på minne.  Om du får ett heap-fel när du kör jobbet bör du öka mängden minne per Mapper eller minskning.  Du bör överväga att lägga till fler behållare för att lägga till extra kostnader för varje ytterligare behållare, vilket kan försämra prestanda.  Ett annat alternativ är att få mer minne genom att använda ett kluster som har större mängder minne eller ökar antalet noder i klustret.  Mer minne gör det möjligt att använda fler behållare, vilket innebär mer samtidighet.  

**Steg 3: Fastställ totalt garn minne**

Om du vill finjustera MapReduce. job. Maps/MapReduce. job. dereducerar bör du ta hänsyn till den totala mängden garn minne som är tillgängligt för användning.  Den här informationen finns i Ambari.  Navigera till garn och Visa fliken configs.  GARN minnet visas i det här fönstret.  Du bör multiplicera garn minnet med antalet noder i klustret för att hämta det totala garn minnet.

Totalt garn minne = noder * garn minne per nod

Om du använder ett tomt kluster kan minnet vara det totala garn minnet för klustret.  Om andra program använder minnet kan du välja att bara använda en del av klustrets minne genom att minska antalet mappningar eller minska antalet till de behållare som du vill använda.  

**Steg 4: beräkna antalet garn behållare**

GARN behållare bestämmer mängden samtidighet som är tillgänglig för jobbet.  Ta totalt garn minne och dividera det med MapReduce. map. Memory.  

\# av garn behållare = totalt garn minne/MapReduce. map. Memory

**Steg 5: Ange MapReduce. job. Maps/MapReduce. job. dereducerar**

Ange MapReduce. job. Maps/MapReduce. job. minskar till minst antalet tillgängliga behållare.  Du kan experimentera ytterligare genom att öka antalet mappningar och reducerare för att se om du får bättre prestanda.  Kom ihåg att fler mappningar kommer att ha ytterligare kostnader så att för många mappningar kan försämra prestanda.  

CPU-schemaläggning och CPU-isolering är inaktiverade som standard så att antalet garn behållare begränsas av minnet.

## <a name="example-calculation"></a>Exempel beräkning

Vi antar att vi har ett kluster bestående av 8 D14-noder och vi vill köra ett I/O-intensivt jobb.  Följande är de beräkningar som du bör utföra:

**Steg 1: Bestäm antalet jobb som körs**

I det här exemplet antar vi att vårt jobb är det enda jobb som körs.  

**Steg 2: Ange MapReduce. map. Memory/MapReduce. reduce. Memory**

I det här exemplet kör vi ett I/O-intensivt jobb och bestämmer att GB minne för kart aktiviteter är tillräckligt.

MapReduce. map. Memory = 3 GB

**Steg 3: Fastställ totalt garn minne**

Totalt minne från klustret är 8 noder * 96GB av garn minne för en D14 = 768GB

**Steg 4: beräkna antalet garn behållare**

\# av garn behållare = 768GB tillgängligt minne/3 GB minne = 256

**Steg 5: Ange MapReduce. job. Maps/MapReduce. job. dereducerar**

mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Exempel för att köra

För att demonstrera hur MapReduce körs på Data Lake Storage Gen2 nedan finns en exempel kod som kördes på ett kluster med följande inställningar:

* 16-nods D14v2
* Hadoop-kluster som kör HDI 3,6

Här är några exempel kommandon för att köra MapReduce Teragen, Terasort och Teravalidate för en start punkt.  Du kan justera de här kommandona baserat på dina resurser.

**Teragen**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input
```

**Terasort**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output
```

**Teravalidate**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
```