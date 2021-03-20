---
title: 'Data Factory självstudie: första data pipelinen '
description: Den här Azure Data Factory själv studie kursen visar hur du skapar och schemalägger en data fabrik som bearbetar data med hjälp av Hive-skript i ett Hadoop-kluster.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 7f1de53e20614ca66c91735ce462da5a194d1836
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377235"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Självstudie: Bygg din första pipeline för att transformera data med Hadoop-kluster
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [Quickstart: Create a data factory using Azure Data Factory](../quickstart-create-data-factory-dot-net.md) (Snabbstart: Skapa en datafabrik med Azure Data Factory) om du använder den aktuella versionen av Data Factory-tjänsten.

I den här självstudien skapar du din första Azure-datafabrik med en data pipeline. Pipelinen transformerar indata genom att köra Hive-skript på ett Azure HDInsight-kluster (Hadoop) för att skapa utdata.  

Den här artikeln innehåller en översikt och förutsättningar för självstudien. När du har slutfört kraven kan du göra en själv studie kurs med hjälp av något av följande verktyg/SDK: er: Visual Studio, PowerShell, Resource Manager-mall, REST API. Välj ett av alternativen i list rutan i början (eller) länkar i slutet av den här artikeln för att göra självstudien med hjälp av något av dessa alternativ.    

## <a name="tutorial-overview"></a>Självstudier – översikt
I den här självstudien får du göra följande:

1. Skapa en **data fabrik**. En data fabrik kan innehålla en eller flera datapipelines som flyttar och transformerar data.

    I den här självstudien skapar du en pipeline i data fabriken.
2. Skapa en **pipeline**. En pipeline kan ha en eller flera aktiviteter (exempel: kopierings aktivitet, HDInsight Hive-aktivitet). I det här exemplet används den HDInsight Hive-aktivitet som kör ett Hive-skript på ett HDInsight Hadoop-kluster. Skriptet skapar först en tabell som refererar till rå webb logg data som lagras i Azure Blob Storage och partitionerar rå data efter år och månad.

    I den här självstudien använder pipelinen Hive-aktiviteten för att transformera data genom att köra en Hive-fråga på ett Azure HDInsight Hadoop kluster.
3. Skapa **länkade tjänster**. Du skapar en länkad tjänst för att länka ett data lager eller en beräknings tjänst till data fabriken. Ett data lager som Azure Storage innehåller indata/utdata för aktiviteter i pipelinen. En beräknings tjänst som HDInsight Hadoop Cluster processs/transformerar data.

    I den här självstudien skapar du två länkade tjänster: **Azure Storage** och **Azure HDInsight**. Den länkade tjänsten Azure Storage länkar ett Azure Storage konto som innehåller in-/utdata-data till data fabriken. Den länkade Azure HDInsight-tjänsten länkar ett Azure HDInsight-kluster som används för att omvandla data till data fabriken.
3. Skapa data **uppsättningar** för indata och utdata. En indatauppsättning representerar indata för en aktivitet i pipeline och en utdatauppsättning representerar utdata för aktiviteten.

    I den här självstudien anger indata och utdata-datauppsättningar platser för indata och utdata i Azure-Blob Storage. Den länkade tjänsten Azure Storage anger vilket Azure Storage konto som används. En indata-datauppsättning anger var indatafilerna finns och en data uppsättning för utdata anger var utdatafilerna placeras.


I [Introduktion till Azure Data Factory](data-factory-introduction.md) artikel finns en detaljerad översikt över Azure Data Factory.

Här är **diagramvyn** för exempel data fabriken som du skapar i den här självstudien. **MyFirstPipeline** har en aktivitet av typen Hive som använder **AzureBlobInput** dataset som indata och genererar **AzureBlobOutput** data uppsättning som utdata.

![Diagramvy i Data Factory själv studie kurs](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


I den här självstudien innehåller mappen **inputdata** i Azure Blob-behållaren för **adfgetstarted** en fil med namnet indata. log. Logg filen har poster från tre månader: januari, februari och mars 2016. Här är exempel raderna för varje månad i indatafilen.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

När filen bearbetas av pipelinen med HDInsight Hive-aktiviteten kör aktiviteten ett Hive-skript i HDInsight-klustret som partitionerar indata per år och månad. Skriptet skapar tre mappar för utdata som innehåller en fil med poster från varje månad.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Från de exempel rader som visas ovan skrivs den första (med 2016-01-01) till filen 000000_0 i mappen månad = 1. På samma sätt skrivs den andra filen i mappen månad = 2 och den tredje är skriven i filen i mappen månad = 3.  

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här självstudien måste du ha följande krav:

1. **Azure-prenumeration** – om du inte har en Azure-prenumeration kan du skapa ett kostnads fritt utvärderings konto på bara några minuter. Se artikeln om [kostnads fri utvärdering](https://azure.microsoft.com/pricing/free-trial/) av hur du kan skaffa ett kostnads fritt utvärderings konto.
2. **Azure Storage** – du använder ett Azure Storage-konto för att lagra data i den här självstudien. Om du inte har ett Azure Storage-konto går du till artikeln [skapa ett lagrings konto](../../storage/common/storage-account-create.md) . När du har skapat lagrings kontot noterar du **konto namnet** och **åtkomst nyckeln**. Information om hur du hämtar åtkomst nycklar för lagrings kontot finns i [Hantera åtkomst nycklar för lagrings kontot](../../storage/common/storage-account-keys-manage.md).
3. Hämta och granska Hive-frågearkivet (**HQL**) på: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql) . Den här frågan transformerar indata för att skapa utdata.
4. Hämta och granska exempel indatafilen (**indata. log**) på: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Skapa en BLOB-behållare med namnet **adfgetstarted** i din Azure-Blob Storage.
6. Ladda upp filen **skriptfilen partitionweblogs. HQL** i mappen **script** i behållaren **adfgetstarted** . Använd verktyg som [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Ladda upp **indata. log** -filen till mappen **inputdata** i behållaren **adfgetstarted** .

När du har slutfört kraven väljer du något av följande verktyg/SDK: er för att göra självstudien:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio ger ett grafiskt användar gränssnitt för att skapa dina data fabriker. I PowerShell, Resource Manager-mallen och REST API alternativ finns skript-och programmerings sätt för att skapa dina data fabriker.

> [!NOTE]
> Datapipelinen i den här självstudien transformerar indata för att generera utdata. Den kopierar inte data från ett källdatalager till ett måldatalager. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).
>
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory).
