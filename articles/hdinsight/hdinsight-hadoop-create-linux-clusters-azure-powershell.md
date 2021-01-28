---
title: Skapa Apache Hadoop kluster med PowerShell – Azure HDInsight
description: Lär dig hur du skapar Apache Hadoop, Apache HBase, Apache Storm eller Apache Spark kluster på Linux för HDInsight med hjälp av Azure PowerShell.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 86622bf96d4b59537a2946073fdc638e51c3852d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945829"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Skapa Linux-baserade kluster i HDInsight med hjälp av Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell är en kraftfull skript miljö som du kan använda för att styra och automatisera distributionen och hanteringen av dina arbets belastningar i Microsoft Azure. Det här dokumentet innehåller information om hur du skapar ett Linux-baserat HDInsight-kluster med hjälp av Azure PowerShell. Det innehåller också ett exempel skript.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) AZ-modul.

## <a name="create-cluster"></a>Skapa kluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du vill skapa ett HDInsight-kluster med hjälp av Azure PowerShell måste du utföra följande procedurer:

* Skapa en Azure-resurs grupp
* Skapa ett Azure Storage-konto
* Skapa en Azure Blob-behållare
* Skapa ett HDInsight-kluster

> [!NOTE]
> Det finns för närvarande inte stöd för att använda PowerShell för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen2.

Följande skript visar hur du skapar ett nytt kluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Värdena som du anger för kluster inloggningen används för att skapa Hadoop-användarkontot för klustret. Använd det här kontot för att ansluta till tjänster som finns i klustret, till exempel webb-UIs eller REST-API: er.

Värdena som du anger för SSH-användaren används för att skapa SSH-användaren för klustret. Använd det här kontot för att starta en fjärr-SSH-session i klustret och köra jobb. Mer information finns i dokumentet [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Om du planerar att använda fler än 32 arbetsnoder (antingen när klustret skapas eller genom att skala klustret efter att det har skapats), måste du också ange en huvudnods storlek med minst 8 kärnor och 14 GB RAM.
>
> Mer information om nodstorlekar och relaterade kostnader finns i [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).

Det kan ta upp till 20 minuter att skapa ett kluster.

## <a name="create-cluster-configuration-object"></a>Skapa kluster: konfigurations objekt

Du kan också skapa ett HDInsight-konfigurations objekt med hjälp av [`New-AzHDInsightClusterConfig`](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) cmdlet. Du kan sedan ändra detta konfigurations objekt om du vill aktivera ytterligare konfigurations alternativ för klustret. Använd slutligen `-Config` parametern för [`New-AzHDInsightCluster`](/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdleten för att använda konfigurationen.

Följande skript skapar ett konfigurations objekt för att konfigurera en R Server på HDInsight-kluster typ. Konfigurationen aktiverar en Edge-nod, RStudio och ett ytterligare lagrings konto.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Det finns inte stöd för att använda ett lagrings konto på en annan plats än HDInsight-klustret. När du använder det här exemplet skapar du det ytterligare lagrings kontot på samma plats som servern.

## <a name="customize-clusters"></a>Anpassa kluster

* Se [Anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Se [Anpassa HDInsight-kluster med skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster kan du använda följande resurser för att lära dig hur du arbetar med klustret.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-kluster

* [Utveckla Java-topologier för storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda python-komponenter i storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark kluster

* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](spark/apache-spark-machine-learning-mllib-ipython.md)