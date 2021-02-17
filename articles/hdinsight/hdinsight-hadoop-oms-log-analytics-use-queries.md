---
title: Fråga Azure Monitor loggar för att övervaka Azure HDInsight-kluster
description: Lär dig hur du kör frågor på Azure Monitor loggar för att övervaka jobb som körs i ett HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 360a9730025dc24eda93868903fcd356c37d06ef
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576334"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Fråga Azure Monitor loggar för att övervaka HDInsight-kluster

Lär dig några grundläggande scenarier för hur du använder Azure Monitor loggar för att övervaka Azure HDInsight-kluster:

* [Analysera HDInsight-kluster mått](#analyze-hdinsight-cluster-metrics)
* [Skapa händelse aviseringar](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

Du måste ha konfigurerat ett HDInsight-kluster för att använda Azure Monitor loggar och lagt till de HDInsight-företagsspecifika Azure Monitor loggar övervaknings lösningar på arbets ytan. Instruktioner finns i [använda Azure Monitor loggar med HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analysera HDInsight-kluster mått

Lär dig hur du söker efter vissa mått för ditt HDInsight-kluster.

1. Öppna arbets ytan Log Analytics som är kopplad till ditt HDInsight-kluster från Azure Portal.
1. Under **Allmänt** väljer du **Loggar**.
1. Skriv följande fråga i sökrutan om du vill söka efter alla mått för alla tillgängliga mått för alla HDInsight-kluster som kon figurer ATS att använda Azure Monitor loggar och välj sedan **Kör**. Granska resultaten.

    ```kusto
    search *
    ```

    ![Apache Ambari Analytics Sök i alla mått](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Sök alla mått")

1. På den vänstra menyn väljer du fliken **filter** .

1. Under **typ** väljer du **pulsslag**. Välj **använd & kör**.

    ![Sök efter vissa mått i Log Analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Sök efter vissa mått")

1. Observera att frågan i text rutan ändras till:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Du kan gå djupare genom att använda alternativen som finns i den vänstra menyn. Exempel:

    - Så här visar du loggar från en speciell nod:

        ![Sök efter vissa fel output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Sök efter vissa fel output1")

    - Så här visar du loggar vid vissa tidpunkter:

        ![Sök efter vissa fel output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Sök efter vissa fel output2")

1. Välj **tillämpa & kör** och granska resultaten. Observera också att frågan har uppdaterats till:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Ytterligare exempel frågor

En exempel fråga baserat på genomsnitt av resurser som används i 10-minuters intervall, kategoriserade efter kluster namn:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

I stället för att förfina baserat på genomsnittet av de resurser som används kan du använda följande fråga för att förfina resultaten baserat på när de maximala resurserna användes (samt nittionde och 95 percentil) i ett 10-minuters fönster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Skapa aviseringar för spårning av händelser

Det första steget för att skapa en avisering är att komma till en fråga baserat på vilken aviseringen utlöses. Du kan använda alla frågor som du vill skapa en avisering för.

1. Öppna arbets ytan Log Analytics som är kopplad till ditt HDInsight-kluster från Azure Portal.
1. Under **Allmänt** väljer du **Loggar**.
1. Kör följande fråga som du vill skapa en avisering på och välj sedan **Kör**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    Frågan innehåller en lista över misslyckade program som körs på HDInsight-kluster.

1. Välj **ny varnings regel** överst på sidan.

    ![Ange fråga för att skapa en alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Ange fråga för att skapa en alert1")

1. I fönstret **Skapa regel** anger du frågan och annan information för att skapa en avisering och väljer sedan **skapa aviserings regel**.

    ![Ange fråga för att skapa en alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Ange fråga för att skapa en alert2")

### <a name="edit-or-delete-an-existing-alert"></a>Redigera eller ta bort en befintlig avisering

1. Öppna arbets ytan Log Analytics från Azure Portal.

1. I den vänstra menyn, under **övervakning**, väljer du **aviseringar**.

1. I överkant väljer du **Hantera aviserings regler**.

1. Välj den avisering som du vill redigera eller ta bort.

1. Du har följande alternativ: **Spara**, **ta** bort, **inaktivera** och **ta bort**.

    ![HDInsight Azure Monitor loggar varning ta bort redigera](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Mer information finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

## <a name="see-also"></a>Se även

* [Kom igång med loggfrågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)
* [Skapa anpassade vyer med hjälp av View Designer i Azure Monitor](../azure-monitor/visualize/view-designer.md)
