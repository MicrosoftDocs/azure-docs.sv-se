---
title: Skapa Apache Hadoop kluster med Azure CLI – Azure HDInsight
description: Lär dig hur du skapar Azure HDInsight-kluster med plattforms oberoende Azure CLI.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: a2b39f25aa2a8847697df8a5097f5ffdc9cbd212
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945888"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Skapa HDInsight-kluster med Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Stegen i det här dokumentet beskriver hur du skapar ett HDInsight 3,6-kluster med Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Skapa ett kluster

1. Logga in på din Azure-prenumeration. Om du planerar att använda Azure Cloud Shell väljer du **testa det** i det övre högra hörnet i kod blocket. Annars anger du kommandot nedan:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ange miljövariabler. Användningen av variabler i den här artikeln baseras på bash. Små variationer kommer att krävas för andra miljöer. Se [AZ-HDInsight-Create](/cli/azure/hdinsight#az-hdinsight-create) för en fullständig lista över möjliga parametrar för att skapa kluster.

    |Parameter | Beskrivning |
    |---|---|
    |`--workernode-count`| Antalet arbetsnoder i klustret. I den här artikeln används variabeln `clusterSizeInNodes` som det värde som skickades till `--workernode-count` . |
    |`--version`| HDInsight-klustrets version. I den här artikeln används variabeln `clusterVersion` som det värde som skickades till `--version` . Se även: [HDInsight-versioner som stöds](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Typ av HDInsight-kluster, t. ex. Hadoop, interactivehive, HBase, Kafka, Storm, Spark, rserver, mlservices.  I den här artikeln används variabeln `clusterType` som det värde som skickades till `--type` . Se även: [kluster typer och konfiguration](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|Versioner av olika Hadoop-komponenter, i blankstegsavgränsad versioner i formatet komponent = version. I den här artikeln används variabeln `componentVersion` som det värde som skickades till `--component-version` . Se även: [Hadoop-komponenter](./hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).|

    Ersätt `RESOURCEGROUPNAME` ,,, `LOCATION` `CLUSTERNAME` `STORAGEACCOUNTNAME` och `PASSWORD` med önskade värden. Ändra värdena för de andra variablerna efter behov. Ange sedan CLI-kommandona.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Skapa resurs gruppen](/cli/azure/group#az-group-create) genom att ange kommandot nedan:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    För en lista över giltiga platser använder du `az account list-locations` kommandot och använder sedan en av platserna från `name` värdet.

4. [Skapa ett Azure Storage konto](/cli/azure/storage/account#az-storage-account-create) genom att ange kommandot nedan:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extrahera primär nyckeln från Azure Storage-kontot](/cli/azure/storage/account/keys#az-storage-account-keys-list) och lagra den i en variabel genom att ange kommandot nedan:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Skapa en Azure Storage-behållare](/cli/azure/storage/container#az-storage-container-create) genom att ange kommandot nedan:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Skapa HDInsight-klustret](/cli/azure/hdinsight#az-hdinsight-create) genom att ange följande kommando:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight-kluster ingår i olika typer, som motsvarar den arbets belastning eller teknik som klustret är anpassat för. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel storm och HBase på ett kluster.

    Det kan ta flera minuter innan kluster skapande processen har slutförts. Vanligt vis runt 15.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används.

Ange alla eller några av följande kommandon för att ta bort resurser:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett HDInsight-kluster med Azure CLI kan du använda följande för att lära dig hur du arbetar med klustret:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kluster

* [Utveckla Java-topologier för Apache Storm i HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
