---
title: Peggad processor i Apache HBase-kluster – Azure HDInsight
description: Felsöka Peggad CPU på region server i Apache HBase-kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 7d0cf139f06bb296b486d2932d95b53fc1167a5d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98937020"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: Peggad processor på region server i Apache HBase-kluster i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache HBase region Server process börjar med nära 200% processor, vilket leder till att aviseringar utlöses på HBase Master process och kluster inte fungerar med full kapacitet.

## <a name="cause"></a>Orsak

Om du kör HBase Cluster v 3.4 kan du ha träffat en möjlig bugg som orsakas av en uppgradering av JDK till version 1.7.0 _151. Det symptom som vi ser är region Server processen börjar ta upp till 200% processor (för att verifiera körningen av `top` kommandot. om det finns en process som håller nära 200% CPU får du ett PID och bekräftar att det är en region Server process genom att köra `ps -aux | grep` ).

## <a name="resolution"></a>Lösning

1. Installera JDK 1,8 på alla noder i klustret enligt nedan:

    * Kör skript åtgärden `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Se till att välja alternativet som ska köras på alla noder.

    * Alternativt kan du logga in på varje enskild nod och köra kommandot `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Gå till Ambari UI – `https://<clusterdnsname>.azurehdinsight.net` .

1. Navigera till **HBase->configs – >Advanced->Avancerat** `hbase-env configs` och ändra variabeln `JAVA_HOME` till `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Spara konfigurations ändringen.

1. [Valfritt men rekommenderas] [Rensa alla tabeller i klustret](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. Från Ambari UI igen startar du om alla HBase-tjänster som behöver startas om.

1. Beroende på data i klustret kan det ta några minuter upp till en timme innan klustret når stabilt tillstånd. Hur du bekräftar att klustret når stabilt tillstånd är genom att antingen kontrol lera HMaster-ANVÄNDARGRÄNSSNITTET (alla region servrar ska vara aktiva) från Ambari (uppdatera) eller från huvudnoden kör HBase Shell och sedan köra status kommando.

Kontrol lera att uppgraderingen lyckades genom att kontrol lera att de relevanta HBase-processerna har startats med rätt Java-version – för instans för region Server kontrol lera som:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]