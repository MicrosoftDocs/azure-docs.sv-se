---
title: Klusternoden tar slut på disk utrymme i Azure HDInsight
description: Fel sökning av problem med disk utrymmet Apache Hadoop klusternoden i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: c58ac1e65509299680f67cf17060ff3e9af4c817
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944357"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: klusternoden tar slut på disk utrymme i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett jobb kan Miss lyckas med ett fel meddelande som liknar: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Eller så kan du få Apache Ambari-aviseringar som liknar: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Orsak

Apache garn Application cache kan ha använt allt tillgängligt disk utrymme. Spark-programmet körs förmodligen ineffektivt.

## <a name="resolution"></a>Lösning

1. Använd Ambari UI för att avgöra vilken nod som håller på att ta slut på disk utrymme.

1. Bestäm vilken mapp i oroande-noden som bidrar till det mesta av disk utrymmet. Använd först SSH till noden och sedan `df` Visa disk användning för alla monteringar. Det är vanligt vis `/mnt` en temporär disk som används av oss. Du kan ange i en mapp och sedan skriva `sudo du -hs` för att Visa sammanfattade fil storlekar under en mapp. Om du ser en mapp liknande `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` innebär det att programmet fortfarande körs. Detta kan bero på RDD beständighet eller mellanliggande blandade filer.

1. Du kan åtgärda problemet genom att avsluta programmet, vilket frigör disk utrymme som används av programmet.

1. Om problemet inträffar ofta på arbetsnoderna kan du justera inställningarna för lokalt cacheminne för garn i klustret.

    Öppna Ambari-ANVÄNDARGRÄNSSNITTET navigera till garn--> configs--> Avancerat.  
    Lägg till följande två egenskaper i avsnittet anpassad yarn-site.xml och spara:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Om det här inte löser problemet permanent kan du optimera ditt program.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]