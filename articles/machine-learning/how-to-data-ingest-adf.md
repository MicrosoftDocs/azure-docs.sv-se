---
title: Datainmatning med Azure Data Factory
titleSuffix: Azure Machine Learning
description: Lär dig tillgängliga alternativ för att skapa en pipeline för data inmatning med Azure Data Factory och fördelarna med var och en.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98796150"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Datainmatning med Azure Data Factory

I den här artikeln får du lära dig mer om tillgängliga alternativ för att skapa en pipeline för data inmatning med [Azure Data Factory](../data-factory/introduction.md). Den här Azure Data Factory pipelinen används för att mata in data som ska användas med [Azure Machine Learning](overview-what-is-azure-ml.md). Med Data Factory kan du enkelt extrahera, transformera och läsa in data (ETL). När data har omvandlats och lästs in i lagringen kan du använda dem för att träna dina maskin inlärnings modeller i Azure Machine Learning.

Enkel datatransformering kan hanteras med interna Data Factory aktiviteter och instrument som [data flöde](../data-factory/control-flow-execute-data-flow-activity.md). När det kommer till mer komplicerade scenarier kan data bearbetas med en anpassad kod. Till exempel python eller R-kod.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Jämför Azure Data Factory pipelines för data inmatning 
Det finns flera vanliga metoder för att använda Data Factory för att transformera data under inmatningen. Varje teknik har fördelar och nack delar som hjälper dig att avgöra om den passar bra för ett särskilt användnings fall:

| Teknik | Fördelar | Nackdelar |
| ----- | ----- | ----- |
| Data Factory + Azure Functions | <li> Låg latens, Server lös beräkning<li>Tillstånds känsliga funktioner<li>Återanvändbara funktioner | Endast lämpat för kortvarig bearbetning |
| Data Factory + anpassad komponent | <li>Storskalig parallell data behandling<li>Lämpligt för stora algoritmer | <li>Kräver inbäddnings kod i en körbar fil<li>Komplexitet vid hantering av beroenden och i/o |
| Data Factory + Azure Databricks Notebook |<li> Apache Spark<li>Inbyggd python-miljö |<li>Kan vara dyrt<li>Att skapa kluster från början tar tid och lägger till latens

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory med Azure Functions

Med Azure Functions kan du köra små delar av kod (funktioner) utan att oroa dig för program infrastrukturen. I det här alternativet bearbetas data med anpassad python-kod inkapslad i en Azure-funktion. 

Funktionen anropas med [Azure Data Factory Azure Function-aktivitet](../data-factory/control-flow-azure-function-activity.md). Den här metoden är ett lämpligt alternativ för lätta data transformationer. 

![Diagrammet visar en Azure Data Factory pipeline, med Azure Function och köra ML pipelines och en Azure Machine Learning pipeline, med träna modell, och hur de interagerar med rå data och för beredda data.](media/how-to-data-ingest-adf/adf-function.png)



* Fördelar:
    * Data bearbetas med en server lös beräkning med en relativt låg latens
    * Data Factory pipeline kan anropa en [varaktig Azure-funktion](../azure-functions/durable/durable-functions-overview.md) som kan implementera ett avancerat data omvandlings flöde 
    * Informationen om Datatransformeringen har sammanställts av Azure-funktionen som kan återanvändas och anropas från andra platser
* Nack delarna
    * Azure Functions måste skapas före användning med ADF
    * Azure Functions är bara lämpat för kort körning av data behandling

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory med anpassad komponent aktivitet

I det här alternativet bearbetas data med anpassad python-kod som är figursatt till en körbar fil. Den anropas med en [ Azure Data Factory anpassad komponent aktivitet](../data-factory/transform-data-using-dotnet-custom-activity.md). Den här metoden passar bättre för stora data än den tidigare tekniken.

![Diagrammet visar en Azure Data Factory pipeline, med en anpassad komponent och kör M L pipeline, och en Azure Machine Learning pipeline, med träna modell, och hur de interagerar med rå data och för beredda data.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Fördelar:
    * Data bearbetas på [Azure Batch](../batch/batch-technical-overview.md) pool, vilket ger storskalig parallell och högpresterande data behandling
    * Kan användas för att köra tungt frekventa algoritmer och bearbeta stora mängder data
* Nackdelar:
    * Azure Batch pool måste skapas före användning med Data Factory
    * Över-teknik som är relaterad till att figursätta python-kod till en körbar fil. Komplexitet vid hantering av beroenden och indata/utdata-parametrar

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory med Azure Databricks python Notebook

[Azure Databricks](https://azure.microsoft.com/services/databricks/) är en Apache Spark-baserad analys plattform i Microsoft-molnet.

I den här tekniken utförs Datatransformeringen av en [python-anteckningsbok](../data-factory/transform-data-using-databricks-notebook.md), som körs på ett Azure Databricks kluster. Detta är förmodligen den vanligaste metoden som utnyttjar en Azure Databrickss tjänsts fulla kapacitet. Den är utformad för distribuerad data bearbetning i stor skala.

![Diagrammet visar en Azure Data Factory pipeline, med Azure Databricks python och kör M L pipeline, och en Azure Machine Learning pipeline, med träna modell, och hur de interagerar med rå data och för beredda data.](media/how-to-data-ingest-adf/adf-databricks.png)

* Fördelar:
    * Data omvandlas till den mest kraftfulla Azure-tjänsten för data bearbetning, som säkerhets kopie ras av Apache Sparks miljö
    * Inbyggt stöd för python tillsammans med ramverk och bibliotek för data vetenskap, inklusive TensorFlow, PyTorch och scikit – lära
    * Det finns inget behov av att figursätta python-koden till Functions eller körbara moduler. Koden fungerar som den är.
* Nackdelar:
    * Azure Databricks-infrastrukturen måste skapas innan den kan användas med Data Factory
    * Kan vara kostsam beroende på Azure Databricks konfiguration
    * Det tar en stund att snurra beräknings kluster från "kall" läge med hög latens för lösningen 
    

## <a name="consume-data-in-azure-machine-learning"></a>Använda data i Azure Machine Learning 

Data Factory pipeline sparar de data som för bereddes i moln lagringen (till exempel Azure-Blob eller Azure-Datalake). <br>
Använda dina för beredda data i Azure Machine Learning av, 

* Anropar en Azure Machine Learning pipeline från Data Factory pipelinen.<br>**OR**
* Skapa en [Azure Machine Learning data lager](how-to-access-data.md#create-and-register-datastores) -och [Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md) för användning vid ett senare tillfälle.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Anropa Azure Machine Learning pipeline från Data Factory

Den här metoden rekommenderas för [MLOps-arbetsflöden (Machine Learning Operations)](concept-model-management-and-deployment.md#what-is-mlops). Om du inte vill skapa en Azure Machine Learning pipeline, se [läsa data direkt från lagring](#read-data-directly-from-storage).

Varje tillfälle som Data Factory pipelinen körs 

1. Data sparas på en annan plats i lagrings platsen. 
1. För att skicka platsen till Azure Machine Learning, anropar Data Factory pipelinen en [Azure Machine Learning pipeline](concept-ml-pipelines.md). När du anropar en ML-pipeline skickas data platsen och körnings-ID: t som parametrar. 
1. Den ML pipelinen kan sedan skapa ett Azure Machine Learning data lager och data uppsättning med data platsen. Läs mer i [köra Azure Machine Learning pipelines i Data Factory](../data-factory/transform-data-machine-learning-service.md).

![Diagrammet visar en Azure Data Factory pipeline och en Azure Machine Learning pipeline och hur de interagerar med rå data och för beredda data. Data Factory pipeline-data till den för beredda data databasen, som feedar ett data lager, som feed-datauppsättningar i arbets ytan Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Data uppsättningar [stöder versions hantering](./how-to-version-track-datasets.md), så den ml-pipelinen kan registrera en ny version av data uppsättningen som pekar på de senaste data från ADF-pipeline.

När data är tillgängliga via ett data lager eller en data uppsättning kan du använda det för att träna en ML-modell. Inlärnings processen kan vara en del av samma ML-pipeline som anropas från ADF. Eller så kan det vara en separat process som experimentering i en Jupyter Notebook.

Eftersom data uppsättningar stöder versions hantering och varje körning från pipelinen skapar en ny version, är det enkelt att förstå vilken version av data som användes för att träna en modell.

### <a name="read-data-directly-from-storage"></a>Läs data direkt från lagring

Om du inte vill skapa en ML-pipeline kan du komma åt data direkt från lagrings kontot där dina för beredda data sparas med en Azure Machine Learning data lager och data uppsättning. 

Följande python-kod visar hur du skapar ett data lager som ansluter till Azure DataLake generation 2-lagring. [Läs mer om data lager och var du hittar tjänst huvud behörigheter](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Skapa sedan en data uppsättning för att referera till de filer som du vill använda i din dator inlärnings uppgift. 

Följande kod skapar en TabularDataset från en CSV-fil `prepared-data.csv` . Läs mer om [data uppsättnings typer och godkända fil format](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

Härifrån använder `prepared_dataset` du för att referera till dina för beredda data, som i dina utbildnings skript. Lär dig hur du [tränar modeller med data uppsättningar i Azure Machine Learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Nästa steg

* [Kör en Databricks Notebook i Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Få åtkomst till data i Azure Storage-tjänster](./how-to-access-data.md#create-and-register-datastores)
* [Träna modeller med data uppsättningar i Azure Machine Learning](./how-to-train-with-datasets.md).
* [DevOps i en pipeline för datainmatning](./how-to-cicd-data-ingestion.md)