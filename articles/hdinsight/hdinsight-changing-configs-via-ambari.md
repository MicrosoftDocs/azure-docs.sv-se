---
title: Optimera kluster med Apache Ambari i Azure HDInsight
description: Använd webbgränssnittet Apache Ambari för att konfigurera och optimera Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 14268e58296554cedc9488e48b41719421797a57
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933276"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Optimera kluster med Apache Ambari i Azure HDInsight

HDInsight tillhandahåller Apache Hadoop kluster för storskaliga data bearbetnings program. Det kan vara svårt att hantera, övervaka och optimera dessa komplexa kluster med flera noder. Apache Ambari är ett webb gränssnitt för att hantera och övervaka HDInsight Linux-kluster.

En introduktion till att använda Ambari-webbgränssnittet finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)

Logga in på Ambari på `https://CLUSTERNAME.azurehdidnsight.net` med dina autentiseringsuppgifter för klustret. Den första skärmen visar en översikts instrument panel.

![Användarens instrument panel för Apache Ambari visas](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Ambari-webbgränssnittet används för att hantera värdar, tjänster, aviseringar, konfigurationer och vyer. Ambari kan inte användas för att skapa ett HDInsight-kluster eller uppgradera tjänster. Det går inte heller att hantera stackar och versioner, deprovision eller reprovision Hosts eller lägga till tjänster i klustret.

## <a name="manage-your-clusters-configuration"></a>Hantera klustrets konfiguration

Konfigurations inställningar hjälper dig att finjustera en viss tjänst. Om du vill ändra konfigurations inställningarna för en tjänst väljer du tjänsten på sid panelen för **tjänster** (till vänster). Gå sedan till fliken **configs** på sidan tjänst information.

![Apache Ambari Services-sidofält](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>Ändra NameNode Java-Heap-storlek

NameNode Java-heap-storleken beror på många faktorer, till exempel belastningen på klustret. Dessutom är antalet filer och antalet block. Standard storleken på 1 GB fungerar bra med de flesta kluster, men vissa arbets belastningar kan kräva mer eller mindre minne.

Ändra NameNode Java-Heap-storlek:

1. Välj **HDFS** från panelen tjänster och navigera till fliken **konfigurationer** .

    ![Apache Ambari HDFS-konfiguration](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Hitta inställningen **NameNode Java-Heap-storlek**. Du kan också använda text rutan **filter** för att skriva och söka efter en viss inställning. Välj **Penn** ikonen bredvid inställnings namnet.

    ![Apache Ambari NameNode Java-Heap-storlek](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Skriv det nya värdet i text rutan och tryck sedan på **RETUR** för att spara ändringen.

    ![Ambari Edit NameNode Java heap size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Storleken på NameNode Java-heap ändras till 1 GB från 2 GB.

    ![Redigerad NameNode Java-heap SIZE2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Spara ändringarna genom att klicka på den gröna knappen **Spara** överst på konfigurations skärmen.

    ![' Apache Ambari Save Configurations '](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med webbgränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari-REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimera Apache HBase](./optimize-hbase-ambari.md)
* [Optimera Apache Hive](./optimize-hive-ambari.md)
* [Optimera Apache Pig](./optimize-pig-ambari.md)
