---
title: 'Självstudie: skapa Apache Spark jobb definition i Synapse Studio'
description: Självstudie – Använd Azure Synapse Analytics för att skapa Spark-jobb definitioner och skicka dem till en Apache Spark för Azure Synapse Analytics-poolen.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 10/16/2020
ms.openlocfilehash: 3f1e3fd360197310a89a67d43053649d904aeb18
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677619"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Självstudie: skapa Apache Spark jobb definition i Synapse Studio

Den här självstudien visar hur du använder Azure Synapse Studio för att skapa Apache Spark jobb definitioner och sedan skickar dem till en server lös Apache Spark-pool.

Den här självstudien omfattar följande uppgifter:
> [!div class="checklist"]
>
> - Skapa en Apache Spark jobb definition för PySpark (python)
> - Skapa en Apache Spark jobb definition för Spark (Scala)
> - Skapa en Apache Spark jobb definition för .NET Spark (C#/F #)
> - Skicka in en Apache Spark jobb definition som ett batch-jobb
> - Lägg till en Apache Spark jobb definition i pipeline

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med den här självstudien måste du uppfylla följande krav:

* En Azure Synapse Analytics-arbetsyta. Instruktioner finns i [skapa en Azure Synapse Analytics-arbetsyta](../../machine-learning/how-to-manage-workspace.md).
* En server lös Apache Spark pool.
* Ett ADLS Gen2 lagrings konto. Du måste vara ägare till **Storage BLOB-data** för det ADLS Gen2-filsystem som du vill arbeta med. Om du inte gör det måste du lägga till behörigheten manuellt.
* Om du inte vill använda arbets ytans standard lagring länkar du det obligatoriska ADLS Gen2 lagrings kontot i Synapse Studio. 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Skapa en Apache Spark jobb definition för PySpark (python)

I det här avsnittet skapar du en Apache Spark jobb definition för PySpark (python).

1. Öppna [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Du kan gå till [exempelfiler för att skapa Apache Spark jobb definitioner](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) för att hämta **exempelfilerna för python.zip**, sedan packa upp det komprimerade paketet och extrahera **WORDCOUNT.py** -och **shakespeare.txt** -filerna. 

     ![Exempelfiler](./media/apache-spark-job-definitions/sample-files.png)

3. Välj   ->  **länkade** data  ->  **Azure Data Lake Storage Gen2** och ladda upp **WORDCOUNT.py** och **shakespeare.txt** i ADLS Gen2 fil systemet. 

     ![överför python-fil](./media/apache-spark-job-definitions/upload-python-file.png)

4. Välj **utveckla** hubb, Välj ikonen "+" och välj **Spark jobb definition** för att skapa en ny Spark-jobbiljett. 

     ![Skapa ny definition för python](./media/apache-spark-job-definitions/create-new-definition.png)

5. Välj **PySpark (python)** från List rutan språk i Apache Spark huvud fönstret för jobb definitionen.

     ![Välj python](./media/apache-spark-job-definitions/select-python.png)

6. Fyll i information för Apache Spark jobb definition. 

     |  Egenskap   | Beskrivning   |  
     | ----- | ----- |  
     |Jobb definitions namn| Ange ett namn för din Apache Spark jobb definition. Detta namn kan uppdateras när som helst tills det publiceras. <br> Urvalsundersökningar `job definition sample`|
     |Huvud definitions fil| Huvud filen som används för jobbet. Välj en PY-fil från lagrings platsen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. <br> Urvalsundersökningar `abfss://…/path/to/wordcount.py`|
     |Kommando rads argument| Valfria argument för jobbet. <br> Exempel: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Obs: två argument för exempel jobb definitionen avgränsas med ett blank steg.*|
     |Reference-filer| Ytterligare filer som används som referens i huvud definitions filen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. |
     |Spark-pool| Jobbet skickas till den valda Apache Spark poolen.|
     |Spark-version| Den version av Apache Spark som Apache Sparks poolen körs på.|
     |Körare| Antal körningar som anges i den angivna Apache Spark poolen för jobbet.|
     |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Apache Spark poolen för jobbet.|  
     |Driv rutins storlek| Antal kärnor och minne som ska användas för den driv rutin som anges i den angivna Apache Spark poolen för jobbet.|

     ![Ange värdet för Spark-jobbets definition för python](./media/apache-spark-job-definitions/create-py-definition.png)

7. Välj **publicera** för att spara Apache Spark jobb definitionen.

     ![publicera py-definition](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Skapa en Apache Spark jobb definition för Apache Spark (Scala)

I det här avsnittet skapar du en Apache Spark jobb definition för Apache Spark (Scala).

 1. Öppna [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Du kan gå till [exempelfiler för att skapa Apache Spark jobb definitioner](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) för att hämta **exempelfiler för scala.zip**, sedan packa upp det komprimerade paketet och extrahera filerna **WORDCOUNT. jar** och **shakespeare.txt** . 
 
     ![Scala för exempel filer](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. Välj   ->  **länkade** data  ->  **Azure Data Lake Storage Gen2** och ladda upp **WORDCOUNT. jar** och **shakespeare.txt** i ADLS Gen2 fil systemet.
 
     ![Förbered Scala-struktur](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. Välj **utveckla** hubb, Välj ikonen "+" och välj **Spark jobb definition** för att skapa en ny Spark-jobbiljett. (Exempel bilden är samma som steg 4 i **skapa en Apache Spark jobb definition (python) för PySpark**.)

 5. Välj **Spark (Scala)** i list rutan språk i den Apache Spark jobb definitionens huvud fönster.

     ![Välj Scala](./media/apache-spark-job-definitions/select-scala.png)

 6. Fyll i information för Apache Spark jobb definition. Du kan kopiera exempel informationen.

     |  Egenskap   | Beskrivning   |  
     | ----- | ----- |  
     |Jobb definitions namn| Ange ett namn för din Apache Spark jobb definition. Detta namn kan uppdateras när som helst tills det publiceras. <br> Urvalsundersökningar `scala`|
     |Huvud definitions fil| Huvud filen som används för jobbet. Välj en JAR-fil från lagrings platsen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. <br> Urvalsundersökningar `abfss://…/path/to/wordcount.jar`|
     |Huvud klass namn| Den fullständigt kvalificerade identifieraren eller den huvudsakliga klass som finns i huvud definitions filen. <br> Urvalsundersökningar `WordCount`|
     |Kommando rads argument| Valfria argument för jobbet. <br> Exempel: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Obs: två argument för exempel jobb definitionen avgränsas med ett blank steg.* |
     |Reference-filer| Ytterligare filer som används som referens i huvud definitions filen. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
     |Spark-pool| Jobbet skickas till den valda Apache Spark poolen.|
     |Spark-version| Den version av Apache Spark som Apache Sparks poolen körs på.|
     |Körare| Antal körningar som anges i den angivna Apache Spark poolen för jobbet.|  
     |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Apache Spark poolen för jobbet.|
     |Driv rutins storlek| Antal kärnor och minne som ska användas för den driv rutin som anges i den angivna Apache Spark poolen för jobbet.|

     ![Ange värdet för Spark-jobbets definition för Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. Välj **publicera** för att spara Apache Spark jobb definitionen.

      ![publicera Scala-definition](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Skapa en Apache Spark jobb definition för .NET Spark (C#/F #)

I det här avsnittet skapar du en Apache Spark jobb definition för .NET Spark (C#/F #).
 1. Öppna [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Du kan gå till [exempelfiler för att skapa Apache Spark jobb definitioner](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) för att hämta **exempelfilerna för dotnet.zip**, sedan packa upp det komprimerade paketet och extrahera **wordcount.zip** -och **shakespeare.txt** -filerna. 

     ![exempel på dotNet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. Välj   ->  **länkade** data  ->  **Azure Data Lake Storage Gen2** och ladda upp **wordcount.zip** och **shakespeare.txt** i ADLS Gen2 fil systemet.
 
     ![Förbered dotNet-struktur](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. Välj **utveckla** hubb, Välj ikonen "+" och välj **Spark jobb definition** för att skapa en ny Spark-jobbiljett. (Exempel bilden är samma som steg 4 i **skapa en Apache Spark jobb definition (python) för PySpark**.)

 5. Välj **.net Spark (C#/f #)** från List rutan språk i fönstret för Apache Spark jobb definitions huvud.

     ![Välj dotNet](./media/apache-spark-job-definitions/select-dotnet.png)

 6. Fyll i information för Apache Spark jobb definition. Du kan kopiera exempel informationen.
    
     |  Egenskap   | Beskrivning   |  
     | ----- | ----- |  
     |Jobb definitions namn| Ange ett namn för din Apache Spark jobb definition. Detta namn kan uppdateras när som helst tills det publiceras. <br> Urvalsundersökningar `dotnet`|
     |Huvud definitions fil| Huvud filen som används för jobbet. Välj en ZIP-fil som innehåller ditt .NET för Apache Spark-program (det vill säga den viktigaste körbara filen, DLL-filer som innehåller användardefinierade funktioner och andra filer som krävs) från din lagrings plats. Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto. <br> Urvalsundersökningar `abfss://…/path/to/wordcount.zip`|
     |Huvud program fil| Den körbara filen i huvud definitionens ZIP-fil. <br> Urvalsundersökningar `WordCount`|
     |Kommando rads argument| Valfria argument för jobbet. <br> Exempel: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Obs: två argument för exempel jobb definitionen avgränsas med ett blank steg.* |
     |Reference-filer| Ytterligare filer som krävs av arbetsnoderna för att köra .NET för Apache Spark program som inte ingår i huvud definitionens ZIP-fil (det vill säga beroende jar v7, ytterligare användardefinierade funktions-DLL-filer och andra konfigurationsfiler). Du kan välja **Ladda upp fil** för att överföra filen till ett lagrings konto.|
     |Spark-pool| Jobbet skickas till den valda Apache Spark poolen.|
     |Spark-version| Den version av Apache Spark som Apache Sparks poolen körs på.|
     |Körare| Antal körningar som anges i den angivna Apache Spark poolen för jobbet.|  
     |Utförar-storlek| Antal kärnor och minne som ska användas för körningar som anges i den angivna Apache Spark poolen för jobbet.|
     |Driv rutins storlek| Antal kärnor och minne som ska användas för den driv rutin som anges i den angivna Apache Spark poolen för jobbet.|

     ![Ange värdet för Spark-jobbets definition för dotNET](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. Välj **publicera** för att spara Apache Spark jobb definitionen.

      ![publicera dotNet-definition](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Skicka in en Apache Spark jobb definition som ett batch-jobb

När du har skapat en Apache Spark jobb definition kan du skicka den till en Apache Spark-pool. Se till att du är **ägare till Storage BLOB-data** för det ADLS Gen2-filsystem som du vill arbeta med. Om du inte gör det måste du lägga till behörigheten manuellt.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Scenario 1: skicka Apache Spark jobb definition
 1. Öppna fönstret Apache Spark jobb definition genom att markera det.

      ![Öppna Spark jobb definition för att skicka in ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Klicka på knappen **Skicka** för att skicka projektet till den valda Apache Spark poolen. Du kan välja **Spark Monitoring URL** -fliken för att se LogQuery i Apache Spark-programmet.

    ![Skicka Spark-jobb genom att klicka på Skicka knapp](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Dialog rutan för Spark-sändning](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Scenario 2: Visa Apache Spark jobb som kör förlopp

 1. Välj **övervaka** och välj alternativet **Apache Spark program** . Du kan hitta det skickade Apache Spark programmet.

     ![Visa Spark-program](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Välj sedan ett Apache Spark program visas fönstret **SparkJobDefinition** Job. Du kan visa jobb körnings förloppet härifrån.
     
     ![Visa Spark Application LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: kontrol lera utdatafilen

 1. Välj   ->  **länkade** data  ->  **Azure Data Lake Storage Gen2** (hozhaobdbj), öppna mappen **result** som du skapade tidigare. du kan gå till mappen result och kontrol lera om utdata har genererats.

     ![Visa utdatafil](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Lägg till en Apache Spark jobb definition i pipeline

I det här avsnittet lägger du till en Apache Spark jobb definition i pipeline.

 1. Öppna en befintlig Apache Spark jobb definition.

 2. Välj ikonen längst upp till höger i Apache Spark jobb definition, Välj **befintlig pipeline** eller **ny pipeline**. Mer information hittar du på pipeline-sidan.

     ![Lägg till i pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Lägg till i pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Nästa steg

Nu kan du använda Azure Synapse Studio för att skapa Power BI data uppsättningar och hantera Power BI data. Gå vidare till den [länkade en Power BI arbets yta till en artikel i Synapse-arbetsytan](../quickstart-power-bi.md) om du vill veta mer. 

