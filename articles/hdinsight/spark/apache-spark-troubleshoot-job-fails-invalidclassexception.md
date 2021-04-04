---
title: InvalidClassException-fel från Apache Spark – Azure HDInsight
description: Apache Spark jobbet Miss lyckas med InvalidClassException, klassen stämmer inte överens i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 0b0889ac1e71ce33406e89ead62370a0c0168763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929267"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark jobbet Miss lyckas med InvalidClassException, klassen stämmer inte överens i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du försöker skapa ett Apache Spark jobb i ett Spark 2. x-kluster. Det Miss lyckas med ett fel som liknar följande:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Orsak

Det här felet kan orsakas av att lägga till en ytterligare jar i `spark.yarn.jars` konfigurationen, särskilt en skuggad jar-version som innehåller en annan version av `commons-lang3` paketet och introducerar en klass matchning. Som standard använder Spark 2.1/2/3 version 3,5 av `commons-lang3` .

> [!TIP]
> För att skugga ett bibliotek är att flytta innehållet till en egen jar-fil och ändra dess paket. Det skiljer sig från paketeringen av biblioteket, vilket innebär att biblioteket placeras i din egen jar-post utan ompackning.

## <a name="resolution"></a>Lösning

Ta antingen bort burken eller kompilera om den anpassade jar-filen (AzureLogAppender) och Använd [maven-Shader-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) för att flytta om klasser.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]