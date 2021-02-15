---
title: Beräknings miljöer som stöds av Azure Data Factory
description: Beräknings miljöer som kan användas med Azure Data Factory pipelines (till exempel Azure HDInsight) för att transformera eller bearbeta data.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 05/08/2019
ms.openlocfilehash: 7dae067b5d8648f1441047c26f8792e55591b64d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368531"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Beräknings miljöer som stöds av Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs olika beräknings miljöer som du kan använda för att bearbeta eller transformera data. Den innehåller också information om olika konfigurationer (på begäran eller ta med din egen) som stöds av Data Factory när du konfigurerar länkade tjänster som länkar dessa beräknings miljöer till en Azure Data Factory.

Följande tabell innehåller en lista över beräknings miljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [HDInsight-kluster på begäran](#azure-hdinsight-on-demand-linked-service) eller [ditt eget HDInsight-kluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [gris](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop-direktuppspelning](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Anpassad](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio (klassisk)](#azure-machine-learning-studio-classic-linked-service) | [Machine Learning Studio (klassisk) aktiviteter: batch-körning och uppdaterings resurs](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning kör pipeline](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Lagrad procedur](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [python](transform-data-databricks-python.md) |
| [Azure-funktion](#azure-function-linked-service)         | [Azure Function-aktivitet](control-flow-azure-function-activity.md)
>  

## <a name="hdinsight-compute-environment"></a>Beräknings miljö för HDInsight

Se tabellen nedan för information om de länkade tjänst typerna som stöds för konfiguration på begäran och BYOC (ta med din egen beräknings miljö).

| I Compute-länkad tjänst | Egenskapens namn                | Beskrivning                                                  | Blob | ADLS Gen2 | Azure SQL-databas | ADLS Gen 1 |
| ------------------------- | ---------------------------- | ------------------------------------------------------------ | ---- | --------- | ------------ | ---------- |
| På begäran                 | linkedServiceName            | Azure Storage länkad tjänst som ska användas av klustret på begäran för att lagra och bearbeta data. | Ja  | Ja       | Inga           | Inga         |
|                           | additionalLinkedServiceNames | Anger ytterligare lagrings konton för den länkade HDInsight-tjänsten så att tjänsten Data Factory kan registrera dem åt dig. | Ja  | Inga        | Inga           | Inga         |
|                           | hcatalogLinkedServiceName    | Namnet på den länkade Azure SQL-tjänsten som pekar på HCatalog-databasen. HDInsight-klustret på begäran skapas med hjälp av Azure SQL Database som metaarkiv. | Inga   | Inga        | Ja          | Inga         |
| BYOC                      | linkedServiceName            | Den Azure Storage länkade tjänst referensen.                | Ja  | Ja       | Inga           | Inga         |
|                           | additionalLinkedServiceNames | Anger ytterligare lagrings konton för den länkade HDInsight-tjänsten så att tjänsten Data Factory kan registrera dem åt dig. | Inga   | Inga        | Inga           | Inga         |
|                           | hcatalogLinkedServiceName    | En referens till den länkade Azure SQL-tjänsten som pekar på HCatalog-databasen. | Inga   | Inga        | Inga           | Inga         |

### <a name="azure-hdinsight-on-demand-linked-service"></a>Länkad tjänst för Azure HDInsight på begäran

I den här typen av konfiguration hanteras dator miljön fullständigt av Azure Data Factory tjänsten. Den skapas automatiskt av Data Factory tjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet har slutförts. Du kan skapa en länkad tjänst för beräknings miljön på begäran, konfigurera den och kontrol lera detaljerade inställningar för jobb körning, kluster hantering och start åtgärder.

> [!NOTE]
> Konfigurationen på begäran stöds för närvarande endast för Azure HDInsight-kluster. Azure Databricks stöder också jobb på begäran med hjälp av jobb kluster. Mer information finns i den [länkade tjänsten Azure databricks](#azure-databricks-linked-service).

Azure Data Factorys tjänsten kan automatiskt skapa ett HDInsight-kluster på begäran för att bearbeta data. Klustret skapas i samma region som lagrings kontot (linkedServiceName-egenskapen i JSON) som är associerad med klustret. Lagrings kontot `must` är ett allmänt standard Azure Storage-konto. 

Observera följande **viktiga** punkter om den länkade HDInsight-tjänsten på begäran:

* HDInsight-klustret på begäran skapas i din Azure-prenumeration. Du kan se klustret i Azure Portal när klustret är igång. 
* Loggarna för jobb som körs på ett HDInsight-kluster på begäran kopieras till det lagrings konto som är associerat med HDInsight-klustret. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword som definieras i den länkade tjänst definitionen används för att logga in på klustret för djupgående fel sökning under klustrets livs cykel. 
* Du debiteras bara för den tid då HDInsight-klustret är igång.
* Du kan använda en **skript åtgärd** med en länkad Azure HDInsight-tjänst på begäran.  

> [!IMPORTANT]
> Det tar vanligt vis **20 minuter** eller mer att etablera ett Azure HDInsight-kluster på begäran.

#### <a name="example"></a>Exempel

Följande JSON definierar en Linux-baserad länkad HDInsight-tjänst på begäran. Tjänsten Data Factory skapar automatiskt ett **Linux-baserat HDInsight-** kluster för att bearbeta den begärda aktiviteten. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> HDInsight-klustret skapar en **standard behållare** i blob-lagringen som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här containern när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar. 
>
> När fler aktiviteter körs ser du många behållare i Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här containrarna följer ett mönster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Använd verktyg som [Microsoft Azure Storage Explorer](https://storageexplorer.com/) för att ta bort behållare i Azure Blob Storage.

#### <a name="properties"></a>Egenskaper

| Egenskap                     | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Egenskapen Type ska anges till **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Antalet arbets uppgifter/datanoder i klustret. HDInsight-klustret skapas med två head-noder och antalet arbetsnoder som du anger för den här egenskapen. Noderna har storlek Standard_D3 som har 4 kärnor, så ett kluster med en arbets nod tar 24 kärnor (4 \* 4 = 16 kärnor för arbetsnoder, plus 2 \* 4 = 8 kärnor för Head-noder). Se [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka och mycket mer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) för mer information. | Ja      |
| linkedServiceName            | Azure Storage länkad tjänst som ska användas av klustret på begäran för att lagra och bearbeta data. HDInsight-klustret skapas i samma region som det här Azure Storage kontot. Azure HDInsight har en begränsning för hur många kärnor du kan använda i varje Azure-region som stöds. Se till att du har tillräckligt många kärn kvoter i Azure-regionen för att uppfylla de nödvändiga clusterSize. Mer information finns i [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka och mycket mer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>För närvarande kan du inte skapa ett HDInsight-kluster på begäran som använder en Azure Data Lake Storage (Gen 2) som lagrings plats. Om du vill lagra resultat data från HDInsight-bearbetning i en Azure Data Lake Storage (Gen 2) använder du en kopierings aktivitet för att kopiera data från Azure-Blob Storage till Azure Data Lake Storage (Gen 2). </p> | Ja      |
| clusterResourceGroup         | HDInsight-klustret skapas i den här resurs gruppen. | Ja      |
| TimeToLive                   | Den tillåtna inaktiva tiden för HDInsight-klustret på begäran. Anger hur länge HDInsight-klustret på begäran förblir aktivt efter att en aktivitets körning slutförts om det inte finns några andra aktiva jobb i klustret. Det minsta tillåtna värdet är 5 minuter (00:05:00).<br/><br/>Om en aktivitets körning till exempel tar 6 minuter och TimeToLive har angetts till 5 minuter förblir klustret aktiv i 5 minuter efter 6 minuters bearbetning av aktivitets körningen. Om en annan aktivitets körning körs med fönstret 6 minuter, bearbetas den av samma kluster.<br/><br/>Att skapa ett HDInsight-kluster på begäran är en dyr åtgärd (kan ta en stund), så Använd den här inställningen vid behov för att förbättra prestandan hos en data fabrik genom att återanvända ett HDInsight-kluster på begäran.<br/><br/>Om du anger TimeToLive-värdet till 0, tas klustret bort så snart aktiviteten körs klart. Om du anger ett högt värde kan klustret vara inaktivt så att du kan logga in för vissa fel söknings syfte men det kan resultera i höga kostnader. Därför är det viktigt att du anger rätt värde utifrån dina behov.<br/><br/>Om värdet för egenskapen TimeToLive har angetts korrekt kan flera pipelines dela instansen av HDInsight-klustret på begäran. | Ja      |
| clusterType                  | Typ av HDInsight-kluster som ska skapas. Tillåtna värden är "Hadoop" och "Spark". Om inget värde anges är standardvärdet Hadoop. Det går inte att skapa Enterprise Security Package aktiverat kluster på begäran, i stället använda ett [befintligt kluster/ta med din egen beräkning](#azure-hdinsight-linked-service). | Inga       |
| version                      | Version av HDInsight-klustret. Om det inte anges används den aktuella HDInsight-definierade standard versionen. | Inga       |
| hostSubscriptionId           | ID för Azure-prenumerationen som används för att skapa HDInsight-kluster. Om inget anges används prenumerations-ID: t för din Azure-inloggnings kontext. | Inga       |
| clusterNamePrefix           | Prefixet för HDI-kluster namn, en tidsstämpel läggs automatiskt till i slutet av kluster namnet| Inga       |
| sparkVersion                 | Versionen av Spark om kluster typen är "Spark" | Inga       |
| additionalLinkedServiceNames | Anger ytterligare lagrings konton för den länkade HDInsight-tjänsten så att tjänsten Data Factory kan registrera dem åt dig. Dessa lagrings konton måste finnas i samma region som HDInsight-klustret, som skapas i samma region som det lagrings konto som anges av linkedServiceName. | Inga       |
| osType                       | Typ av operativ system. Tillåtna värden är: Linux och Windows (endast för HDInsight 3,3). Standardvärdet är Linux. | Inga       |
| hcatalogLinkedServiceName    | Namnet på den länkade Azure SQL-tjänsten som pekar på HCatalog-databasen. HDInsight-klustret på begäran skapas genom att använda Azure SQL Database som metaarkiv. | Inga       |
| connectVia                   | Integration Runtime som ska användas för att skicka aktiviteter till den länkade HDInsight-tjänsten. För den länkade HDInsight-tjänsten på begäran stöder den bara Azure Integration Runtime. Om inget värde anges används standard Azure Integration Runtime. | Inga       |
| clusterUserName                   | Användar namnet för att få åtkomst till klustret. | Inga       |
| clusterPassword                   | Lösen ordet i typ av säker sträng för att komma åt klustret. | Inga       |
| clusterSshUserName         | Användar namnet för SSH fjärr anslutning till klustrets nod (för Linux). | Inga       |
| clusterSshPassword         | Lösen ordet i typ av säker sträng till SSH fjärran slutet av klustrets nod (för Linux). | Inga       |
| scriptActions | Ange skript för [anpassningar av HDInsight-kluster](../hdinsight/hdinsight-hadoop-customize-cluster-linux.md) när du skapar kluster på begäran. <br />För närvarande stöder Azure Data Factory användar gränssnitts redigerings verktyget bara en skript åtgärd, men du kan få igenom denna begränsning i JSON (ange flera skript åtgärder i JSON). | Inga |


> [!IMPORTANT]
> HDInsight stöder flera Hadoop-kluster versioner som kan distribueras. Varje versions val skapar en speciell version av HDP-distributionen (Hortonworks Data Platform) och en uppsättning komponenter som ingår i distributionen. Listan över de HDInsight-versioner som stöds fortsätter att uppdateras för att tillhandahålla de senaste komponenterna och korrigeringarna för Hadoop-eko systemet. Se till att du alltid refererar till den senaste informationen om [vilka HDInsight-versioner som stöds och vilken typ av operativ system](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) som stöds. 
>
> [!IMPORTANT]
> För närvarande stöder inte HDInsight-länkade tjänster HBase, interaktiv fråga (Hive LLAP), storm. 

* additionalLinkedServiceNames JSON-exempel

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

#### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Den länkade HDInsight-tjänsten på begäran kräver en tjänstens huvud namns autentisering för att skapa HDInsight-kluster för din räkning. Om du vill använda tjänstens huvud namns autentisering registrerar du en programentitet i Azure Active Directory (Azure AD) och tilldelar den rollen **deltagare** i prenumerationen eller resurs gruppen där HDInsight-klustret skapas. Detaljerade anvisningar finns i [använda portalen för att skapa ett Azure Active Directory program och tjänstens huvud namn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID
- Program nyckel 
- Klient-ID:t

Använd tjänstens huvud namns autentisering genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckel.           | Ja      |
| **tenant**              | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja      |

#### <a name="advanced-properties"></a>Avancerade egenskaper

Du kan också ange följande egenskaper för den detaljerade konfigurationen av HDInsight-klustret på begäran.

| Egenskap               | Beskrivning                              | Krävs |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger kärn konfigurations parametrar (som i core-site.xml) för HDInsight-klustret som ska skapas. | Inga       |
| hBaseConfiguration     | Anger konfigurations parametrar för HBase (hbase-site.xml) för HDInsight-klustret. | Inga       |
| hdfsConfiguration      | Anger konfigurations parametrar för HDFS (hdfs-site.xml) för HDInsight-klustret. | Inga       |
| hiveConfiguration      | Anger Hive-konfigurations parametrar (hive-site.xml) för HDInsight-klustret. | Inga       |
| mapReduceConfiguration | Anger konfigurations parametrar för MapReduce (mapred-site.xml) för HDInsight-klustret. | Inga       |
| oozieConfiguration     | Anger konfigurations parametrar för Oozie (oozie-site.xml) för HDInsight-klustret. | Inga       |
| stormConfiguration     | Anger Storm-konfigurations parametrar (storm-site.xml) för HDInsight-klustret. | Inga       |
| yarnConfiguration      | Anger konfigurations parametrar för garn (yarn-site.xml) för HDInsight-klustret. | Inga       |

* Exempel – konfiguration av HDInsight-kluster på begäran med avancerade egenskaper

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

#### <a name="node-sizes"></a>Node-storlekar
Du kan ange storlekarna på huvud-, data-och Zookeeper-noderna med följande egenskaper: 

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Anger Head-nodens storlek. Standardvärdet är: Standard_D3. Mer information finns i avsnittet **Ange Node-storlekar** . | Inga       |
| dataNodeSize      | Anger data nodens storlek. Standardvärdet är: Standard_D3. | Inga       |
| zookeeperNodeSize | Anger storleken på den Zoo Keeper-noden. Standardvärdet är: Standard_D3. | Inga       |

* Om du anger en Node-storlek ser du [storlekarna för Virtual Machines](../virtual-machines/sizes.md) artikel för sträng värden som du måste ange för egenskaperna som anges i föregående avsnitt. Värdena måste följa **cmdletarna &-API: er** som refereras till i artikeln. Som du kan se i artikeln har datanoden för stor (standard) storlek 7 GB minne, vilket kanske inte är tillräckligt för ditt scenario. 

Om du vill skapa värden för D4 size-Head och Worker, anger du **Standard_D4** som värde för egenskaperna HeadNodeSize och dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för dessa egenskaper kan du få följande **fel meddelande:** det gick inte att skapa klustret. Undantag: Unable to complete the cluster create operation. (Det går inte att slutföra åtgärden att skapa ett kluster.) Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Cluster left behind state: 'Error'. (Klustret efterlämnade status: Fel.) Meddelande: ' PreClusterCreationValidationFailure '. När du får det här felet måste du kontrol lera att du använder **cmdleten & API: er** namn i tabellen i [Virtual Machiness artikel storlek](../virtual-machines/sizes.md) .

### <a name="bring-your-own-compute-environment"></a>Ta med din egen beräknings miljö
I den här typen av konfiguration kan användarna registrera en redan befintlig dator miljö som en länkad tjänst i Data Factory. Dator miljön hanteras av användaren och den Data Factory tjänsten använder den för att köra aktiviteterna.

Den här typen av konfiguration stöds för följande beräknings miljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure Synapse Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Länkad Azure HDInsight-tjänst
Du kan skapa en länkad Azure HDInsight-tjänst för att registrera ditt eget HDInsight-kluster med Data Factory.

### <a name="example"></a>Exempel

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                                                  | Krävs |
| ----------------- | ------------------------------------------------------------ | -------- |
| typ              | Egenskapen Type ska anges till **HDInsight**.            | Ja      |
| clusterUri        | URI för HDInsight-klustret.                            | Ja      |
| användarnamn          | Ange namnet på den användare som ska användas för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| password          | Ange lösen ordet för användar kontot.                       | Ja      |
| linkedServiceName | Namnet på den länkade tjänsten Azure Storage som refererar till Azure Blob Storage som används av HDInsight-klustret. <p>För närvarande kan du inte ange en länkad Azure Data Lake Storage-tjänst (Gen 2) för den här egenskapen. Om HDInsight-klustret har åtkomst till Data Lake Store, kan du komma åt data i Azure Data Lake Storage (Gen 2) från Hive/gris-skript. </p> | Ja      |
| isEspEnabled      | Ange *True* om HDInsight-klustret är [Enterprise Security Package](../hdinsight/domain-joined/apache-domain-joined-architecture.md) aktiverat. Standardvärdet är *false*. | Inga       |
| connectVia        | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om inget värde anges används standard Azure Integration Runtime. <br />För Enterprise Security Package (ESP) aktiverat HDInsight-kluster använder du en lokal integration runtime, som har en detaljerad linje för klustret, eller så bör det distribueras inuti samma Virtual Network som ESP HDInsight-klustret. | Inga       |

> [!IMPORTANT]
> HDInsight stöder flera Hadoop-kluster versioner som kan distribueras. Varje versions val skapar en speciell version av HDP-distributionen (Hortonworks Data Platform) och en uppsättning komponenter som ingår i distributionen. Listan över de HDInsight-versioner som stöds fortsätter att uppdateras för att tillhandahålla de senaste komponenterna och korrigeringarna för Hadoop-eko systemet. Se till att du alltid refererar till den senaste informationen om [vilka HDInsight-versioner som stöds och vilken typ av operativ system](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) som stöds. 
>
> [!IMPORTANT]
> För närvarande stöder inte HDInsight-länkade tjänster HBase, interaktiv fråga (Hive LLAP), storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch länkad tjänst

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skapa en Azure Batch länkad tjänst för att registrera en batch-pool med virtuella datorer (VM) till en data fabrik. Du kan köra anpassad aktivitet med Azure Batch.

Se följande artiklar om du är nybörjare på Azure Batch-tjänsten:

* [Azure Batch grunderna](../batch/batch-technical-overview.md) för en översikt över Azure Batchs tjänsten.
* [New-AzBatchAccount-](/powershell/module/az.batch/New-azBatchAccount) cmdlet för att skapa ett Azure Batch konto (eller) [Azure Portal](../batch/batch-account-create-portal.md) för att skapa Azure Batch-kontot med hjälp av Azure Portal. Detaljerade anvisningar om hur du använder cmdleten finns i [använda PowerShell för att hantera Azure Batch konto](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) artikel.
* [New-AzBatchPool-](/powershell/module/az.batch/New-AzBatchPool) cmdlet för att skapa en Azure Batch-pool.

### <a name="example"></a>Exempel

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Egenskapen Type ska anges till **AzureBatch**. | Ja      |
| accountName       | Namnet på Azure Batch kontot.         | Ja      |
| accessKey         | Åtkomst nyckel för Azure Batch kontot.  | Ja      |
| batchUri          | URL till ditt Azure Batch-konto, i formatet https://*batchaccountname. region*. batch.Azure.com. | Ja      |
| poolName          | Namn på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på den länkade tjänsten Azure Storage som är kopplad till den här Azure Batch länkade tjänsten. Den här länkade tjänsten används för uppsamlingsfiler som krävs för att köra aktiviteten. | Ja      |
| connectVia        | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om inget värde anges används standard Azure Integration Runtime. | Inga       |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (klassisk) länkad tjänst
Du skapar en Azure Machine Learning Studio (klassisk) länkad tjänst för att registrera en Machine Learning Studio (klassisk) slut punkt för batch-poäng till en data fabrik.

### <a name="example"></a>Exempel

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Egenskaper
| Egenskap               | Beskrivning                              | Krävs                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Egenskapen Type ska anges till: **azureml**. | Ja                                      |
| mlEndpoint             | URL för batch-poäng.                   | Ja                                      |
| apiKey                 | Den publicerade arbets ytans modells API.     | Ja                                      |
| updateResourceEndpoint | Uppdaterings resursens URL för en Azure Machine Learning Studio (klassisk) webb tjänst slut punkt som används för att uppdatera den förutsägbara webb tjänsten med utbildad modell fil | Inga                                       |
| servicePrincipalId     | Ange programmets klient-ID.     | Krävs om updateResourceEndpoint har angetts |
| servicePrincipalKey    | Ange programmets nyckel.           | Krävs om updateResourceEndpoint har angetts |
| tenant                 | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Krävs om updateResourceEndpoint har angetts |
| connectVia             | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om inget värde anges används standard Azure Integration Runtime. | Inga                                       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst
Du skapar en Azure Machine Learning länkad tjänst för att ansluta en Azure Machine Learning arbets yta till en data fabrik.

> [!NOTE]
> För närvarande stöds endast autentisering av tjänstens huvud namn för den länkade tjänsten Azure Machine Learning.

### <a name="example"></a>Exempel

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Egenskaper
| Egenskap               | Beskrivning                              | Krävs                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Egenskapen Type ska anges till: **AzureMLService**. | Ja                                      |
| subscriptionId         | ID för Azure-prenumeration              | Ja                                      |
| resourceGroupName      | name | Ja                                      |
| mlWorkspaceName        | Namn på Azure Machine Learning arbets yta | Ja  |
| servicePrincipalId     | Ange programmets klient-ID.     | Inga |
| servicePrincipalKey    | Ange programmets nyckel.           | Inga |
| tenant                 | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Krävs om updateResourceEndpoint har angetts | Inga |
| connectVia             | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om inget värde anges används standard Azure Integration Runtime. | Inga |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka en Azure Data Lake Analytics Compute-tjänst till en Azure Data Factory. Data Lake Analytics U-SQL-aktiviteten i pipelinen refererar till den här länkade tjänsten. 

### <a name="example"></a>Exempel

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Egenskaper

| Egenskap             | Beskrivning                              | Krävs                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| typ                 | Egenskapen Type ska anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | Azure Data Lake Analytics konto namn.  | Ja                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics-URI.           | Inga                                       |
| subscriptionId       | ID för Azure-prenumeration                    | Inga                                       |
| resourceGroupName    | Azure-resursgruppsnamn                | Inga                                       |
| servicePrincipalId   | Ange programmets klient-ID.     | Ja                                      |
| servicePrincipalKey  | Ange programmets nyckel.           | Ja                                      |
| tenant               | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja                                      |
| connectVia           | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om inget värde anges används standard Azure Integration Runtime. | Inga                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks länkad tjänst
Du kan skapa **Azure Databricks länkade tjänsten** för att registrera Databricks-arbetsyta som du använder för att köra Databricks-arbetsbelastningar (Notebook, JAR, python). 
> [!IMPORTANT]
> Databricks länkade tjänster stöder [instans pooler](https://aka.ms/instance-pools) & systemtilldelad autentisering med hanterad identitet.

### <a name="example---using-new-job-cluster-in-databricks"></a>Exempel – använda nytt jobb kluster i Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exempel – använda befintligt interaktivt kluster i Databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Egenskaper

| Egenskap             | Beskrivning                              | Obligatorisk                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Namnet på den länkade tjänsten               | Ja   |
| typ                 | Egenskapen Type ska anges till: **Azure Databricks**. | Ja                                      |
| domän               | Ange Azure-regionen baserat på regionen för Databricks-arbetsytan. Exempel: https://eastus.azuredatabricks.net | Ja                                 |
| accessToken          | Åtkomsttoken krävs för att Data Factory ska kunna autentisera till Azure Databricks. Åtkomsttoken måste genereras från arbets ytan databricks. Mer detaljerade anvisningar för att hitta åtkomsttoken finns [här](/azure/databricks/dev-tools/api/latest/authentication#generate-token)  | Inga                                       |
| MSI          | Använd Data Factory hanterad identitet (systemtilldelad) för att autentisera till Azure Databricks. Du behöver inte åtkomsttoken när du använder MSI-autentisering  | Inga                                       |
| existingClusterId    | Kluster-ID för ett befintligt kluster att köra alla jobb på detta. Detta bör vara ett interaktivt kluster som redan skapats. Du kan behöva starta om klustret manuellt om det slutar svara. Databricks föreslå jobb som körs på nya kluster för bättre tillförlitlighet. Du hittar kluster-ID: t för ett interaktivt kluster på Databricks-arbetsytan – > kluster – > interaktivt kluster namn-> konfigurations > taggar. [Mer information](https://docs.databricks.com/user-guide/clusters/tags.html) | Inga 
| instancePoolId    | Instans-pool-ID för en befintlig pool i databricks-arbetsytan.  | Inga  |
| newClusterVersion    | Spark-versionen av klustret. Det skapar ett jobb kluster i databricks. | Inga  |
| newClusterNumOfWorker| Antal arbetsnoder som det här klustret ska ha. Ett kluster har en spark-drivrutin och num_workers körningar för totalt num_workers + 1 Spark-noder. En sträng formaterad Int32, t. ex. "1" betyder att numOfWorker är 1 eller "1:10" betyder autoskalning från 1 till och med 10 som Max.  | Inga                |
| newClusterNodeType   | Det här fältet kodas med hjälp av ett enda värde och resurserna som är tillgängliga för var och en av Spark-noderna i det här klustret. Till exempel kan Spark-noderna tillhandahållas och optimeras för minnes-eller beräknings intensiva arbets belastningar. Det här fältet krävs för det nya klustret                | Inga               |
| newClusterSparkConf  | en uppsättning valfria, användardefinierade konfigurations nyckel/värde-par för Spark-konfiguration. Användarna kan också skicka en sträng med extra JVM-alternativ till driv rutinen och körningarna via Spark. driver. extraJavaOptions och spark.executor. extraJavaOptions. | Inga  |
| newClusterInitScripts| en uppsättning valfria, användardefinierade initierings skript för det nya klustret. Ange DBFS-sökvägen till init-skripten. | Inga  |


## <a name="azure-sql-database-linked-service"></a>Länkad Azure SQL Database-tjänst

Du skapar en länkad Azure SQL-tjänst och använder den med den [lagrade procedur aktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory pipeline. Se artikeln om [Azure SQL Connector](connector-azure-sql-database.md#linked-service-properties) för information om den här länkade tjänsten.

## <a name="azure-synapse-analytics-linked-service"></a>Länkad Azure Synapse Analytics-tjänst

Du skapar en länkad Azure Synapse Analytics-tjänst och använder den med den [lagrade procedur aktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory pipeline. Se artikeln om [Azure Synapse Analytics Connector](connector-azure-sql-data-warehouse.md#linked-service-properties) för information om den här länkade tjänsten.

## <a name="sql-server-linked-service"></a>SQL Server länkad tjänst

Du skapar en SQL Server länkad tjänst och använder den med den [lagrade procedur aktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory pipeline. Se [SQL Server Connector](connector-sql-server.md#linked-service-properties) -artikeln för information om den här länkade tjänsten.

## <a name="azure-function-linked-service"></a>Länkad Azure Function-tjänst

Du skapar en länkad Azure Function-tjänst och använder den med [Azure Function-aktiviteten](control-flow-azure-function-activity.md) för att köra Azure Functions i en Data Factory pipeline. Retur typen för Azure-funktionen måste vara giltig `JObject` . (Tänk på att [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *inte* är en `JObject` .) Andra retur typer än `JObject` Miss lyckas och det går inte att skicka användar fel *svars innehållet är inte ett giltigt JObject*.

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| typ   | Egenskapen Type måste anges till: **AzureFunction** | ja |
| URL till Function-app | URL för Azure-Funktionsapp. Formatet är `https://<accountname>.azurewebsites.net` . URL: en är värdet under **URL** -avsnittet när du visar Funktionsapp i Azure Portal  | ja |
| funktions nyckel | Åtkomst nyckel för Azure-funktionen. Klicka på **Hantera** -avsnittet för respektive funktion och kopiera antingen **funktions nyckeln** eller **värd nyckeln**. Läs mer här: [Azure Functions HTTP-utlösare och bindningar](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="next-steps"></a>Nästa steg

En lista över de omvandlings aktiviteter som stöds av Azure Data Factory finns i [transformera data](transform-data.md).
