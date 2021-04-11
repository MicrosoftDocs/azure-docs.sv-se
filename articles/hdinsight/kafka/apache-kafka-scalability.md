---
title: Apache Kafka öka skala – Azure HDInsight
description: Lär dig hur du konfigurerar hanterade diskar för Apache Kafka-kluster på Azure HDInsight för att öka skalbarheten.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 9aa11be42aca59458fea0462a90b6aeb70df893d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863147"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurera lagring och skalbarhet för Apache Kafka på HDInsight

Lär dig hur du konfigurerar antalet hanterade diskar som används av [Apache Kafka](https://kafka.apache.org/) i HDInsight.

Kafka på HDInsight använder den lokala disken för virtuella datorer i HDInsight-klustret. Eftersom Kafka är mycket i/o-stor används [Azure hanterade diskar](../../virtual-machines/managed-disks-overview.md) för att tillhandahålla hög genomströmning och ger mer lagringsutrymme per nod. Om traditionella virtuella hårddiskar (VHD) användes för Kafka skulle varje nod vara begränsad till 1 TB. Du kan använda flera diskar med hanterade diskar för att uppnå 16 TB för varje nod i klustret.

Följande diagram innehåller en jämförelse mellan Kafka på HDInsight före hanterade diskar och Kafka i HDInsight med hanterade diskar:

:::image type="content" source="./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png" alt-text="Kafka med Managed disks-arkitektur" border="false":::

## <a name="configure-managed-disks-azure-portal"></a>Konfigurera hanterade diskar: Azure-portalen

1. Följ stegen i [Skapa ett HDInsight-kluster](../hdinsight-hadoop-create-linux-clusters-portal.md) för att förstå de vanliga stegen för att skapa ett kluster med hjälp av portalen. Slutför inte skapande processen för portalen.

2. I avsnittet **konfiguration & prissättning** använder du fältet __antal noder__ för att konfigurera antalet diskar.

    > [!NOTE]  
    > Typen av hanterade diskar kan vara antingen __Standard__ (HDD) eller __Premium__ (SSD). Premiumdiskar används med DS- och GS-serien virtuella datorer. Alla andra typer av virtuella dator använder standard.

    :::image type="content" source="./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png" alt-text="avsnittet kluster storlek med noden diskar per arbets grupp markerad" border="true":::

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurera hanterade diskar: Resource Manager-mall

Använd följande avsnitt i mallen för att styra antalet diskar som används av arbetarnoder i ett Kafka-kluster:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Du kan hitta en fullständig mall som visar hur du konfigurerar hanterade diskar på [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) .

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med Apache Kafka i HDInsight finns i följande dokument:

* [Använd MirrorMaker för att skapa en replik av Apache Kafka på HDInsight](apache-kafka-mirroring.md)
* [Använda Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Använda Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Ansluta till Apache Kafka via en Azure-Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [HDInsight-blogg på hanterade diskar med Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
