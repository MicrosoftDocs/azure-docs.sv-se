---
title: Apache Spark & Apache Kafka med Cosmos DB – Azure HDInsight
description: Lär dig hur du använder Apache Spark strukturerad strömning för att läsa data från Apache Kafka och sedan lagra dem i Azure Cosmos DB. I det här exemplet strömmar du data med hjälp av en Jupyter Notebook från Spark på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/18/2019
ms.openlocfilehash: 0f07cf563a3b1ce9d105fc5ca5c4f2869b13d2da
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821782"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Använd Apache Spark strukturerad strömning med Apache Kafka och Azure Cosmos DB

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) [strukturerad strömning](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) för att läsa data från [Apache Kafka](https://kafka.apache.org/) i Azure HDInsight och sedan lagra data i Azure Cosmos dB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är en globalt distribuerad databas för flera modeller. I det här exemplet används en SQL-API-databas modell. Mer information finns i [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md) Document.

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data. Mer information om strukturerad strömning finns i [programmerings guiden för strukturerad strömning](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) på Apache.org.

> [!IMPORTANT]  
> I det här exemplet användes Spark 2,2 på HDInsight 3,6.
>
> Stegen i det här dokumentet skapar en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Kom ihåg att ta bort klustren för att undvika onödiga avgifter när du är klar med stegen i det här dokumentet.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka i HDInsight ger inte åtkomst till Kafka-utjämnare via det offentliga Internet. Allt som pratar med Kafka måste finnas i samma virtuella Azure-nätverk som noderna i Kafka-klustret. I det här exemplet finns både Kafka-och Spark-klustren i ett virtuellt Azure-nätverk. Följande diagram visar hur kommunikations flöden mellan klustren:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka-tjänsten är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Även om du kan skapa ett virtuellt Azure-nätverk, Kafka och Spark-kluster manuellt, är det enklare att använda en Azure Resource Manager mall. Använd följande steg för att distribuera ett virtuellt Azure-nätverk, Kafka och Spark-kluster till din Azure-prenumeration.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Azure Resource Manager-mallen finns i GitHub-lagringsplatsen för det här projektet ( [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) ).

    Den här mallen skapar följande resurser:

   * En Kafka på HDInsight 3.6-klustret.

   * Ett spark på HDInsight 3,6-kluster.

   * Ett virtuellt Azure-nätverk som innehåller HDInsight-klustren. Det virtuella nätverk som skapas av mallen använder adress utrymmet 10.0.0.0/16.

   * En Azure Cosmos DB SQL API-databas.

    > [!IMPORTANT]  
    > Anteckningsboken för Structured Streaming som används i det här exemplet kräver Apache Spark i HDInsight 3.6. Om du använder en tidigare version av Apache Spark i HDInsight får du ett felmeddelande när du använder anteckningsboken.

1. Använd följande information för att fylla i posterna i avsnittet **Anpassad distribution** :

    |Egenskap |Värde |
    |---|---|
    |Prenumeration|Välj din Azure-prenumeration.|
    |Resursgrupp|Skapa en grupp eller Välj en befintlig. Den här gruppen innehåller HDInsight-klustret.|
    |Cosmos DB konto namn|Det här värdet används som namn på det Cosmos DB kontot. Namnet får endast innehålla gemener, siffror och bindestreck (-). Det måste innehålla 3–31 tecken.|
    |Bas kluster namn|Det här värdet används som grund namn för Spark-och Kafka-klustren. Om du till exempel anger **myhdi** skapas ett Spark-kluster med namnet __Spark-Myhdi__ och ett Kafka-kluster med namnet **Kafka-myhdi**.|
    |Kluster version|HDInsight-klustrets version. Det här exemplet testas med HDInsight 3,6 och fungerar kanske inte med andra kluster typer.|
    |Användarnamn för klusterinloggning|Administratörs användar namnet för Spark-och Kafka-klustren.|
    |Lösenord för klusterinloggning|Administratörs användarens lösen ord för Spark-och Kafka-kluster.|
    |Användar namn för SSH|SSH-användaren som ska skapas för Spark-och Kafka-kluster.|
    |SSH-lösenord|Lösen ordet för SSH-användaren för Spark-och Kafka-klustren.|

    ![Anpassade värden för HDInsight-distribution](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

1. Välj slutligen **köp**. Det kan ta upp till 45 minuter att skapa kluster, virtuellt nätverk och Cosmos DB konto.

## <a name="create-the-cosmos-db-database-and-collection"></a>Skapa Cosmos DB databas och samling

Projektet som används i det här dokumentet lagrar data i Cosmos DB. Innan du kör koden måste du först skapa en _databas_ och _samling_ i Cosmos DB-instansen. Du måste också hämta dokument slut punkten och _nyckeln_ som används för att autentisera begär anden till Cosmos dB.

Ett sätt att göra detta är att använda [Azure CLI](/cli/azure/). Följande skript kommer att skapa en databas med namnet `kafkadata` och en samling med namnet `kafkacollection` . Den returnerar sedan den primära nyckeln.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter Notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

Dokument slut punkten och primär nyckelinformation liknar följande text:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Spara slut punkts-och nyckel värden eftersom de behövs i antecknings böckerna för Jupyter.

## <a name="get-the-notebooks"></a>Hämta antecknings böckerna

Koden för exemplet som beskrivs i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) .

## <a name="upload-the-notebooks"></a>Ladda upp anteckningsböckerna

Använd följande steg för att ladda upp antecknings böckerna från projektet till ditt Spark på HDInsight-kluster:

1. I webbläsaren ansluter du till Jupyter Notebook i Spark-klustret. I följande URL ersätter du `CLUSTERNAME` med namnet på ditt __Apache Spark__-kluster:

    ```http
    https://CLUSTERNAME.azurehdinsight.net/jupyter
    ```

    Ange klusterinloggningen (administratör) och det lösenord som användes när du skapade klustret.

2. Från den övre högra sidan av sidan använder du knappen __Ladda upp__ för att ladda upp data __-till-Kafka. ipynb-filen från Stream-taxi-data__ till klustret. Välj __Öppna__ för att påbörja uppladdningen.

3. Leta upp posten __Stream-taxi-data-till-Kafka. ipynb__ i listan över antecknings böcker och välj knappen __Ladda upp__ bredvid den.

4. Upprepa steg 1-3 för att läsa in __Stream-data-från-Kafka-till-Cosmos-dB. ipynb__ Notebook.

## <a name="load-taxi-data-into-kafka"></a>Läs in taxi data till Kafka

När filerna har laddats upp väljer du posten __Stream-taxi-data-till-Kafka. ipynb__ för att öppna antecknings boken. Följ stegen i antecknings boken för att läsa in data i Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Bearbeta taxi data med Spark-strukturerad strömning

På Start sidan för [Jupyter Notebook](https://jupyter.org/) väljer du posten __Stream-data-från-Kafka-till-Cosmos-dB. ipynb__ . Följ stegen i antecknings boken för att strömma data från Kafka och till Azure Cosmos DB använda Spark-strukturerad strömning.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Apache Spark strukturerad strömning kan du läsa mer om hur du arbetar med Apache Spark, Apache Kafka och Azure Cosmos DB i följande dokument:

* [Använda Apache Spark streaming (DStream) med Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Börja med Jupyter Notebook och Apache Spark på HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Välkommen till Azure Cosmos DB](../cosmos-db/introduction.md)
