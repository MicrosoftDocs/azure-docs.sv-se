---
title: Azure Data Factory-exempel
description: Innehåller information om exempel som medföljer tjänsten Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 82c871e8914d70dde7f2586b7dbf6556ebd2621f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387044"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory-exempel
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factorys tjänsten, se [PowerShell-exempel i Data Factory](../samples-powershell.md) och [kod exempel i galleriet med Azure kod exempel](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Exempel på GitHub
[GitHub Azure-DataFactory-lagringsplatsen](https://github.com/azure/azure-datafactory) innehåller flera exempel som hjälper dig att snabbt öka rampen med Azure Data Factory-tjänsten (eller) ändra skripten och använda den i ett eget program. Mappen Samples\JSON innehåller JSON-kodfragment för vanliga scenarier.

| Exempel | Beskrivning |
|:--- |:--- |
| [Genom gång av ADF](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Det här exemplet innehåller en slut punkt till slut punkts genom gång för bearbetning av loggfiler med Azure Data Factory för att omvandla data från loggfiler till insikter. <br/><br/>I den här genom gången samlar Data Factory pipelinen ut exempel loggar, bearbetar och berikar data från loggar med referens data och transformerar data för att utvärdera effektiviteten hos en marknadsförings kampanj som nyligen har startats. |
| [JSON-exempel](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Det här exemplet innehåller JSON-exempel för vanliga scenarier. |
| [Exempel på http-datahämtare](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |I det här exemplet demonstreras hämtning av data från en HTTP-slutpunkt till Azure Blob Storage med hjälp av anpassad .NET-aktivitet. |
| [Exempel på punkt net-aktivitet i Cross AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Med det här exemplet kan du skapa en anpassad .NET-aktivitet som inte är begränsad till sammansättnings versioner som används av ADF-Start programmet (till exempel WindowsAzure. Storage v 4.3.0, Newtonsoft.Jspå v 6.0. x osv.). |
| [Kör R-skriptet](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Det här exemplet innehåller Data Factory anpassad aktivitet som kan användas för att anropa RScript.exe. Det här exemplet fungerar bara med ditt eget HDInsight-kluster (inte på begäran) som redan har R installerat på det. |
| [Anropa Spark-jobb på HDInsight Hadoop-kluster](../tutorial-transform-data-spark-portal.md) |Det här exemplet visar hur du använder MapReduce-aktivitet för att anropa ett Spark-program. Spark-programmet kopierar bara data från en Azure Blob-behållare till en annan. |
| [Twitter-analys med Azure Machine Learning Studio (klassisk) batch-bedömnings aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Det här exemplet visar hur du använder AzureMLBatchScoringActivity för att anropa en Azure Machine Learning modell som utför Twitter-sentiment analys, poängsättning, förutsägelser osv. |
| [Twitter-analys med anpassad aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Det här exemplet visar hur du använder en anpassad .NET-aktivitet för att anropa en Azure Machine Learning modell som utför Twitter-sentiment analys, poängsättning, förutsägelser osv. |
| [Parameterstyrda pipelines för Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |Exemplet innehåller en kod från slut punkt till slut punkt för att distribuera N pipelines för att kunna ta del av och träna om var och en med en annan region parameter där listan över regioner kommer från en parameters.txt-fil som ingår i det här exemplet. |
| [Referens data uppdatering för Azure Stream Analytics jobb](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Det här exemplet visar hur du använder Azure Data Factory och Azure Stream Analytics tillsammans för att köra frågorna med referens data och konfigurera uppdatering av referens data enligt ett schema. |
| [Hybrid pipelines med lokal Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Exemplet använder ett lokalt Hadoop-kluster som ett beräknings mål för jobb som körs i Data Factory precis som du skulle lägga till andra beräknings mål som ett HDInsight-baserat Hadoop-kluster i molnet. |
| [JSON Conversion Tool](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Med det här verktyget kan du konvertera JSON-objekt från tidigare versioner än 2015-07-01 – för hands version till senaste eller 2015-07-01 – för hands version (standard). |
| [U-SQL exempel indatafil](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Den här filen är en exempel fil som används av en U-SQL-aktivitet. |
| [Ta bort BLOB-fil](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Det här exemplet visar en C#-fil som kan användas som en del av den anpassade .net-aktiviteten i ADF för att ta bort filer från käll Azure Blob-platsen när filerna har kopierats.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Du kan hitta följande Azure Resource Manager mallar för Data Factory på GitHub.

| Mall | Beskrivning |
| --- | --- |
| [Kopiera från Azure Blob Storage till Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Genom att distribuera den här mallen skapas en Azure-datafabrik med en pipeline som kopierar data från Azure Blob Storage till Azure SQL Database |
| [Kopiera från Salesforce till Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Genom att distribuera den här mallen skapas en Azure-datafabrik med en pipeline som kopierar data från det angivna Salesforce-kontot till Azure Blob Storage. |
| [Transformera data genom att köra Hive-skript på ett Azure HDInsight-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Genom att distribuera den här mallen skapas en Azure-datafabrik med en pipeline som transformerar data genom att köra Hive-skriptet på ett Azure HDInsight Hadoop-kluster. |

## <a name="samples-in-azure-portal"></a>Exempel i Azure Portal
Du kan använda panelen **exempel pipelines** på Start sidan för din data fabrik för att distribuera exempel pipeliner och deras associerade entiteter (data uppsättningar och länkade tjänster) i till din data fabrik.

1. Skapa en data fabrik eller öppna en befintlig data fabrik. Information om hur du skapar en data fabrik finns i [Kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
2. På **Data Factory** -bladet för data fabriken klickar du på panelen **exempel pipelines** .

    ![Exempel panel för exempel](./media/data-factory-samples/SamplePipelinesTile.png)
3. I bladet **exempel på pipelines** klickar du på det **exempel** som du vill distribuera.

    ![Exempel på pipeline-blad](./media/data-factory-samples/SampleTile.png)
4. Ange konfigurations inställningar för exemplet. Till exempel Azure Storage-kontots namn och konto nyckel, logiskt SQL Server-namn, databas, användar-ID och lösen ord, osv.

    ![Exempel blad](./media/data-factory-samples/SampleBlade.png)
5. När du är klar med att ange konfigurations inställningarna klickar du på **skapa** för att skapa/distribuera exempel pipeliner och länkade tjänster/tabeller som används av pipelinen.
6. Du ser status för distributionen i exempel panelen som du klickade på tidigare på bladet **exempel pipelines** .

    ![Status för distribution](./media/data-factory-samples/DeploymentStatus.png)
7. När du ser meddelandet **distributionen har slutförts** på panelen för exemplet stänger du bladet **exempel pipelines** .  
8. På bladet **data fabrik** ser du att länkade tjänster, data uppsättningar och pipeliner läggs till i din data fabrik.  

    ![Bladet Datafabrik](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Exempel i Visual Studio
### <a name="prerequisites"></a>Förutsättningar
Du måste ha följande installerat på datorn:

* Visual Studio 2013 eller Visual Studio 2015
* Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
* Hämta det senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Om du använder Visual Studio 2013 kan du också uppdatera plugin-programmet genom att göra följande: på menyn klickar du på **verktyg**  ->  **tillägg och uppdateringar**  ->  **online**  ->  **Visual Studio-galleriet**  ->  **Microsoft Azure Data Factory verktyg för Visual Studio**  ->  **Update**.

### <a name="use-data-factory-templates"></a>Använda Data Factory mallar
1. Klicka på **Arkiv** på menyn, peka på **nytt** och klicka på **projekt**.
2. Gör följande steg i dialogrutan **Nytt projekt**:

   1. Välj **DataFactory** under **mallar**.
   2. Välj **Data Factory mallar** i den högra rutan.
   3. Ange ett **namn** för projektet.
   4. Välj en **plats** för projektet.
   5. Klicka på **OK**.

      ![Dialogrutan Nytt projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. I dialog rutan **Data Factory mallar** väljer du exempel mal len från avsnittet **använda-Case-mallar** och klickar på **Nästa**. Följande steg beskriver hur du använder mallen för **kund profilering** . Stegen är liknande för de andra exemplen.

    ![Dialog rutan Data Factory mallar](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. I dialog rutan **Data Factory konfiguration** klickar du på **Nästa** på sidan för **Data Factory grundläggande** information.
5. Utför följande steg på sidan **Konfigurera data fabrik** :
   1. Välj **Skapa ny Data Factory**. Du kan också välja **Använd befintlig data fabrik**.
   2. Ange ett **namn** för data fabriken.
   3. Välj den **Azure-prenumeration** där du vill att data fabriken ska skapas.
   4. Välj **resurs grupp** för data fabriken.
   5. Välj **västra USA**, **östra USA** eller **norra Europa** för **regionen**.
   6. Klicka på **Nästa**.
6. På sidan **Konfigurera data lager** anger du en befintlig **databas i Azure SQL Database** och **Azure Storage-konto** (eller) skapa databas/lagring och klickar på Nästa.
7. På sidan **Konfigurera beräkning** väljer du standardvärden och klickar på **Nästa**.
8. Granska alla inställningar på sidan **Sammanfattning** och klicka på **Nästa**.
9. På sidan **distributions status** väntar du tills distributionen är klar och klickar på **Slutför**.
10. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**.
11. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
12. Du bör se följande dialogruta:

    ![Dialogrutan Publicera](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. På sidan **Konfigurera datafabrik** går du igenom följande steg:

    1. Bekräfta att alternativet **Använd befintlig data fabrik** .
    2. Välj den **data fabrik** du valde när du använde mallen.
    3. Klicka på **Nästa** för att växla till sidan **Publicera objekt**. (Tryck på **TAB** för att flytta ut från namnfältet om knappen **Nästa** är inaktiverad.)
14. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.     
15. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
16. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar.

Se [skapa din första data fabrik (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) om du vill ha mer information om hur du använder Visual Studio för att redigera data Factory entiteter och publicera dem till Azure.