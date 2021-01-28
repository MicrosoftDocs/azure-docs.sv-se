---
title: Skapa Azure HDInsight – Azure Data Lake Storage Gen2 – Azure CLI
description: Lär dig hur du använder Azure Data Lake Storage Gen2 med Azure HDInsight-kluster med hjälp av Azure CLI.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: bbc1cd27d5c16eddd3aaad748c34445e5017e209
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945499"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Skapa ett kluster med Data Lake Storage Gen2 med Azure CLI

Följ dessa steg om du vill skapa ett HDInsight-kluster som använder Data Lake Storage Gen2 för lagring.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till Azure Data Lake Storage Gen2 kan du läsa [avsnittet Översikt](hdinsight-hadoop-use-data-lake-storage-gen2.md). 
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Det finns tre alternativ för att köra CLI-skript exempel:
    - Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure Portal (se nästa avsnitt).
    - Använd den inbäddade Azure Cloud Shell via knappen "prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda en lokal CLI-konsol. Logga in på Azure med hjälp `az login` av ett konto som är associerat med den Azure-prenumeration under vilken du vill distribuera den tilldelade hanterade identiteten. Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Du kan [Ladda ned en mall för exempel filen](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) och [Ladda ned en exempel parameter fil](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Innan du använder mallen och kod avsnittet för Azure CLI nedan ersätter du följande plats hållare med rätt värden:

| Platshållare | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | ID för din Azure-prenumeration |
| `<RESOURCEGROUPNAME>` | Den resurs grupp där du vill att det nya klustret och lagrings kontot ska skapas. |
| `<MANAGEDIDENTITYNAME>` | Namnet på den hanterade identitet som får behörigheter på ditt lagrings konto med Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Det nya lagrings kontot med Azure Data Lake Storage Gen2 som ska skapas. |
| `<FILESYSTEMNAME>`  | Namnet på det fil system som det här klustret ska använda i lagrings kontot. |
| `<CLUSTERNAME>` | Namnet på ditt HDInsight-kluster. |
| `<PASSWORD>` | Ditt lösen ord för att logga in på klustret med SSH och Ambari-instrumentpanelen. |

Kodfragmentet nedan visar följande inledande steg:

1. Loggar in på ditt Azure-konto.
1. Anger den aktiva prenumeration där åtgärderna för att skapa ska utföras.
1. Skapar en ny resurs grupp för de nya distributions aktiviteterna.
1. Skapar en användardefinierad hanterad identitet.
1. Lägger till ett tillägg i Azure CLI för att använda funktioner för Data Lake Storage Gen2.
1. Skapar ett nytt lagrings konto med Data Lake Storage Gen2 med hjälp av `--hierarchical-namespace true` flaggan.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Logga sedan in på portalen. Lägg till den nya användarspecifika hanterade identiteten i rollen **Storage BLOB data Contributor** på lagrings kontot. Det här steget beskrivs i steg 3 under [använda Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Kontrol lera att ditt lagrings konto har den användar tilldelnings identitet med rollen **Storage BLOB data Contributor** , annars kommer klustret inte att kunna skapas.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

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

Du har skapat ett HDInsight-kluster. Lär dig nu hur du arbetar med klustret.

### <a name="apache-spark-clusters"></a>Apache Spark kluster

* [Anpassa Linux-baserade HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)
* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
