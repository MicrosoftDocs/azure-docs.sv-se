---
title: Skapa Apache Hadoop-kluster med Azure CLI – Azure HDInsight
description: Lär dig hur du Azure HDInsight kluster med hjälp av plattformsoberoende Azure CLI.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 9c19eb58e32fec66e5fe698c82133c8583f67b8b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775141"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Skapa HDInsight-kluster med Hjälp av Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Stegen i det här dokumentet går igenom hur du skapar ett HDInsight 3.6-kluster med Hjälp av Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Skapa ett kluster

1. Logga in på din Azure-prenumeration. Om du planerar att Azure Cloud Shell väljer du **Prova i** det övre högra hörnet av kodblocket. Annars anger du kommandot nedan:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Ange miljövariabler. Användningen av variabler i den här artikeln baseras på Bash. Små variationer krävs för andra miljöer. Se [az-hdinsight-create för](/cli/azure/hdinsight#az_hdinsight_create) en fullständig lista över möjliga parametrar för att skapa kluster.

    |Parameter | Beskrivning |
    |---|---|
    |`--workernode-count`| Antalet arbetarnoder i klustret. I den här artikeln används `clusterSizeInNodes` variabeln som det värde som skickas till `--workernode-count` . |
    |`--version`| HDInsight-klusterversionen. I den här artikeln används `clusterVersion` variabeln som det värde som skickas till `--version` . Se även: [HDInsight-versioner som stöds.](./hdinsight-component-versioning.md#supported-hdinsight-versions)|
    |`--type`| Typ av HDInsight-kluster, till exempel: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  I den här artikeln används `clusterType` variabeln som det värde som skickas till `--type` . Se även: [Klustertyper och konfiguration.](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type)|
    |`--component-version`|Versioner av olika Hadoop-komponenter i utrymmesavgränsade versioner i formatet "component=version". I den här artikeln används `componentVersion` variabeln som det värde som skickas till `--component-version` . Se även: [Hadoop-komponenter](./hdinsight-component-versioning.md).|

    Ersätt `RESOURCEGROUPNAME` , , , och med önskade `LOCATION` `CLUSTERNAME` `STORAGEACCOUNTNAME` `PASSWORD` värden. Ändra värdena för de andra variablerna efter behov. Ange sedan CLI-kommandona.

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

3. [Skapa resursgruppen genom](/cli/azure/group#az_group_create) att ange kommandot nedan:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Om du vill ha en lista över giltiga platser använder du kommandot `az account list-locations` och sedan någon av platserna från `name` värdet.

4. [Skapa ett Azure Storage-konto](/cli/azure/storage/account#az_storage_account_create) genom att ange kommandot nedan:

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

5. [Extrahera primärnyckeln från Azure Storage och lagra](/cli/azure/storage/account/keys#az_storage_account_keys_list) den i en variabel genom att ange kommandot nedan:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Skapa en Azure Storage container](/cli/azure/storage/container#az_storage_container_create) genom att ange kommandot nedan:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Skapa HDInsight-klustret](/cli/azure/hdinsight#az_hdinsight_create) genom att ange följande kommando:

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
    > HDInsight-kluster finns i olika typer, som motsvarar den arbetsbelastning eller teknik som klustret är justerat för. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase i ett kluster.

    Det kan ta flera minuter att skapa klustret. Vanligtvis runt 15.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även om det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

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

Nu när du har skapat ett HDInsight-kluster med hjälp av Azure CLI kan du använda följande för att lära dig hur du arbetar med klustret:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kluster

* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda Python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
