---
title: Hantera Azure HDInsight-kluster med Azure CLI
description: Lär dig hur du använder Azure CLI för att hantera Azure HDInsight-kluster. Kluster typerna är Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query och ML-tjänster.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: b17c5a2abc036c16ff3ce36b81428f9149e36b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942862"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Hantera Azure HDInsight-kluster med Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lär dig hur du använder [Azure CLI](/cli/azure/) för att hantera Azure HDInsight-kluster. Kommandoradsgränssnittet för Azure (CLI) är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Azure CLI. Om du inte har installerat Azure CLI kan du läsa om hur du [installerar Azure](/cli/azure/install-azure-cli) cli.

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Anslut till Azure

Logga in på din Azure-prenumeration. Om du planerar att använda Azure Cloud Shell väljer du **testa det** i det övre högra hörnet i kod blocket. Annars anger du kommandot nedan:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Lista kluster

Använd [AZ HDInsight List](/cli/azure/hdinsight#az-hdinsight-list) för att Visa kluster. Redigera kommandona nedan genom `RESOURCE_GROUP_NAME` att ersätta med namnet på din resurs grupp och ange sedan följande kommandon:

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

Använd [AZ HDInsight show](/cli/azure/hdinsight#az-hdinsight-show) för att visa information för ett angivet kluster. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME` och `CLUSTER_NAME` med relevant information, och ange sedan kommandot:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Ta bort kluster

Använd [AZ HDInsight Delete](/cli/azure/hdinsight#az-hdinsight-delete) för att ta bort ett angivet kluster. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME` och `CLUSTER_NAME` med relevant information, och ange sedan kommandot:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Du kan också ta bort ett kluster genom att ta bort resurs gruppen som innehåller klustret. Observera att alla resurser i gruppen tas bort, inklusive standard lagrings kontot.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skala kluster

Använd [AZ HDInsight ändra storlek](/cli/azure/hdinsight#az-hdinsight-resize) för att ändra storlek på det angivna HDInsight-klustret till den angivna storleken. Redigera kommandot nedan genom att ersätta `RESOURCE_GROUP_NAME` och `CLUSTER_NAME` med relevant information. Ersätt `WORKERNODE_COUNT` med det önskade antalet arbetsnoder för klustret. Mer information om hur du skalar kluster finns i [skala HDInsight-kluster](./hdinsight-scaling-best-practices.md). Ange kommandot:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du utför olika administrativa uppgifter i HDInsight-klustret. Mer information finns i följande artiklar:

* [Hantera Apache Hadoop kluster i HDInsight med hjälp av Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrera HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli)
