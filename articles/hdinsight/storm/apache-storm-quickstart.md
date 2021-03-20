---
title: 'Snabb start: skapa/hantera Apache Storm topologi – Azure HDInsight'
description: I snabb starten lär du dig hur du skapar och övervakar en Apache Storm topologi i Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.custom: mvc
ms.openlocfilehash: fe7b1cdc96ae95ac361183342ea491432c3ab4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928852"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Snabb start: skapa och övervaka en Apache Storm topologi i Azure HDInsight

Apache Storm är ett skalbart, feltolerant och distribuerat system för beräkningar i realtid för bearbetning av dataströmmar. Du kan skapa ett molnbaserat Storm-kluster som utför analyser av stordata i realtid med Storm på Azure HDInsight.

I den här snabb starten använder du ett exempel från Apache [Storm-starter-](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projektet för att skapa och övervaka en Apache Storm topologi till ett befintligt Apache Storm-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Storm kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **kluster typ**.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Skapa topologin

1. Anslut till ditt Storm-kluster. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på ditt Storm-kluster och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. **WORDCOUNT** -exemplet ingår i ditt HDInsight-kluster på `/usr/hdp/current/storm-client/contrib/storm-starter/` . Topologin genererar slumpmässiga meningar och räknar hur många gånger ord förekommer. Använd följande kommando för att starta **WORDCOUNT** -topologin i klustret:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Övervaka topologin

Storm tillhandahåller ett webb gränssnitt för att arbeta med topologier som körs och ingår i ditt HDInsight-kluster.

Genomför följande för att övervaka topologin med hjälp av Storm-användargränssnittet:

1. Visa Storm-användargränssnittet genom att öppna en webbläsare på `https://CLUSTERNAME.azurehdinsight.net/stormui`. Ersätt `CLUSTERNAME` med namnet på klustret.

2. Under **topologins Sammanfattning** väljer du posten **WORDCOUNT** i kolumnen **namn** . Detta visar information om topologin.

    ![Storm Dashboard med storm-starter, topologisk information om WordCount.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Den nya sidan innehåller följande information:

    |Egenskap | Beskrivning |
    |---|---|
    |Topology-statistik|Grundläggande information om topologins prestanda, indelad i tid Windows. När du markerar ett specifikt tidsfönster ändras tidsfönstret för information som visas i andra avsnitt på sidan.|
    |Kanaler|Grundläggande information om kanaler, inklusive det senaste felet som returneras av varje kanalen.|
    |Bultar|Grundläggande information om bultar.|
    |Konfiguration av topologi|Detaljerad information om Topology-konfigurationen.|
    |Aktivera|Återupptar bearbetning av en inaktive rad topologi.|
    |Inaktivera|Pausar en topologi som körs.|
    |Balansera om|Justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Ombalansering justerar parallelliteten och kompenserar för det ökade/minskade antalet noder i klustret. Mer information finns i [förstå parallellitet för en Apache Storm topologi](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Döda|Avslutar en Storm-topologi efter den angivna tids gränsen.|

3. På den här sidan väljer du en post från avsnittet **Spouts** (Kanaler) eller **Bolts** (Bultar). Detta visar information om den valda komponenten.

    ![Storm-instrumentpanelen med information om valda komponenter.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Den nya sidan visar följande information:

    |Egenskap | Beskrivning |
    |---|---|
    |Kanalen/bult-statistik|Grundläggande information om komponent prestanda, indelad i tid Windows. När du markerar ett specifikt tidsfönster ändras tidsfönstret för information som visas i andra avsnitt på sidan.|
    |Ingångs statistik (endast bult)|Information om komponenter som producerar data som används av bulten.|
    |Utmatnings statistik|Information om data som genereras av den här bulten.|
    |Körare|Information om instanser av den här komponenten.|
    |Fel|Fel som skapats av den här komponenten.|

4. När du visar information om en kanal eller en bult väljer du en post i kolumnen **Port** i avsnittet **Executors** (Utförare) för att visa information för en viss komponentinstans.

```output
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
```

I det här exemplet förekommer ordet **seven** (sju) 1 493 957 gånger. Det är antalet gånger ordet har påträffats sedan topologin startades.

## <a name="stop-the-topology"></a>Stoppa topologin

Gå tillbaka till sidan **Topology summary** (Topologiöversikt) för ordräkningstopologin och välj knappen **Kill** (Avsluta) i avsnittet **Topology actions** (Topologiåtgärder). När du uppmanas ange antal sekunder innan topologin stoppas, anger du 10. När tidsgränsen uppnåtts visas topologin inte längre när du går in på avsnittet **Storm UI** (Storm-användargränssnitt) på instrumentpanelen.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster läser du [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du ett exempel från Apache [Storm-starter-](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projektet för att skapa och övervaka en Apache Storm topologi till ett befintligt Apache Storm-kluster. Gå vidare till nästa artikel och lär dig grunderna för att hantera och övervaka Apache Storm topologier.

> [!div class="nextstepaction"]
>[Distribuera och hantera Apache Storm topologier på Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)