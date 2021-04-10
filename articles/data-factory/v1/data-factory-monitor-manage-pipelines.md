---
title: Övervaka och hantera pipelines med hjälp av Azure Portal och PowerShell
description: Lär dig hur du använder Azure Portal och Azure PowerShell för att övervaka och hantera Azure-datafabriker och pipeliner som du har skapat.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 0744a7d915d6bee868b160abc29964d58947dd28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779638"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Övervaka och hantera Azure Data Factory pipelines med hjälp av Azure Portal och PowerShell
> [!div class="op_single_selector"]
> * [Använda Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Använda övervaknings-och hanterings program](data-factory-monitor-manage-app.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [övervaka och hantera data Factory pipeliner i](../monitor-visually.md).

Den här artikeln beskriver hur du övervakar, hanterar och felsöker dina pipelines med hjälp av Azure Portal och PowerShell.

> [!IMPORTANT]
> Hanterings programmet för övervakning & ger bättre stöd för övervakning och hantering av datapipeliner och fel sökning av eventuella problem. Mer information om hur du använder programmet finns i [övervaka och hantera data Factory pipelines med hjälp av appen övervakning och hantering](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory version 1 använder nu den nya [Azure Monitor aviserings infrastrukturen](../../azure-monitor/alerts/alerts-metric.md). Den gamla aviserings infrastrukturen är föråldrad. Därför fungerar inte dina befintliga aviseringar som kon figurer ATS för data fabriker av version 1. Dina befintliga aviseringar för v1-datafabriker migreras inte automatiskt. Du måste återskapa de här aviseringarna på den nya aviserings infrastrukturen. Logga in på Azure Portal och välj **övervaka** för att skapa nya aviseringar för mått (till exempel misslyckade körningar eller lyckade körningar) för dina data fabriker för version 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Förstå pipelines och aktivitets tillstånd
Med hjälp av Azure Portal kan du:

* Visa din data fabrik som ett diagram.
* Visa aktiviteter i en pipeline.
* Visa data uppsättningar för indata och utdata.

Det här avsnittet beskriver också hur en data uppsättnings sektor övergår från ett tillstånd till ett annat tillstånd.   

### <a name="navigate-to-your-data-factory"></a>Navigera till din data fabrik
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **data fabriker** på menyn till vänster. Om du inte ser det klickar du på **fler tjänster >** och klickar sedan på **data fabriker** under kategorin **information + analys** .

   ![Bläddra bland alla > data fabriker](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. På bladet **data** fabriker väljer du den data fabrik som du är intresse rad av.

    ![Välja datafabrik](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Du bör se start sidan för data fabriken.

   ![Bladet Datafabrik](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Diagramvy över din data fabrik
**Diagramvyn** för en data fabrik innehåller ett fönster med glas för att övervaka och hantera data fabriken och dess till gångar. Om du vill se **diagramvyn** för din data fabrik klickar du på **diagram** på Start sidan för data fabriken.

![Diagramvy](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Du kan zooma in, zooma ut, zooma in, zooma till 100%, låsa diagrammets layout och automatiskt placera pipeliner och data uppsättningar. Du kan också se information om data härkomst (det vill säga Visa överordnade och underordnade objekt i valda objekt).

### <a name="activities-inside-a-pipeline"></a>Aktiviteter i en pipeline
1. Högerklicka på pipelinen och klicka sedan på **Öppna pipeline** för att se alla aktiviteter i pipeline, tillsammans med indata och utdata för data uppsättningar för aktiviteterna. Den här funktionen är användbar när din pipeline innehåller fler än en aktivitet och du vill förstå drift härkomst i en enda pipeline.

    ![Menyn Öppna pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. I följande exempel visas en kopierings aktivitet i pipelinen med indata och utdata. 

    ![Aktiviteter i en pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Du kan gå tillbaka till data fabrikens start sida genom att klicka på länken **data fabrik** i spåret i det övre vänstra hörnet.

    ![Gå tillbaka till Data Factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Visa status för varje aktivitet i en pipeline
Du kan visa det aktuella tillståndet för en aktivitet genom att visa status för alla data uppsättningar som skapas av aktiviteten.

Genom att dubbelklicka på **OutputBlobTable** i **diagrammet** kan du se alla segment som produceras av olika aktivitets körningar inuti en pipeline. Du kan se att kopierings aktiviteten har lyckats under de senaste åtta timmarna och gav sektorerna statusen **klar** .  

![Status för pipelinen](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Data uppsättnings sektorerna i data fabriken kan ha en av följande status:

<table>
<tr>
    <th align="left">Tillstånd</th><th align="left">Delstat</th><th align="left">Beskrivning</th>
</tr>
<tr>
    <td rowspan="8">Väntar</td><td>ScheduleTime</td><td>Tiden har inte kommit för att sektorn ska kunna köras.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>De överordnade beroendena är inte klara.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beräknings resurserna är inte tillgängliga.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alla aktivitets instanser är upptagna med andra sektorer.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktiviteten har pausats och kan inte köra sektorerna förrän aktiviteten har återupptagits.</td>
</tr>
<tr>
<td>Försök igen</td><td>Det görs ett nytt försök att köra aktiviteten.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen har inte startat ännu.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Verifieringen väntar på att göras om.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Pågår</td><td>Verifiera</td><td>Verifiering pågår.</td>
</tr>
<td>-</td>
<td>Sektorn bearbetas.</td>
</tr>
<tr>
<td rowspan="4">Misslyckad</td><td>Stängningsåtgärd</td><td>Aktivitets körningen tog längre tid än vad som tillåts av aktiviteten.</td>
</tr>
<tr>
<td>Avbrutna</td><td>Sektorn avbröts av en användar åtgärd.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen misslyckades.</td>
</tr>
<tr>
<td>-</td><td>Det gick inte att generera sektorn och/eller verifiera den.</td>
</tr>
<td>Redo</td><td>-</td><td>Sektorn är klar för användning.</td>
</tr>
<tr>
<td>Överhoppad</td><td>Inget</td><td>Sektorn bearbetas inte.</td>
</tr>
<tr>
<td>Inget</td><td>-</td><td>En sektor som används för att existera med en annan status, men som har återställts.</td>
</tr>
</table>



Du kan visa information om en sektor genom att klicka på en segment post på bladet **nyligen uppdaterade segment** .

![Information om segment](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Om sektorn har körts flera gånger visas flera rader i listan **aktivitets körningar** . Du kan visa information om en aktivitets körning genom att klicka på körnings posten i listan **aktivitets körningar** . I listan visas alla loggfiler, tillsammans med ett fel meddelande om det finns en sådan. Den här funktionen är användbar för att visa och felsöka loggar utan att behöva lämna din data fabrik.

![Aktivitetskörningsinformation](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Om sektorn inte har statusen **klar** kan du se de överordnade segmenten som inte är klara och blockerar den aktuella sektorn från att köras i de **överordnade segment som inte är klara** . Den här funktionen är användbar när din sektor är i **vänte** läge och du vill förstå de överordnade överordnade beroenden som sektorn väntar på.

![Överordnade segment som inte är klara](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram över data uppsättnings tillstånd
När du har distribuerat en data fabrik och pipelinen har en giltig aktiv period, övergår data uppsättnings segmenten över från ett tillstånd till ett annat. För närvarande följer sektor statusen följande tillstånds diagram:

![Tillstånds diagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Flödet för data uppsättnings överföring i Data Factory är följande: väntar-> pågående/pågående (validering)-> klart/misslyckade.

Sektorn startar i ett **vänte** läge, vilket väntar på att villkor ska uppfyllas innan den körs. Sedan startar aktiviteten och sektorn hamnar **i ett pågående** tillstånd. Aktivitets körningen kan lyckas eller Miss lyckas. Sektorn har marker ATS som **klar** eller **misslyckad**, baserat på resultatet av körningen.

Du kan återställa sektorn så att den går tillbaka från det **färdiga** eller **misslyckade** läget till **vänte** läge. Du kan också markera sektor tillstånd att **hoppa över**, vilket förhindrar att aktiviteten körs och inte bearbetar sektorn.

## <a name="pause-and-resume-pipelines"></a>Pausa och återuppta pipeliner
Du kan hantera dina pipelines med hjälp av Azure PowerShell. Du kan till exempel pausa och återuppta pipeliner genom att köra Azure PowerShell-cmdletar. 

> [!NOTE] 
> Diagramvyn har inte stöd för att pausa och återuppta pipeliner. Om du vill använda ett användar gränssnitt använder du övervaknings-och hanterings programmet. Mer information om hur du använder programmet finns i [övervaka och hantera data Factory pipelines med hjälp av appen övervakning och hantering](data-factory-monitor-manage-app.md) . 

Du kan pausa/pausa pipelines med hjälp av PowerShell-cmdleten **suspend-AzDataFactoryPipeline** . Denna cmdlet är användbar när du inte vill köra pipelines förrän ett problem har åtgärd ATS. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Exempel:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

När problemet har åtgärd ATS med pipelinen kan du återuppta den pausade pipelinen genom att köra följande PowerShell-kommando:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Exempel:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Felsöka pipelines
Azure Data Factory innehåller omfattande funktioner som du kan använda för att felsöka och felsöka pipelines med hjälp av Azure Portal och Azure PowerShell.

> [!NOTE] 
> Det är mycket enklare att troubleshot fel med hjälp av appen övervakning & hantering. Mer information om hur du använder programmet finns i [övervaka och hantera data Factory pipelines med hjälp av appen övervakning och hantering](data-factory-monitor-manage-app.md) . 

### <a name="find-errors-in-a-pipeline"></a>Hitta fel i en pipeline
Om aktivitets körningen Miss lyckas i en pipeline, är data uppsättningen som skapas av pipelinen i ett fel tillstånd på grund av felet. Du kan felsöka och felsöka fel i Azure Data Factory med hjälp av följande metoder.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Felsöka ett fel med hjälp av Azure Portal
1. På bladet **tabell** klickar du på det problem segment som har **statusen** inställt på **misslyckad**.

   ![Tabell blad med problem sektor](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. På bladet **data sektor** klickar du på aktivitets körningen som misslyckades.

   ![Data sektor med fel](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. På bladet **aktivitets körnings information** kan du ladda ned de filer som är associerade med HDInsight-bearbetningen. Klicka på **Hämta** för status/stderr för att hämta fel logg filen som innehåller information om felet.

   ![Bladet körnings information för aktivitet med fel](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Felsöka ett fel med PowerShell
1. Starta **PowerShell**.
2. Kör kommandot **Get-AzDataFactorySlice** för att se sektorerna och deras status. Du bör se en sektor med statusen **misslyckades**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Exempel:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Ersätt **StartDateTime** med start tiden för din pipeline. 
3. Kör nu cmdleten **Get-AzDataFactoryRun** för att få information om aktivitets körningen för sektorn.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Exempel:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Värdet för StartDateTime är start tiden för det fel/problem-segment som du noterade i föregående steg. Datum/tid ska omges av dubbla citat tecken.
4. Du bör se utdata med information om felet som liknar följande:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Du kan köra cmdleten **Save-AzDataFactoryLog** med det ID-värde som visas i utdata och ladda ned loggfilerna med hjälp av cmdleten **-DownloadLogsoption** för cmdleten.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Köra om felen i en pipeline

> [!IMPORTANT]
> Det är enklare att felsöka fel och köra Felaktiga segment igen med hjälp av appen övervakning & hantering. Mer information om hur du använder programmet finns i [övervaka och hantera data Factory pipelines med hjälp av appen övervakning och hantering](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
När du har fel söknings-och fel söknings fel i en pipeline kan du köra om fel genom att gå till fel sektorn och klicka på knappen **Kör** i kommando fältet.

![Köra en felaktig sektor igen](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Om sektorn inte kunde verifieras på grund av ett princip fel (till exempel om data inte är tillgängliga) kan du åtgärda felet och verifiera det igen genom att klicka på knappen **Verifiera** i kommando fältet.

![Åtgärda fel och validera](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Använda Azure PowerShell
Du kan köra om ett problem med cmdleten **set-AzDataFactorySliceStatus** . Se avsnittet [set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus) för syntax och annan information om cmdleten.

**Exempel:**

I följande exempel anges status för alla segment för tabellen ' DAWikiAggregatedData ' till ' waiting ' i Azure Data Factory ' WikiADF '.

"Uppdateringstyp" är inställt på "UpstreamInPipeline", vilket innebär att status för varje sektor för tabellen och alla beroende (överordnade) tabeller anges till "väntar". Det andra möjliga värdet för den här parametern är ' individ '.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Skapa aviseringar i Azure Portal

1.  Logga in på Azure Portal och välj **övervaka-> aviseringar** för att öppna sidan aviseringar.

    ![Öppna sidan aviseringar.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Välj **+ ny varnings regel** för att skapa en ny avisering.

    ![Skapa en ny avisering](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definiera **varnings villkoret**. (Se till att välja **data fabriker** i fältet **Filtrera efter resurs typ** .) Du kan också ange värden för **dimensioner**.

    ![Definiera aviserings villkoret – Välj mål](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definiera aviserings villkoret – Lägg till aviserings villkor](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definiera aviserings villkoret – Lägg till aviserings logik](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definiera **aviserings informationen**.

    ![Definiera aviserings informationen](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definiera **Åtgärds gruppen**.

    ![Definiera åtgärds gruppen – Skapa en ny åtgärds grupp](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definiera egenskaper för åtgärds grupps uppsättning](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definiera åtgärds gruppen – ny åtgärds grupp har skapats](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Flytta en data fabrik till en annan resurs grupp eller prenumeration
Du kan flytta en data fabrik till en annan resurs grupp eller en annan prenumeration genom att använda knappen **Flytta** kommando fält på Start sidan för din data fabrik.

![Flytta data fabrik](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Du kan också flytta relaterade resurser (till exempel aviseringar som är associerade med data fabriken) tillsammans med data fabriken.

![Dialog rutan Flytta resurser](./media/data-factory-monitor-manage-pipelines/MoveResources.png)