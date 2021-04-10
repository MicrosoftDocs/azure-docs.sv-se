---
title: Aktivera automatisk generering av ämnen i Apache Kafka – Azure HDInsight
description: Lär dig hur du konfigurerar Apache Kafka på HDInsight för att automatiskt skapa ämnen. Du kan konfigurera Kafka genom att ställa in `auto.create.topics.enable` på Sant via Ambari. Eller när klustret skapas via PowerShell eller Resource Manager-mallar.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 3766d41959383d802e50aafbf59b9841d1c8d74e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870695"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Så här konfigurerar du Apache Kafka på HDInsight för att automatiskt skapa ämnen

Apache Kafka i HDInsight aktiverar som standard inte automatisk generering av ämne. Du kan aktivera skapande av automatiskt ämne för befintliga kluster med Apache Ambari. Du kan också aktivera skapande av automatiskt ämne när du skapar ett nytt Kafka-kluster med hjälp av en Azure Resource Manager-mall.

## <a name="apache-ambari-web-ui"></a>Apache Ambari-webbgränssnitt

Gör så här om du vill aktivera automatisk generering av ämnen i ett befintligt kluster genom Ambari-webbgränssnittet:

1. Välj ditt Kafka-kluster från [Azure Portal](https://portal.azure.com).

1. Välj **Ambari start** från **kluster instrument paneler**.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png" alt-text="Bild av portalen med valt kluster instrument panel" border="true":::

    När du uppmanas autentiserar du med inloggnings uppgifterna (admin) för klustret. I stället kan du ansluta till Amabri direkt från `https://CLUSTERNAME.azurehdinsight.net/` där `CLUSTERNAME` är namnet på ditt Kafka-kluster.

1. Välj Kafka-tjänsten i listan till vänster på sidan.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-list.png" alt-text="Flik för Apache Ambari service list" border="true":::

1. Välj config i mitten av sidan.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-config.png" alt-text="Fliken Konfiguration av Apache Ambari-tjänst" border="true":::

1. Ange ett värde i fältet filter `auto.create` .

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png" alt-text="Sök filter fält för Apache Ambari" border="true":::

    Den här inställningen filtrerar listan med egenskaper och visar `auto.create.topics.enable` inställningen.

1. Ändra värdet för `auto.create.topics.enable` till `true` och välj sedan **Spara**. Lägg till en anteckning och välj sedan **Spara** igen.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png" alt-text="Bild av filen Auto. Create. topics. enable Entry" border="true":::

1. Välj Kafka-tjänsten, Välj __starta om__ och välj sedan __starta om alla berörda__. När du uppmanas väljer du __Bekräfta omstart av alla__.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/restart-all-affected.png" alt-text="&quot;Apache Ambari restart all berörs&quot;" border="true":::

> [!NOTE]  
> Du kan också ange Ambari-värden via Ambari-REST API. Detta är i allmänhet svårare eftersom du måste göra flera REST-anrop för att hämta den aktuella konfigurationen, ändra den osv. Mer information finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) Document.

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

När du skapar ett Kafka-kluster med hjälp av en Azure Resource Manager-mall kan du direkt ange `auto.create.topics.enable` genom att lägga till det i en `kafka-broker` . Följande JSON-kodfragment visar hur du ställer in det här värdet på `true` :

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du aktiverar automatisk generering av ämnen för Apache Kafka i HDInsight. Mer information om hur du arbetar med Kafka finns i följande länkar:

* [Analysera Apache Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Apache Kafka-kluster](apache-kafka-mirroring.md)
