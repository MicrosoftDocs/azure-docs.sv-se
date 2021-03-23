---
title: Övervaka kluster tillgänglighet med Apache Ambari i Azure HDInsight
description: Lär dig hur du använder Apache Ambari för att övervaka kluster hälsa och tillgänglighet.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: dcbe44defeb89ad2f67833b263e5f4983070a46c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863538"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Övervaka kluster tillgänglighet med Apache Ambari i Azure HDInsight

HDInsight-kluster innehåller Apache Ambari, som ger hälso information på ett överskådligt och fördefinierade aviseringar.

Den här artikeln visar hur du använder Ambari för att övervaka klustret och går igenom några exempel på hur du konfigurerar en Ambari-avisering, övervakar tillgänglighets takten för noden och skapar en Azure Monitor avisering som utlöses när ett pulsslag inte har tagits emot från en eller flera noder på fem timmar.

## <a name="dashboard"></a>Instrumentpanel

Ambari-instrumentpanelen kan nås genom att välja länken **Ambari Home** i avsnittet **kluster instrument paneler** i HDInsight-översikten i Azure Portal som visas nedan. Du kan också komma åt den genom att navigera till `https://CLUSTERNAME.azurehdinsight.net` i en webbläsare där kluster namn är namnet på klustret.

:::image type="content" source="media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png" alt-text="Vyn HDInsight-resurs Portal":::

Sedan uppmanas du att ange ett användar namn och lösen ord för kluster inloggning. Ange de autentiseringsuppgifter som du valde när du skapade klustret.

Du kommer sedan till Ambari-instrumentpanelen som innehåller widgetar som visar en fåtal av mått för att ge dig en snabb översikt över ditt HDInsight-klusters hälsa. Dessa widgetar visar mått som antalet Live-DataNodes (arbetsnoder) och Journalnodes available (Zookeeper Node), NameNodes (huvudnoder) drift tid, samt mått som är specifika för vissa kluster typer, t. ex. garn-ResourceManager drift tid för Spark-och Hadoop-kluster.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-dashboard.png" alt-text="Apache Ambari Använd instrument panels visning":::

## <a name="hosts--view-individual-node-status"></a>Värdar – Visa status för enskilda noder

Du kan också visa statusinformation för enskilda noder. Välj fliken **värdar** om du vill visa en lista över alla noder i klustret och se grundläggande information om varje nod. Den gröna kontrollen till vänster om varje nodnamn visar att alla komponenter är upp på noden. Om en komponent finns i en nod visas en röd aviserings triangel i stället för den gröna kontrollen.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts1.png" alt-text="HDInsight Apache Ambari hosts-vy":::

Du kan sedan välja **namnet** på en nod om du vill visa mer detaljerade värd mått för just den noden. I den här vyn visas status/tillgänglighet för varje enskild komponent.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts-node.png" alt-text="Apache Ambari-värdar, vy med en nod":::

## <a name="ambari-alerts"></a>Ambari aviseringar

Ambari erbjuder också flera konfigurerbara aviseringar som kan ge meddelanden om vissa händelser. När aviseringar utlöses visas de i det övre vänstra hörnet av Ambari i en röd skylt som innehåller antalet aviseringar. Om du väljer den här skylten visas en lista över aktuella aviseringar.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-alerts.png" alt-text="Antal aktuella aviseringar för Apache Ambari":::

Om du vill visa en lista över aviserings definitioner och deras status väljer du fliken **aviseringar** , som visas nedan.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alerts-definitions.png" alt-text="Vy över Ambari aviserings definitioner":::

Ambari erbjuder många fördefinierade aviseringar relaterade till tillgänglighet, inklusive:

| Aviseringsnamn                        | Beskrivning   |
|---|---|
| DataNode hälso översikt           | Den här aviseringen på tjänst nivå utlöses om det finns DataNodes som inte är felfria|
| NameNode-hälsa för hög tillgänglighet | Den här aviseringen på tjänst nivå utlöses om antingen den aktiva NameNode eller vänte läges NameNode inte körs.|
| Procent Journalnodes available tillgängliga    | Den här aviseringen utlöses om antalet Journalnodes available i klustret är större än det konfigurerade kritiska tröskelvärdet. Den sammanställer resultaten av JournalNode process-kontroller. |
| Procent DataNodes tillgängliga       | Den här aviseringen utlöses om antalet DataNodes i klustret är större än det konfigurerade kritiska tröskelvärdet. Den sammanställer resultaten av DataNode process-kontroller.|


Om du vill visa information om en avisering eller ändra kriterier väljer du **namnet** på aviseringen. Ta **DataNode Health Summary** som ett exempel. Du kan se en beskrivning av aviseringen samt de villkor som utlöser en varnings avisering eller kritisk avisering och kontroll intervallet för kriterierna. Om du vill redigera konfigurationen väljer du knappen **Redigera** i det övre högra hörnet i rutan konfiguration.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration.png" alt-text="Aviserings konfiguration för Apache Ambari":::

Här kan du redigera beskrivningen och, viktigare, kontroll intervall och tröskelvärden för varningar eller kritiska aviseringar.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png" alt-text="Ambari-aviserings konfiguration redigera vy":::

I det här exemplet kan du göra 2 DataNodes som utlöser en kritisk varning och 1 DataNode som endast utlöser en varning. Välj **Spara** när du är klar med redigeringen.

## <a name="email-notifications"></a>E-postmeddelanden

Du kan också välja att konfigurera e-postaviseringar för Ambari-aviseringar. Det gör du genom att klicka på knappen **åtgärder** längst upp till vänster i fliken **aviseringar** och sedan **Hantera meddelanden.**

:::image type="content" source="media/hdinsight-cluster-availability/ambari-manage-notifications.png" alt-text="Ambari-åtgärd för att hantera meddelanden":::

En dialog ruta för att hantera aviseringar öppnas. Välj längst **+** ned i dialog rutan och fyll i de obligatoriska fälten för att tillhandahålla Ambari med information om e-postservern som e-postmeddelanden ska skickas från.

> [!TIP]
> Att ställa in Ambari e-postaviseringar kan vara ett bra sätt att få aviseringar på en plats när du hanterar många HDInsight-kluster.

## <a name="next-steps"></a>Nästa steg

- [Tillgänglighet och tillförlitlighet för Apache Hadoop kluster i HDInsight](./hdinsight-business-continuity.md)
- [Klustertillgänglighet – Azure Monitor-loggar](./cluster-availability-monitor-logs.md)
- [Använda Azure Monitor loggar](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [E-postaviseringar med Apache Ambari](apache-ambari-email.md)
