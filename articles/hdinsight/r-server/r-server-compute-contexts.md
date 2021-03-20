---
title: Alternativ för beräknings kontext för ML-tjänster i HDInsight – Azure
description: Lär dig mer om de olika beräknings kontext alternativen som är tillgängliga för användare med ML-tjänster i HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 71ce0d87faa33bd7d533242edfcf3b131c8f7e47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943963"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Alternativ för beräknings kontext för ML-tjänster i HDInsight

ML-tjänster i Azure HDInsight styr hur anrop utförs genom att ange beräknings kontexten. Den här artikeln beskriver de alternativ som är tillgängliga för att ange om och hur körningen ska vara parallell över kärnor i Edge-noden eller HDInsight-klustret.

Edge-noden i ett kluster är en praktisk plats för att ansluta till klustret och köra R-skript. Med en Edge-nod kan du välja att köra de parallella distribuerade funktionerna i RevoScaleR över kärnorna i Edge-nodens Server. Du kan också köra dem på noderna i klustret genom att använda RevoScaleR för att minska eller Apache Spark Compute-kontexter.

## <a name="ml-services-on-azure-hdinsight"></a>ML-tjänster på Azure HDInsight

[Ml-tjänster i Azure HDInsight](r-server-overview.md) tillhandahåller de senaste funktionerna för R-baserade analyser. Den kan använda data som lagras i en Apache Hadoop HDFS-behållare i ditt [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage") Storage-konto, en data Lake Store eller det lokala Linux-filsystemet. Eftersom ML-tjänster bygger på öppen källkod R kan de R-baserade program som du skapar använda alla R-paket med öppen källkod. De kan också använda rutinerna i [RevoScaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), Microsofts Big data Analytics-paket som ingår i ml-tjänster.  

## <a name="compute-contexts-for-an-edge-node"></a>Compute-kontexter för en Edge-nod

I allmänhet körs ett R-skript som körs i ML Services-kluster på Edge-noden i R-tolken på den noden. Undantagen är de steg som anropar en RevoScaleR-funktion. RevoScaleR-anropen körs i en beräknings miljö som bestäms av hur du ställer in beräknings kontexten för RevoScaleR.  När du kör R-skriptet från en Edge-nod, är möjliga värden för beräknings kontexten följande:

- lokal ordning (*lokal*)
- lokal parallell (*localpar*)
- Reducera karta
- Spark

De *lokala* alternativen och *localpar* -alternativen skiljer sig bara i hur **rxExec** -anrop utförs. Båda kör andra RX-funktions anrop på ett parallellt sätt i alla tillgängliga kärnor, om inget annat anges genom användning av alternativet RevoScaleR **numCoresToUse** , till exempel `rxOptions(numCoresToUse=6)` . Alternativ för parallell körning ger optimala prestanda.

I följande tabell sammanfattas olika beräknings kontext alternativ för att ange hur samtal ska utföras:

| Beräkningskontext  | Så här ställer du in                      | Körningskontext                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokal sekventiell | rxSetComputeContext (' Local ')    | Parallell körning över kärnorna i Edge Node Server, förutom för rxExec-anrop, som körs seriellt |
| Lokal parallell   | rxSetComputeContext('localpar') | Parallell körning över kärnorna i Edge Node Server |
| Spark            | Rxspark beräkningskontexten ()                       | Parallellt distribuerad körning via spark över noderna i HDI-klustret |
| Reducera karta       | RxHadoopMR()                    | Parallellt distribuerad körning via kart minskning över noderna i HDI-klustret |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Rikt linjer för att bestämma en beräknings kontext

Vilken av de tre alternativen du väljer som ger parallella körningar beror på analys arbetets typ, storleken och platsen för dina data. Det finns ingen enkel formel som visar vilken beräknings kontext som ska användas. Det finns dock vissa GUID-principer som kan hjälpa dig att välja rätt eller, minst, hjälpa dig att begränsa dina val innan du kör ett riktmärke. Dessa GUID-principer omfattar:

- Det lokala Linux-filsystemet är snabbare än HDFS.
- Upprepade analyser är snabbare om data är lokala och om de är i XDF.
- Det är bättre att strömma små mängder data från en text data källa. Om mängden data är större kan du konvertera den till XDF innan du analyserar.
- Omkostnaderna för att kopiera eller strömma data till Edge-noden för analys blir ohanterade för mycket stora data mängder.
- ApacheSpark är snabbare än kart minskning för analys i Hadoop.

Med hänsyn till dessa principer, erbjuder följande avsnitt några allmänna regler för att välja en beräknings kontext.

### <a name="local"></a>Lokal

- Om mängden data som ska analyseras är liten och inte kräver upprepad analys kan du strömma den direkt till analys rutinen med hjälp av *lokala* eller *localpar*.
- Om mängden data som ska analyseras är liten eller medels Tor och kräver upprepad analys, kopierar du den sedan till det lokala fil systemet, importerar den till XDF och analyserar den via *lokala* eller *localpar*.

### <a name="apache-spark"></a>Apache Spark

- Om mängden data som ska analyseras är stor importerar du den till en spark-DataFrame med hjälp av **RxHiveData** eller **RxParquetData**, eller till XDF i HDFS (om det inte finns något problem med lagringen) och analyserar den med hjälp av Spark Compute-kontexten.

### <a name="apache-hadoop-map-reduce"></a>Minska Apache Hadoop karta

- Använd kartan minska beräknings kontexten endast om du stöter på ett insurmountable-problem med Spark Compute context eftersom det är i allmänhet långsammare.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Infogad hjälp på rxSetComputeContext
Mer information och exempel på RevoScaleR Compute-kontexter finns i den infogade hjälpen i R på rxSetComputeContext-metoden, till exempel:

```console
> ?rxSetComputeContext
```

Du kan också läsa [översikten över distribuerade data behandling](/machine-learning-server/r/how-to-revoscaler-distributed-computing) i [Machine Learning Server-dokumentationen](/machine-learning-server/).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om vilka alternativ som är tillgängliga för att ange om och hur körningen ska vara parallell över kärnor i Edge-noden eller HDInsight-klustret. Mer information om hur du använder ML-tjänster med HDInsight-kluster finns i följande avsnitt:

- [Översikt över ML-tjänster för Apache Hadoop](r-server-overview.md)
- [Azure Storage alternativ för ML-tjänster i HDInsight](r-server-storage.md)