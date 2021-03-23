---
title: Vanliga frågor och svar om Azure Data Factory
description: Vanliga frågor och svar om Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 450a12ae5ae1e13c9cc9b5599a41b6c29d740a9f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785333"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Vanliga frågor och svar om Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [vanliga frågor och svar – Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?
Data Factory är en molnbaserad data integrerings tjänst som **automatiserar flytt och transformering av data**. Precis som en fabrik som kör utrustning för att ta råmaterial och omvandla dem till färdiga varor, Data Factory dirigerar befintliga tjänster som samlar in rå data och omvandlar dem till information som är klar att använda.

Med Data Factory kan du skapa data drivna arbets flöden för att flytta data mellan både lokala och molnbaserade data lager samt bearbeta/transformera data med hjälp av beräknings tjänster som Azure HDInsight och Azure Data Lake Analytics. När du har skapat en pipeline som utför den åtgärd som du behöver kan du schemalägga den så att den körs regelbundet (varje timme, varje dag, varje vecka osv.).   

Mer information finns i [översikt & viktiga begrepp](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Var hittar jag pris information för Azure Data Factory?
Se pris information för [Data Factory][adf-pricing-details] för Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hur gör jag för att kom igång med Azure Data Factory?
* En översikt över Azure Data Factory finns i [Introduktion till Azure Data Factory](data-factory-introduction.md).
* En själv studie kurs om hur du **kopierar/flyttar data** med hjälp av kopierings aktivitet finns i [Kopiera data från Azure Blob Storage till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* En själv studie kurs om hur du **omvandlar data** med HDInsight Hive-aktivitet. Se [bearbeta data genom att köra Hive-skript på Hadoop-kluster](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Vad är Data Factory regions tillgänglighet?
Data Factory finns i **västra USA** och **Nord Europa**. De beräknings-och lagrings tjänster som används av data fabriker kan finnas i andra regioner. Se [regioner som stöds](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Vilka är gränserna för antalet data fabriker/pipelines/Activities/data uppsättningar?
Se avsnittet **Azure Data Factory gränser** i artikeln om [begränsningar, kvoter och begränsningar för Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Vad är upplevelsen för redigering/utvecklare med Azure Data Factory tjänsten?
Du kan skapa/skapa data fabriker med något av följande verktyg/SDK: er:

* **Visual Studio** Du kan använda Visual Studio för att skapa en Azure-datafabrik. Mer information finns i [bygga din första data pipeline med Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) .
* **Azure PowerShell** Se [skapa och övervaka Azure Data Factory att använda Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) för självstudier/genom gång för att skapa en data fabrik med hjälp av PowerShell. Mer information om Data Factory-cmdletar finns i referens innehåll för [Data Factory-cmdleten][adf-powershell-reference] i MSDN Library.
* **.NET-klass bibliotek** Du kan skapa data fabriker program mässigt genom att använda Data Factory .NET SDK. Se [skapa, övervaka och hantera data fabriker med hjälp av .NET SDK](data-factory-create-data-factories-programmatically.md) för en genom gång av hur du skapar en data fabrik med hjälp av .NET SDK. Se [Data Factory klass biblioteks referens][msdn-class-library-reference] för en omfattande dokumentation av Data Factory .NET SDK.
* **REST API** Du kan också använda REST API som exponeras av tjänsten Azure Data Factory för att skapa och distribuera data fabriker. Se [Data Factory REST API Reference][msdn-rest-api-reference] för en omfattande dokumentation av Data Factory REST API.
* **Azure Resource Manager mall** Se [självstudie: Bygg din första Azure-datafabrik med hjälp av Azure Resource Manager mall](data-factory-build-your-first-pipeline-using-arm.md) för information.

### <a name="can-i-rename-a-data-factory"></a>Kan jag byta namn på en data fabrik?
Nej. Precis som andra Azure-resurser kan du inte ändra namnet på en Azure Data Factory.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Kan jag flytta en data fabrik från en Azure-prenumeration till en annan?
Ja. Använd knappen **Flytta** på Data Factory-bladet, som du ser i följande diagram:

![Flytta data fabrik](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Vilka beräknings miljöer stöds av Data Factory?
Följande tabell innehåller en lista över beräknings miljöer som stöds av Data Factory och de aktiviteter som kan köras på dem.

| Compute-miljö | activities |
| --- | --- |
| [HDInsight-kluster på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) eller [ditt eget HDInsight-kluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [gris](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klassisk)](data-factory-compute-linked-services.md#azure-machine-learning-studio-classic-linked-service) |[Studio (klassiska) aktiviteter: batch-körning och uppdaterings resurs](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure Synapse Analytics](data-factory-compute-linked-services.md#azure-synapse-analytics-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Lagrad procedur](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hur jämför Azure Data Factory med SQL Server Integration Services (SSIS)? 
Se [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) -presentationen från någon av våra MVP: er (Most Valuable profession ALS): Reza rad. Några av de senaste ändringarna i Data Factory kanske inte visas i bild däcket. Vi lägger kontinuerligt till fler funktioner till Azure Data Factory. Vi lägger kontinuerligt till fler funktioner till Azure Data Factory. Vi kommer att införliva dessa uppdateringar i jämförelsen av data integrerings tekniker från Microsoft senare i år.   

## <a name="activities---faq"></a>Aktiviteter – vanliga frågor och svar
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Vilka är de olika typerna av aktiviteter som du kan använda i en Data Factory pipeline?
* [Data förflyttnings aktiviteter](data-factory-data-movement-activities.md) för att flytta data.
* [Data omvandlings aktiviteter](data-factory-data-transformation-activities.md) för att bearbeta/transformera data.

### <a name="when-does-an-activity-run"></a>När körs en aktivitet?
Inställningen **tillgänglighets** konfiguration i tabellen utdata avgör när aktiviteten körs. Om indata-datauppsättningar anges, kontrollerar aktiviteten om alla indata-databeroenden är uppfyllda (vilket är **klart** ) innan det börjar köras.

## <a name="copy-activity---faq"></a>Kopierings aktivitet – vanliga frågor och svar
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Är det bättre att ha en pipeline med flera aktiviteter eller en separat pipeline för varje aktivitet?
Pipelines ska paketera relaterade aktiviteter. Om data uppsättningarna som ansluter dem inte används av någon annan aktivitet utanför pipelinen kan du behålla aktiviteterna i en pipeline. På så sätt behöver du inte kedje aktiva perioder för pipeline så att de överensstämmer med varandra. Dessutom bevaras data integriteten i tabellerna som är interna för pipelinen när pipelinen uppdateras. Pipeline-uppdateringen stoppar i princip alla aktiviteter i pipelinen, tar bort dem och skapar dem igen. Från redigerings perspektivet kan det också vara lättare att se data flödet i de relaterade aktiviteterna i en JSON-fil för pipelinen.

### <a name="what-are-the-supported-data-stores"></a>Vilka är de data lager som stöds?
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

### <a name="what-are-the-supported-file-formats"></a>Vilka fil format stöds?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Var utförs kopierings åtgärden?
Mer information finns i avsnittet om [globalt tillgänglig data förflyttning](data-factory-data-movement-activities.md#global) . Kort, när ett lokalt data lager är involverat, utförs kopierings åtgärden av Data Management Gateway i din lokala miljö. När data flytten är mellan två moln lager, utförs kopierings åtgärden i den region som ligger närmast platsen för mottagaren i samma geografi.

## <a name="hdinsight-activity---faq"></a>HDInsight-aktivitet – vanliga frågor och svar
### <a name="what-regions-are-supported-by-hdinsight"></a>Vilka regioner stöds av HDInsight?
Se avsnittet geografisk tillgänglighet i följande artikel: eller [pris information för HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Vilken region används av ett HDInsight-kluster på begäran?
HDInsight-klustret på begäran skapas i samma region där det lagrings utrymme som du har angett ska användas med klustret finns.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Hur kopplar du ytterligare lagrings konton till ditt HDInsight-kluster?
Om du använder ett eget HDInsight-kluster (BYOC-ta med ditt eget kluster) kan du läsa följande avsnitt:

* [Använda ett HDInsight-kluster med alternativa lagrings konton och Metastores][hdinsight-alternate-storage]
* [Använd ytterligare lagrings konton med HDInsight Hive][hdinsight-alternate-storage-2]

Om du använder ett kluster på begäran som skapats av tjänsten Data Factory, anger du ytterligare lagrings konton för den länkade HDInsight-tjänsten så att tjänsten Data Factory kan registrera dem åt dig. Använd egenskapen **additionalLinkedServiceNames** i JSON-definitionen för den länkade tjänsten på begäran för att ange alternativa lagrings konton som visas i följande JSON-kodfragment:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
I exemplet ovan representerar otherLinkedServiceName1 och otherLinkedServiceName2 länkade tjänster vars definitioner innehåller autentiseringsuppgifter som HDInsight-klustret behöver för att komma åt alternativa lagrings konton.

## <a name="slices---faq"></a>Segment – vanliga frågor och svar
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Varför är mina indataportar inte i klart läge?
Ett vanligt fel är att inte ställa in **extern** egenskap på **True** för indata-datauppsättningen när indata är externa i data fabriken (som inte produceras av data fabriken).

I följande exempel behöver du bara ange **external** till true för **DataSet1**.  

**DataFactory1** Pipeline 1: DataSet1-> Activity1-> DataSet2-> Activity2-> dataset3 pipeline 2: dataset3-> Activity3-> dataset4

Om du har en annan data fabrik med en pipeline som tar dataset4 (producerad av pipelinen 2 i Data Factory 1) markerar du dataset4 som en extern data uppsättning eftersom data uppsättningen produceras av en annan data fabrik (DataFactory1, inte DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4->Activity4->dataset5

Om den externa egenskapen har angetts korrekt kontrollerar du om indata finns på den plats som anges i definitionen för data uppsättningen för indata.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hur kör man en sektor vid en annan tidpunkt än midnatt när sektorn produceras dagligen?
Använd egenskapen **offset** för att ange när du vill att sektorn ska produceras. Mer information om den här egenskapen finns i avsnittet om [data uppsättnings tillgänglighet](data-factory-create-datasets.md#dataset-availability) . Här är ett snabbt exempel:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
De dagliga sektorerna börjar kl. **6** i stället för standard midnatt.     

### <a name="how-can-i-rerun-a-slice"></a>Hur gör jag för att köra en sektor igen?
Du kan köra om en sektor på något av följande sätt:

* Använd övervaka och hantera app för att köra ett aktivitets fönster eller en sektor igen. Mer information finns i [köra om valt aktivitets fönster](data-factory-monitor-manage-app.md#perform-batch-actions) .   
* Klicka på **Kör** i kommando fältet på bladet **data sektor** för sektorn i Azure Portal.
* Kör **set-AzDataFactorySliceStatus-** cmdlet med status inställt på **väntar** på sektorn.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Se [set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] för information om cmdleten.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Hur lång tid tog det att bearbeta en sektor?
Använd aktivitets fönstret i Övervakare för & hantera appar för att veta hur lång tid det tog att bearbeta en data sektor. Mer information finns i [Utforskaren för aktivitets fönstret](data-factory-monitor-manage-app.md#activity-window-explorer) .

Du kan också göra följande i Azure Portal:  

1. Klicka på panelen data **uppsättningar** på **Data Factory** -bladet för din data fabrik.
2. Klicka på den angivna data uppsättningen på bladet **data uppsättningar** .
3. Välj den sektor som du är intresse rad av från listan **senaste segment** på bladet **tabell** .
4. Klicka på aktiviteten Kör från listan **aktivitets körningar** på bladet **data sektor** .
5. Klicka på panelen **Egenskaper** på bladet **AKTIVITETS körnings information** .
6. Du bör se **varaktighets** fältet med ett värde. Det här värdet är den tid det tar att bearbeta sektorn.   

### <a name="how-to-stop-a-running-slice"></a>Så här stoppar du en sektor som körs?
Om du behöver stoppa pipelinen från att köra kan du använda cmdleten [suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) . För närvarande stoppas inte de sektor körningar som pågår för tillfället genom att pausa pipelinen. När pågående körningar är slutförd hämtas ingen extra sektor.

Om du verkligen vill stoppa körningen direkt är det enda sättet att ta bort pipelinen och skapa den igen. Om du väljer att ta bort pipelinen behöver du inte ta bort tabeller och länkade tjänster som används av pipelinen.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: /archive/blogs/cindygross/use-additional-storage-accounts-with-hdinsight-hive