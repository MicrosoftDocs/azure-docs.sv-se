---
title: Migrera kluster till en nyare version
titleSuffix: Azure HDInsight
description: Läs rikt linjerna för att migrera ditt Azure HDInsight-kluster till en nyare version.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 04da5d668515fe96d50d4e6a7d0f5ff1c4c48c27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931379"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrera HDInsight-kluster till en nyare version

För att kunna dra nytta av de senaste HDInsight-funktionerna rekommenderar vi att HDInsight-kluster regelbundet migreras till den senaste versionen. HDInsight stöder inte uppgraderingar på plats där ett befintligt kluster uppgraderas till en nyare komponent version. Du måste skapa ett nytt kluster med önskad komponent och plattforms version och sedan migrera dina program till att använda det nya klustret. Följ rikt linjerna nedan för att migrera dina HDInsight-kluster versioner.

> [!NOTE]  
> Information om vilka versioner av HDInsight som stöds finns i [versioner av HDInsight-komponenter](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Migrera uppgifter

Arbets flödet för att uppgradera HDInsight-kluster är följande.
![Arbets flödes diagram för HDInsight-uppgradering](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Läs varje avsnitt av det här dokumentet om du vill veta vilka ändringar som kan krävas när du uppgraderar ditt HDInsight-kluster.
2. Skapa ett kluster som test/kvalitets säkrings miljö. Mer information om hur du skapar ett kluster finns i [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
3. Kopiera befintliga jobb, data källor och mottagare till den nya miljön.
4. Genomför verifierings testning för att se till att dina jobb fungerar som förväntat på det nya klustret.

När du har kontrollerat att allt fungerar som förväntat schemalägger du stillestånds tiden för migreringen. Under den här stillestånds tiden utför du följande åtgärder:

1. Säkerhetskopiera alla tillfälliga data som lagras lokalt på klusternoderna. Om du till exempel har data som lagras direkt på en head-nod.
1. [Ta bort det befintliga klustret](./hdinsight-delete-cluster.md).
1. Skapa ett kluster i samma VNET-undernät med den senaste (eller stödda) HDI-versionen med samma standard data lager som det tidigare klustret använde. Detta gör att det nya klustret kan fortsätta att arbeta med dina befintliga produktions data.
1. Importera alla övergående data som du säkerhetskopierar.
1. Starta jobb/Fortsätt bearbeta med det nya klustret.

## <a name="workload-specific-guidance"></a>Vägledning för arbets belastnings information

Följande dokument ger vägledning om hur du migrerar vissa arbets belastningar:

* [Migrera HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migrera Kafka](./kafka/migrate-versions.md)
* [Migrera Hive/interaktiv fråga](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Mer information om säkerhets kopiering och återställning av databaser finns [i återställa en databas i Azure SQL Database med hjälp av automatisk säkerhets kopiering av databasen](../azure-sql/database/recovery-using-backups.md).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du skapar Linux-baserade HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Ansluta till HDInsight med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Hantera ett Linux-baserat kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Viktig information om HDInsight](./hdinsight-version-release.md)
