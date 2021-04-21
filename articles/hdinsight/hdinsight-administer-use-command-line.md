---
title: Hantera Azure HDInsight med Azure CLI
description: Lär dig hur du använder Azure CLI för att Azure HDInsight kluster. Exempel på klustertyper är Apache Hadoop, Spark, HBase, Storm, Kafka, Interaktiv fråga och ML Services.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: 14b88700f3968e3bfdc788abb2fc9ce90634068e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770353"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Hantera Azure HDInsight med Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lär dig hur du [använder Azure CLI](/cli/azure/) för att Azure HDInsight kluster. Kommandoradsgränssnittet för Azure (CLI) är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Azure CLI. Om du inte har installerat Azure CLI kan du följa [anvisningarna i Installera Azure CLI.](/cli/azure/install-azure-cli)

* Ett Apache Hadoop-kluster i HDInsight. Se [Kom igång med HDInsight på Linux.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

## <a name="connect-to-azure"></a>Anslut till Azure

Logga in på din Azure-prenumeration. Om du planerar att Azure Cloud Shell väljer du **Prova i** det övre högra hörnet av kodblocket. Annars anger du kommandot nedan:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Lista kluster

Använd [az hdinsight list för](/cli/azure/hdinsight#az_hdinsight_list) att lista kluster. Redigera kommandona nedan genom att `RESOURCE_GROUP_NAME` ersätta med namnet på din resursgrupp och ange sedan kommandona:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Visa kluster

Använd [az hdinsight show för](/cli/azure/hdinsight#az_hdinsight_show) att visa information för ett angivet kluster. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME` , och med relevant information och ange sedan `CLUSTER_NAME` kommandot:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Ta bort kluster

Använd [az hdinsight delete för](/cli/azure/hdinsight#az_hdinsight_delete) att ta bort ett angivet kluster. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME` , och med relevant information och ange sedan `CLUSTER_NAME` kommandot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Du kan också ta bort ett kluster genom att ta bort resursgruppen som innehåller klustret. Observera att alla resurser i gruppen tas bort, inklusive standardlagringskontot.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skala kluster

Använd [az hdinsight resize för](/cli/azure/hdinsight#az_hdinsight_resize) att ändra storlek på det angivna HDInsight-klustret till den angivna storleken. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME` och `CLUSTER_NAME` med relevant information. Ersätt `WORKERNODE_COUNT` med önskat antal arbetsnoder för klustret. Mer information om skalning av kluster finns i [Skala HDInsight-kluster.](./hdinsight-scaling-best-practices.md) Ange kommandot:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du utför olika administrativa uppgifter för HDInsight-kluster. Mer information finns i följande artiklar:

* [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli)
