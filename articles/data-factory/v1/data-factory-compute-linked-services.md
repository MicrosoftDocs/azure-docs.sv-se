---
title: Beräknings miljöer som stöds av Azure Data Factory version 1
description: Lär dig mer om beräknings miljöer som du kan använda i Azure Data Factory pipelines (till exempel Azure HDInsight) för att transformera eller bearbeta data.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 07e13036a427ff7ff4f0cbbd2dfd6fbe272bf915
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377201"
---
# <a name="compute-environments-supported-by-azure-data-factory-version-1"></a>Beräknings miljöer som stöds av Azure Data Factory version 1
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av Data Factorys tjänsten, se [Compute-länkade tjänster i](../compute-linked-services.md).

I den här artikeln beskrivs de beräknings miljöer som du kan använda för att bearbeta eller transformera data. Den innehåller också information om olika konfigurationer (på begäran jämfört med att göra egna) som Data Factory stöder när du konfigurerar länkade tjänster som länkar dessa beräknings miljöer till en Azure Data Factory.

Följande tabell innehåller en lista över beräknings miljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                      | Aktiviteter                               |
| ---------------------------------------- | ---------------------------------------- |
| [Azure HDInsight-kluster på begäran](#azure-hdinsight-on-demand-linked-service) eller [ditt eget HDInsight-kluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [gris](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klassisk)](#azure-machine-learning-studio-classic-linked-service) | [Studio (klassiska) aktiviteter: batch-körning och uppdaterings resurs](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight-versioner som stöds i Data Factory
Azure HDInsight stöder flera Hadoop-kluster som du kan distribuera när som helst. Varje version som stöds skapar en speciell version av HDP-distributionen (Hortonworks Data Platform) och en uppsättning komponenter i distributionen. 

Microsoft uppdaterar listan över HDInsight-versioner som stöds med de senaste komponenterna och korrigeringarna för Hadoop-eko systemet. Detaljerad information finns i [HDInsight-versioner som stöds](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux-baserade HDInsight version 3,3 har dragits tillbaka den 31 juli 2017. Data Factory version 1 kund tjänster som är länkade till HDInsight till den 15 december 2017, för att testa och uppgradera till en senare version av HDInsight. Windows-baserad HDInsight kommer att dras tillbaka den 31 juli 2018.
>
> 

### <a name="after-the-retirement-date"></a>Efter indragnings datumet 

Efter den 15 december 2017:

- Du kan inte längre skapa Linux-baserade HDInsight version 3,3-kluster (eller tidigare versioner) med hjälp av en länkad HDInsight-tjänst på begäran i Data Factory version 1. 
- Om egenskaperna [ **OsType** och **version**](#azure-hdinsight-on-demand-linked-service) inte uttryckligen anges i JSON-definitionen för en befintlig data Factory version 1-länkad HDInsight-tjänst på begäran, ändras standardvärdet från **version = 3.1, osType = Windows** till **version = \<latest HDI default version\> ( https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) , osType = Linux**.

Efter den 31 juli 2018:

- Du kan inte längre skapa någon version av Windows-baserade HDInsight-kluster med hjälp av en länkad HDInsight-tjänst på begäran i Data Factory version 1. 

### <a name="recommended-actions"></a>Rekommenderade åtgärder

- För att säkerställa att du kan använda de senaste komponenterna och korrigeringarna för Hadoop-eko system, uppdaterar du [egenskaperna **OsType** och **version**](#azure-hdinsight-on-demand-linked-service) för Data Factory berörda HDInsight-länkade tjänst definitioner på begäran till nyare Linux-baserade HDInsight-versioner (HDInsight 3,6). 
- Före den 15 december 2017, test Data Factory version 1 Hive-, gris-, MapReduce-och Hadoop streaming-aktiviteter som hänvisar till den berörda länkade tjänsten. Se till att de är kompatibla med de nya **osType** -och standardvärdena för **version** (**version = 3.6**, **osType = Linux**) eller den explicita HDInsight-version och OS-typ som du uppgraderar till. 
  Mer information om kompatibilitet finns i [Migrera från ett Windows-baserat HDInsight-kluster till ett Linux-baserat kluster](../../hdinsight/index.yml) och [vilka Hadoop-komponenter och-versioner som är tillgängliga med HDInsight?](../../hdinsight/hdinsight-component-versioning.md). 
- Om du vill fortsätta att använda en Data Factory version 1-länkad HDInsight-tjänst på begäran för att skapa Windows-baserade HDInsight-kluster måste du uttryckligen ange **osType** till **Windows** före den 15 december 2017. Vi rekommenderar att du migrerar till Linux-baserade HDInsight-kluster före den 31 juli 2018. 
- Om du använder en länkad HDInsight-tjänst på begäran för att köra Data Factory version 1 DotNet anpassad aktivitet, uppdaterar du den anpassade JSON-definitionen för DotNet för att istället använda en Azure Batch länkad tjänst. Mer information finns i [använda anpassade aktiviteter i en Data Factory pipeline](./data-factory-use-custom-activities.md). 

> [!Note]
> Om du använder en befintlig, ny länkad HDInsight-enhet i kluster i Data Factory version 1 eller en lokal HDInsight-länkad tjänst i Azure Data Factory, krävs ingen åtgärd. I dessa scenarier tillämpas den senaste versions principen för HDInsight-kluster redan. 
>
> 


## <a name="on-demand-compute-environment"></a>Beräknings miljö på begäran
I en konfiguration på begäran hanterar Data Factory hela beräknings miljön. Data Factory skapar automatiskt beräknings miljön innan ett jobb skickas för bearbetning av data. När jobbet är klart tar Data Factory bort beräknings miljön. 

Du kan skapa en länkad tjänst för en beräknings miljö på begäran. Använd den länkade tjänsten för att konfigurera beräknings miljön och för att kontrol lera detaljerade inställningar för jobb körning, kluster hantering och start åtgärder.

> [!NOTE]
> För närvarande stöds inte konfigurationen på begäran för HDInsight-kluster.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Länkad tjänst för Azure HDInsight på begäran
Data Factory kan automatiskt skapa ett Windows-baserat eller ett Linux-baserat HDInsight-kluster på begäran för bearbetning av data. Klustret skapas i samma region som det lagrings konto som är associerat med klustret. Använd egenskapen JSON- **linkedServiceName** för att skapa klustret.

Observera följande *viktiga* punkter om den länkade HDInsight-tjänsten på begäran:

* HDInsight-klustret på begäran visas inte i din Azure-prenumeration. Tjänsten Data Factory hanterar HDInsight-kluster på begäran för din räkning.
* Loggarna för jobb som körs på ett HDInsight-kluster på begäran kopieras till det lagrings konto som är associerat med HDInsight-klustret. Du kommer åt dessa loggar genom att gå till fönstret **aktivitets körnings information** i Azure Portal. Mer information finns i [övervaka och hantera pipeliner](data-factory-monitor-manage-pipelines.md).
* Du debiteras bara för den tid som HDInsight-klustret är igång.

> [!IMPORTANT]
> Det tar vanligt vis *20 minuter* eller mer att etablera ett HDInsight-kluster på begäran.
>
> 

### <a name="example"></a>Exempel
Följande JSON definierar en Linux-baserad länkad HDInsight-tjänst på begäran. Data Factory skapar automatiskt ett *Linux-baserat HDInsight-* kluster när det bearbetar en data sektor. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> HDInsight-klustret skapar en *standard behållare* i Azure Blob Storage som du anger i egenskapen JSON **linkedServiceName** . Som design tar HDInsight inte bort den här behållaren när klustret tas bort. I en länkad HDInsight-tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt Live-kluster (**TimeToLive**). Klustret tas bort när bearbetningen är färdig. 
>
> Allteftersom fler sektorer bearbetas visas många behållare i blob-lagringen. Om du inte behöver behållarna för fel sökning av jobb kanske du vill ta bort behållarna för att minska lagrings kostnaderna. Namnen på de här containrarna följer ett mönster: `adf<your Data Factory name>-<linked service name>-<date and time>`. Du kan använda ett verktyg som [Microsoft Azure Storage Explorer](https://storageexplorer.com/) för att ta bort behållare i Blob Storage.
>
> 

### <a name="properties"></a>Egenskaper
| Egenskap                     | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Ange egenskapen type till **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Antalet arbets uppgifter och datanoder i klustret. HDInsight-klustret skapas med två huvudnoder, förutom antalet arbets noder som du anger för den här egenskapen. Noderna har storlek Standard_D3, som har 4 kärnor. Ett kluster med fyra arbets noder tar 24 kärnor (4 \* 4 = 16 kärnor för arbetsnoder, plus 2 \* 4 = 8 kärnor för Head-noder). Mer information om Standard_D3 nivån finns i [skapa Linux-baserade Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Ja      |
| timeToLive                   | Den tillåtna inaktiva tiden för HDInsight-klustret på begäran. Anger hur länge HDInsight-klustret på begäran förblir aktivt när en aktivitets körning är färdig, om det inte finns några andra aktiva jobb i klustret.<br /><br />Om en aktivitets körning till exempel tar 6 minuter och **TimeToLive** har angetts till 5 minuter förblir klustret aktiv i 5 minuter efter 6 minuters bearbetning av aktivitets körningen. Om en annan aktivitet körs i fönstret 6 minuter bearbetas den av samma kluster.<br /><br />Att skapa ett HDInsight-kluster på begäran är en dyr åtgärd (det kan ta en stund). Använd den här inställningen vid behov för att förbättra prestandan för en data fabrik genom att återanvända ett HDInsight-kluster på begäran.<br /><br />Om du ställer in värdet för **TimeToLive** på **0**, tas klustret bort så snart aktiviteten körs klart. Men om du anger ett högt värde kan klustret vara inaktivt, vilket leder till höga kostnader. Det är viktigt att ange rätt värde utifrån dina behov.<br /><br />Om **TimeToLive** -värdet har angetts korrekt kan flera pipelines dela instansen av HDInsight-klustret på begäran. | Ja      |
| version                      | Versionen av HDInsight-klustret. För tillåtna HDInsight-versioner, se [HDInsight-versioner som stöds](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Om det här värdet inte anges används den [senaste HDI standard versionen](../../hdinsight/hdinsight-component-versioning.md) . | Inga       |
| linkedServiceName            | Den Azure Storage länkade tjänsten som ska användas av klustret på begäran för att lagra och bearbeta data. HDInsight-klustret skapas i samma region som det här lagrings kontot.<p>För närvarande kan du inte skapa ett HDInsight-kluster på begäran som använder Azure Data Lake Store som lagrings plats. Om du vill lagra resultat data från HDInsight-bearbetning i Data Lake Store använder du kopierings aktivitet för att kopiera data från Blob Storage till Data Lake Store. </p> | Ja      |
| additionalLinkedServiceNames | Anger ytterligare lagrings konton för den länkade HDInsight-tjänsten. Data Factory registrerar lagrings kontona för din räkning. Dessa lagrings konton måste finnas i samma region som HDInsight-klustret. HDInsight-klustret skapas i samma region som det lagrings konto som anges av egenskapen **linkedServiceName** . | Inga       |
| osType                       | Typ av operativ system. Tillåtna värden är **Linux** och **Windows**. Om det här värdet inte anges används **Linux** .  <br /><br />Vi rekommenderar starkt att du använder Linux-baserade HDInsight-kluster. Indragnings datumet för HDInsight i Windows är den 31 juli 2018. | Inga       |
| hcatalogLinkedServiceName    | Namnet på den länkade Azure SQL-tjänsten som pekar på HCatalog-databasen. HDInsight-klustret på begäran skapas med hjälp av SQL-databasen som metaarkiv. | Inga       |

#### <a name="example-linkedservicenames-json"></a>Exempel: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Avancerade egenskaper
För detaljerad konfiguration av HDInsight-klustret på begäran kan du ange följande egenskaper:

| Egenskap               | Beskrivning                              | Krävs |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger kärn konfigurations parametrar (core-site.xml) för HDInsight-klustret som ska skapas. | Inga       |
| hBaseConfiguration     | Anger konfigurations parametrar för HBase (hbase-site.xml) för HDInsight-klustret. | Inga       |
| hdfsConfiguration      | Anger konfigurations parametrar för HDFS (hdfs-site.xml) för HDInsight-klustret. | Inga       |
| hiveConfiguration      | Anger Hive-konfigurations parametrar (hive-site.xml) för HDInsight-klustret. | Inga       |
| mapReduceConfiguration | Anger konfigurations parametrar för MapReduce (mapred-site.xml) för HDInsight-klustret. | Inga       |
| oozieConfiguration     | Anger konfigurations parametrar för Oozie (oozie-site.xml) för HDInsight-klustret. | Inga       |
| stormConfiguration     | Anger Storm-konfigurations parametrar (storm-site.xml) för HDInsight-klustret. | Inga       |
| yarnConfiguration      | Anger konfigurations parametrar för garn (yarn-site.xml) för HDInsight-klustret. | Inga       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exempel: konfiguration av HDInsight-kluster på begäran med avancerade egenskaper

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
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
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Node-storlekar
Om du vill ange storleken på huvud-, data-och ZooKeeper-noderna använder du följande egenskaper: 

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Anger storleken på Head-noden. Standardvärdet är **Standard_D3**. Mer information finns i [Ange Node-storlekar](#specify-node-sizes). | Inga       |
| dataNodeSize      | Anger data nodens storlek. Standardvärdet är **Standard_D3**. | Inga       |
| zookeeperNodeSize | Anger storleken på ZooKeeper-noden. Standardvärdet är **Standard_D3**. | Inga       |

#### <a name="specify-node-sizes"></a>Ange Node-storlekar
För sträng värden som du måste ange för egenskaperna som beskrivs i föregående avsnitt, se [storlekar för virtuella datorer](../../virtual-machines/sizes.md). Värdena måste följa de cmdletar och API: er som refereras till i [virtuella dator storlekar](../../virtual-machines/sizes.md). Storleken på den stora (standard) datanoden har 7 GB minne. Detta kanske inte räcker för ditt scenario. 

Om du vill skapa en D4-storleks huvud-noder och arbetsnoder, anger du **Standard_D4** som värde för egenskaperna **headNodeSize** och **dataNodeSize** : 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för dessa egenskaper kan följande meddelande visas:

  Det gick inte att skapa klustret. Undantag: Unable to complete the cluster create operation. (Det går inte att slutföra åtgärden att skapa ett kluster.) Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Cluster left behind state: 'Error'. (Klustret efterlämnade status: Fel.) Meddelande: ' PreClusterCreationValidationFailure '. 
  
Om det här meddelandet visas kontrollerar du att du använder cmdlet-och API-namnen från tabellen i [storlekar för virtuella datorer](../../virtual-machines/sizes.md).  

> [!NOTE]
> Data Factory stöder för närvarande inte HDInsight-kluster som använder Data Lake Store som primär lagring. Använd Azure Storage som primär lagring för HDInsight-kluster. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Ta en egen beräknings miljö
Du kan registrera en befintlig beräknings miljö som en länkad tjänst i Data Factory. Du hanterar beräknings miljön. Den Data Factory tjänsten använder beräknings miljön för att köra aktiviteter.

Den här typen av konfiguration stöds för följande beräknings miljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning Studio (klassisk)
* Azure Data Lake Analytics
* Azure SQL Database, Azure Synapse Analytics SQL Server

## <a name="azure-hdinsight-linked-service"></a>Länkad Azure HDInsight-tjänst
Du kan skapa en länkad HDInsight-tjänst för att registrera ditt eget HDInsight-kluster med Data Factory.

### <a name="example"></a>Exempel

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ange egenskapen type till **HDInsight**. | Ja      |
| clusterUri        | URI för HDInsight-klustret.        | Ja      |
| användarnamn          | Namnet på det användar konto som ska användas för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| password          | Lösen ordet för användar kontot.   | Ja      |
| linkedServiceName | Namnet på den länkade lagrings tjänsten som refererar till blob-lagringen som används av HDInsight-klustret. <p>För närvarande kan du inte ange en Data Lake Store länkad tjänst för den här egenskapen. Om HDInsight-klustret har åtkomst till Data Lake Store, kan du komma åt data i Data Lake Store från Hive-eller gris-skript. </p> | Ja      |

## <a name="azure-batch-linked-service"></a>Azure Batch länkad tjänst
Du kan skapa en länkad batch-tjänst för att registrera en batch-pool med virtuella datorer (VM) till en data fabrik. Du kan köra Microsoft .NET anpassade aktiviteter med antingen batch eller HDInsight.

Om du är nybörjare på att använda batch-tjänsten:

* Lär dig mer om [Azure Batch grunderna](../../azure-sql/database/sql-database-paas-overview.md).
* Läs mer om cmdleten [New-AzureBatchAccount](/previous-versions/azure/mt125880(v=azure.100)) . Använd denna cmdlet för att skapa ett batch-konto. Du kan också skapa batch-kontot med hjälp av [Azure Portal](../../batch/batch-account-create-portal.md). Detaljerad information om hur du använder cmdleten finns i [använda PowerShell för att hantera ett batch-konto](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account).
* Läs mer om cmdleten [New-AzureBatchPool](/previous-versions/azure/mt125936(v=azure.100)) . Använd denna cmdlet för att skapa en batch-pool.

### <a name="example"></a>Exempel

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

För egenskapen **accountName** lägger du till **. \<region name\>** till namnet på ditt batch-konto. Exempel:

```json
"accountName": "mybatchaccount.eastus"
```

Ett annat alternativ är att tillhandahålla **batchUri** -slutpunkten. Exempel:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ange egenskapen type till **AzureBatch**. | Ja      |
| accountName       | Namnet på batch-kontot.         | Ja      |
| accessKey         | Åtkomst nyckeln för batch-kontot.  | Ja      |
| poolName          | Namnet på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på den länkade lagrings tjänst som är associerad med den här länkade batch-tjänsten. Den här länkade tjänsten används för att mellanlagra filer som krävs för att köra aktiviteten och för att lagra aktivitets körnings loggar. | Ja      |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (klassisk) länkad tjänst
Du kan skapa en Azure Machine Learning Studio (klassisk) länkad tjänst för att registrera en Studio (klassisk) slut punkt för batch-poäng till en data fabrik.

### <a name="example"></a>Exempel

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Egenskaper
| Egenskap   | Beskrivning                              | Krävs |
| ---------- | ---------------------------------------- | -------- |
| Typ       | Ange egenskapen type till **azureml**. | Ja      |
| mlEndpoint | URL för batch-poäng.                   | Ja      |
| apiKey     | Den publicerade arbets ytans modells API.     | Ja      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du kan skapa en Data Lake Analytics länkad tjänst för att länka en Data Lake Analytics Compute-tjänst till en Azure Data Factory. Data Lake Analytics U-SQL-aktiviteten i pipelinen refererar till den här länkade tjänsten. 

I följande tabell beskrivs de allmänna egenskaper som används i JSON-definitionen:

| Egenskap                 | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| typ                 | Ange egenskapen type till **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | Namnet på Data Lake Analyticss kontot.  | Ja                                      |
| dataLakeAnalyticsUri | Data Lake Analytics-URI.           | Inga                                       |
| subscriptionId       | ID för Azure-prenumerationen.                    | Inga<br /><br />(Om det inte anges används Data Factory-prenumerationen.) |
| resourceGroupName    | Namnet på Azure-resurs gruppen.                | Inga<br /><br /> (Om den inte anges används data fabriks resurs gruppen.) |

### <a name="authentication-options"></a>Autentiseringsalternativ
Du kan välja mellan autentisering med hjälp av ett huvud namn för tjänsten eller en användares autentiseringsuppgifter för den länkade tjänsten Data Lake Analytics.

#### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvud namn (rekommenderas)
Om du vill använda tjänstens huvud namns autentisering registrerar du en programentitet i Azure Active Directory (Azure AD). Ge sedan Azure AD åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID
* Program nyckel 
* Klient-ID:t

Använd tjänstens huvud namns autentisering genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Programmets klient-ID.     | Ja      |
| servicePrincipalKey | Programmets nyckel.           | Ja      |
| tenant              | Klient information (domän namn eller klient-ID) där ditt program finns. För att få den här informationen kan du Hovra musen i det övre högra hörnet av Azure Portal. | Ja      |

**Exempel: autentisering av tjänstens huvud namn**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Autentisering av autentiseringsuppgifter för användare
Ange följande egenskaper för autentisering av användar referenser för Data Lake Analytics:

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| auktorisering | I Data Factory redigeraren väljer du knappen **auktorisera** . Ange autentiseringsuppgifterna som tilldelar den automatiskt genererade auktoriserings-URL: en till den här egenskapen. | Ja      |
| sessionId     | OAuth-sessions-ID från OAuth-auktoriseringsbegäran. Varje sessions-ID är unikt och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory redigeraren. | Ja      |

**Exempel: autentisering med användar behörighet**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Förfallo datum för token
Den auktoriseringskod som du skapade genom att välja knappen **auktorisera** upphör att gälla efter ett angivet intervall. 

Du kan se följande fel meddelande när autentiseringstoken upphör att gälla: 

  Åtgärds fel för autentiseringsuppgift: invalid_grant-AADSTS70002: fel vid verifiering av autentiseringsuppgifter. AADSTS70008: den angivna åtkomst tilldelningen har förfallit eller återkallats. Spårnings-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelations-ID: fac30a0c-6BE6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21:09:31Z

Följande tabell visar förfallo datum per användar konto typ: 

| Användar typ                                | Upphör att gälla efter                            |
| :--------------------------------------- | :--------------------------------------- |
| Användar konton som *inte* hanteras av Azure AD (Hotmail, Live osv.) | 12 timmar.                                 |
| Användar *konton som hanteras* av Azure AD | 14 dagar efter den sista sektor körningen. <br /><br />90 dagar, om en sektor som baseras på en OAuth-baserad länkad tjänst körs minst en gång var 14: e dag. |

Om du vill undvika eller lösa det här felet kan du godkänna genom att välja knappen **auktorisera** när token upphör att gälla. Distribuera sedan om den länkade tjänsten. Du kan också skapa värden för **SessionID** -och **Authorization** -egenskaperna program mässigt med hjälp av följande kod:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Mer information om de Data Factory klasser som används i det här kod exemplet finns i:
* [AzureDataLakeStoreLinkedService-klass](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
* [AzureDataLakeAnalyticsLinkedService-klass](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* [AuthorizationSessionGetResponse-klass](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse)

Lägg till en referens till Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll för **WindowsFormsWebAuthenticationDialog** -klassen. 

## <a name="azure-sql-linked-service"></a>Länkad Azure SQL-tjänst
Du kan skapa en länkad SQL-tjänst och använda den med den [lagrade procedur aktiviteten](data-factory-stored-proc-activity.md) för att anropa en lagrad procedur från en Data Factory pipeline. Mer information finns i [Azure SQL-anslutning](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-synapse-analytics-linked-service"></a>Länkad Azure Synapse Analytics-tjänst
Du kan skapa en länkad Azure Synapse Analytics-tjänst och använda den med den [lagrade procedur aktiviteten](data-factory-stored-proc-activity.md) för att anropa en lagrad procedur från en Data Factory pipeline. Mer information finns i [Azure Synapse Analytics-anslutning](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>SQL Server länkad tjänst
Du kan skapa en SQL Server länkad tjänst och använda den med den [lagrade procedur aktiviteten](data-factory-stored-proc-activity.md) för att anropa en lagrad procedur från en Data Factory pipeline. Mer information finns i [SQL Server Connector](data-factory-sqlserver-connector.md#linked-service-properties).