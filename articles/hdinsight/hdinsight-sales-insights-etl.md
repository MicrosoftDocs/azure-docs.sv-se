---
title: 'Självstudie: skapa en ETL-pipeline från slut punkt till slut punkt för att härleda Sälj insikter i Azure HDInsight'
description: Lär dig hur du använder skapa ETL-pipelines med Azure HDInsight för att härleda insikter från försäljnings data med Spark-kluster på begäran och Power BI.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 32c331b10fcc73b5449588595370f8f69ef9893c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872055"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Självstudie: skapa en pipeline för data från slut punkt till slut punkt för att härleda Sälj insikter i Azure HDInsight

I den här självstudien skapar du en pipeline från slut punkt till slut punkt som utför åtgärder för att extrahera, transformera och läsa in (ETL). Pipelinen använder [Apache Spark](./spark/apache-spark-overview.md) -och Apache Hive-kluster som körs på Azure HDInsight för att skicka frågor till och ändra data. Du använder också tekniker som Azure Data Lake Storage Gen2 för data lagring och Power BI för visualisering.

Den här datapipelinen kombinerar data från olika butiker, tar bort alla oönskade data, lägger till nya data och läser in allt detta på lagrings platsen för att visualisera affärs insikter. Läs mer om ETL-pipelines i [Extract, Transform och load (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)i stor skala.

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="ETL-arkitektur" border="false":::

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Azure CLI – minst version 2.2.0. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

* JQ, en JSON-processor med kommando rad.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* En medlem i den [inbyggda roll ägaren i Azure](../role-based-access-control/built-in-roles.md).

* Om du använder PowerShell för att utlösa Data Factory pipeline behöver du [AZ-modulen](/powershell/azure/).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) för att visualisera affärs insikter i slutet av den här självstudien.

## <a name="create-resources"></a>Skapa resurser

### <a name="clone-the-repository-with-scripts-and-data"></a>Klona lagrings platsen med skript och data

1. Logga in på din Azure-prenumeration. Om du planerar att använda Azure Cloud Shell väljer du **testa det** i det övre högra hörnet i kod blocket. Annars anger du kommandot nedan:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Se till att du är medlem i Azure Role- [ägaren](../role-based-access-control/built-in-roles.md). Ersätt `user@contoso.com` med ditt konto och ange sedan kommandot:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Om ingen post returneras är du inte medlem och kan inte slutföra den här självstudien.

1. Hämta data och skript för den här självstudien från [ETL-databasen för HDInsight Sales Insights](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Ange följande kommando:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Se till att de `salesdata scripts templates` har skapats. Verifiera med följande kommando:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Distribuera Azure-resurser som krävs för pipelinen

1. Lägg till körnings behörigheter för alla skript genom att ange:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Ange variabel för resurs grupp. Ersätt `RESOURCE_GROUP_NAME` med namnet på en befintlig eller ny resurs grupp och ange sedan kommandot:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Kör skriptet. Ersätt `LOCATION` med ett önskat värde och ange sedan kommandot:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Om du inte är säker på vilken region du ska ange kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [AZ Account List-locations](/cli/azure/account#az-account-list-locations) .

    Kommandot distribuerar följande resurser:

    * Ett Azure Blob Storage-konto. Det här kontot kommer att innehålla företagets försäljnings data.
    * Ett Azure Data Lake Storage Gen2 konto. Det här kontot kommer att fungera som lagrings konto för båda HDInsight-klustren. Läs mer om HDInsight och Data Lake Storage Gen2 i [Azure HDInsight-integrering med data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * En användare som tilldelats en hanterad identitet. Det här kontot ger HDInsight-kluster åtkomst till Data Lake Storage Gen2 kontot.
    * Ett Apache Spark kluster. Klustret kommer att användas för att rensa och transformera rå data.
    * Ett Apache Hive [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) -kluster. Det här klustret kommer att tillåta att fråga försäljnings data och visualisera dem med Power BI.
    * Ett virtuellt Azure-nätverk som stöds av regler för nätverks säkerhets grupper (NSG). Det här virtuella nätverket gör att klustren kan kommunicera och säkra sin kommunikation.

Det kan ta ungefär 20 minuter att skapa kluster.

Standard lösen ordet för SSH-åtkomst till klustren är `Thisisapassword1` . Om du vill ändra lösen ordet går du till `./templates/resourcesparameters_remainder.json` filen och ändrar lösen ordet för `sparksshPassword` `sparkClusterLoginPassword` parametrarna,, `llapClusterLoginPassword` och `llapsshPassword` .

### <a name="verify-deployment-and-collect-resource-information"></a>Verifiera distribution och samla in resursinformation

1. Om du vill kontrol lera statusen för din distribution går du till resurs gruppen på Azure Portal. Under **Inställningar** väljer du **distributioner**, sedan din distribution. Här kan du se vilka resurser som har distribuerats och vilka resurser som fortfarande pågår.

1. Ange följande kommando för att visa namnen på klustren:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Om du vill visa Azure Storage-kontot och åtkomst nyckeln anger du följande kommando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Om du vill visa Data Lake Storage Gen2 konto och åtkomst nyckel anger du följande kommando:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Azure Data Factory är ett verktyg som hjälper dig att automatisera Azure-pipeliner. Det är inte det enda sättet att utföra dessa uppgifter, men det är ett bra sätt att automatisera processerna. Mer information om Azure Data Factory finns i Azure Data Factory- [dokumentationen](https://azure.microsoft.com/services/data-factory/).

Den här data fabriken kommer att ha en pipeline med två aktiviteter:

* Den första aktiviteten kopierar data från Azure Blob Storage till Data Lake Storage gen 2-lagrings konto för att efterlikna data inmatningen.
* Den andra aktiviteten kommer att transformera data i Spark-klustret. Skriptet transformerar data genom att ta bort oönskade kolumner. Den lägger också till en ny kolumn som beräknar intäkterna som en enskild transaktion genererar.

Kör kommandot nedan för att konfigurera Azure Data Factory pipelinen.  Du bör fortfarande vara på `hdinsight-sales-insights-etl` katalogen.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Det här skriptet gör följande:

1. Skapar ett huvud namn för tjänsten med `Storage Blob Data Contributor` behörigheter på data Lake Storage Gen2 lagrings konto.
1. Hämtar en autentiseringstoken för att auktorisera POST begär anden till [data Lake Storage Gen2 fil system REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Fyller i det faktiska namnet på ditt Data Lake Storage Gen2 lagrings konto i `sparktransform.py` `query.hql` filerna och.
1. Hämtar lagrings nycklar för Data Lake Storage Gen2-och Blob Storage-konton.
1. Skapar en annan resurs distribution för att skapa en Azure Data Factory pipeline med tillhör ande länkade tjänster och aktiviteter. Den överför lagrings nycklarna som parametrar till mallfilen så att de länkade tjänsterna kan komma åt lagrings kontona på rätt sätt.

## <a name="run-the-data-pipeline"></a>Kör data pipelinen

### <a name="trigger-the-data-factory-activities"></a>Utlös Data Factory aktiviteter

Den första aktiviteten i Data Factory pipelinen som du har skapat flyttar data från Blob Storage till Data Lake Storage Gen2. Den andra aktiviteten använder Spark-omvandlingarna för data och sparar transformerade CSV-filer på en ny plats. Det kan ta några minuter att slutföra hela pipelinen.

Ange följande kommando för att hämta Data Factory namn:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Om du vill utlösa pipelinen kan du antingen:

* Utlös Data Factory pipeline i PowerShell. Ersätt `RESOURCEGROUP` och `DataFactoryName` med lämpliga värden och kör sedan följande kommandon:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Kör på nytt `Get-AzDataFactoryV2PipelineRun` vid behov för att övervaka förloppet.

    Eller

* Öppna data fabriken och välj **redigera & övervakare**. Utlös `IngestAndTransform` pipelinen från portalen. Information om hur du aktiverar pipelines via portalen finns i [skapa Apache Hadoop kluster på begäran i HDInsight med Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

För att verifiera att pipelinen har körts kan du göra något av följande:

* Gå till **Monitor** -avsnittet i din data fabrik via portalen.
* I Azure Storage Explorer går du till ditt Data Lake Storage gen 2-lagrings konto. Gå till `files` fil systemet och gå sedan till `transformed` mappen och kontrol lera dess innehåll för att se om pipelinen lyckades.

Andra sätt att transformera data med hjälp av HDInsight finns i [den här artikeln om hur du använder Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Skapa en tabell i det interaktiva fråga klustret om du vill visa data på Power BI

1. Kopiera `query.hql` filen till LLAP-klustret med hjälp av SCP. Ange kommandot:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Påminnelse: standard lösen ordet är `Thisisapassword1` .

1. Använd SSH för att få åtkomst till LLAP-klustret. Ange kommandot:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Använd följande kommando för att köra skriptet:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Det här skriptet skapar en hanterad tabell på det interaktiva Query-kluster som du kan komma åt från Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Skapa en Power BI instrument panel från försäljnings data

1. Öppna Power BI Desktop.

1. Från menyn navigerar du till **Hämta data**  >  **mer...**  >  **Azure**  >  **Interaktiv HDInsight-fråga**.

1. Välj **Anslut**.

1. Från dialog rutan **HDInsight-interaktiv fråga** :
    1. I text rutan **Server** anger du namnet på ditt LLAP-kluster i formatet `https://LLAPCLUSTERNAME.azurehdinsight.net` .
    1. I text rutan **databas** anger du `default` .
    1. Välj **OK**.

1. I dialog rutan **AzureHive** :
    1. I text rutan **användar namn** anger du `admin` .
    1. I text rutan **lösen ord** anger du `Thisisapassword1` .
    1. Välj **Anslut**.

1. Från **Navigator** väljer du `sales` och/eller `sales_raw` för att förhandsgranska data. När data har lästs in kan du experimentera med den instrument panel som du vill skapa. Se följande länkar för att komma igång med Power BI-instrumentpaneler:

* [Introduktion till instrumentpaneler för Power BI-designers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Självstudie: Kom igång med Power BI-tjänsten](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort alla resurser genom att använda följande kommando så att du inte debiteras för dem.

1. Om du vill ta bort resurs gruppen anger du kommandot:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Om du vill ta bort tjänstens huvud namn anger du följande kommandon:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in (ETL) i skala](./hadoop/apache-hadoop-etl-at-scale.md)
