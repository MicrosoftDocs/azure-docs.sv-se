---
title: Skapa schema utlösare i Azure Data Factory
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline enligt ett schema.
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: f10dac4e70a1edb05f2f2c02c48b9ae16c4f6823
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177835"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Skapa en utlösare som kör en pipeline enligt ett schema

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln innehåller information om schema utlösaren och stegen för att skapa, starta och övervaka en schema utlösare. För andra typer av utlösare, se [pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md).

När du skapar en schema utlösare anger du ett schema (start datum, upprepning, slutdatum osv.) för utlösaren och associerar med en pipeline. Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en pipeline. En enskild utlösare kan starta flera pipeliner.

I följande avsnitt beskrivs hur du skapar en schema utlösare på olika sätt. 

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

Du kan skapa en **schema utlösare** för att schemalägga en pipeline så att den körs regelbundet (varje timme, varje dag osv.). 

> [!NOTE]
> En fullständig genom gång av hur du skapar en pipeline och en schema utlösare, som associerar utlösaren med pipelinen och kör och övervakar pipelinen finns i [snabb start: skapa en data fabrik med hjälp av Data Factory UI](quickstart-create-data-factory-portal.md).

1. Växla till fliken **Redigera** , som visas med en Penn symbol. 

    ![Växla till fliken Redigera](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. Välj **utlösare** på menyn och välj sedan **ny/redigera**. 

    ![Menyn Ny utlösare](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. På sidan **Lägg till utlösare** väljer du **Välj utlösare...** och väljer sedan **+ ny**. 

    ![Lägg till utlösare – ny utlösare](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. Utför följande steg på sidan **Ny utlösare** : 

    1. Bekräfta att **schemat** har valts för **typ**.
    1. Ange start datum/tid för utlösaren för **start datum**. Den ställs in på aktuellt datum/tid i UTC (Coordinated Universal Time) som standard.
    1. Ange den tidszon som utlösaren ska skapas i. Tids zons inställningen gäller för **start datum**, **slutdatum** och **schema körnings tider** i avancerade upprepnings alternativ. Att ändra tids zons inställningen ändrar inte start datumet automatiskt. Kontrol lera att start datumet är rätt i den angivna tids zonen. Observera att den schemalagda körnings tiden för utlösaren kommer att anses som start datum (se till att start datumet är minst 1minute mindre än körnings tiden annars utlöses pipelinen i nästa upprepning). 

        > [!NOTE]
        > För tids zoner som ser sommar tid, justeras utlösnings tiden automatiskt för de två två årens ändringar. Välj en tidszon som inte ser sommar tid att spara, till exempel UTC, om du vill välja att inte använda sommar tids ändringar.

    1. Ange **upprepning** för utlösaren. Välj ett av värdena i list rutan (varje minut, varje timme, varje dag, varje vecka och varje månad). Ange multiplikatorn i text rutan. Om du till exempel vill att utlösaren ska köras en gång för var 15: e minut väljer du **varje minut** och anger **15** i text rutan. 
    1. Om du vill ange en Slutdatum tid väljer du **Ange ett slutdatum** och anger _slutar på_ och väljer sedan **OK**. Det finns ingen associerad kostnad till varje pipelinekörning. Om du testar kanske du vill se till att pipelinen bara utlöses några gånger. Men se till att det finns tillräckligt med tid att köra pipelinen mellan publiceringstiden och sluttiden. Utlösaren träder endast i kraft när du har publicerat lösningen till Data Factory, och inte när du sparar utlösaren i användargränssnittet.

        ![Inställningar för utlösare](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![Utlös ande inställningar för slutdatum](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. I fönstret **Ny utlösare** väljer du **Ja** i alternativet **aktive rad** och väljer sedan **OK**. Du kan använda den här kryss rutan för att inaktivera utlösaren senare. 

    ![Inställningar för utlösare – knappen Nästa](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. I fönstret **Ny utlösare** granskar du varnings meddelandet och väljer sedan **OK**.

    ![Inställningar för utlösare – knappen Slutför](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. Välj **publicera alla** för att publicera ändringarna till Data Factory. Innan du publicerar ändringarna i Data Factory startar inte utlösaren att utlösa pipelinen. 

    ![Knappen Publicera](./media/how-to-create-schedule-trigger/publish-2.png)

1. Växla till fliken **pipeline-körningar** till vänster och välj sedan **Uppdatera** för att uppdatera listan. Pipeline-körningar som har utlösts av den schemalagda utlösaren visas. Observera värdena i kolumnen **Aktiverad av**. Om du använder alternativet **utlösare nu** visas den manuella utlösaren kör i listan. 

    ![Övervaka utlösta körningar](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. Växla till **utlösaren kör**  \  vyn **schema** . 

    ![Övervaka utlösarkörningar](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en schema utlösare. För att se det här exempel arbetet går du först igenom [snabb starten: skapa en data fabrik med hjälp av Azure PowerShell](quickstart-create-data-factory-powershell.md). Lägg sedan till följande kod i Main-metoden, som skapar och startar en schema utlösare som körs var 15: e minut. Utlösaren är associerad med en pipeline med namnet **Adfv2QuickStartPipeline** som du skapar som en del av snabb starten.

1. Skapa en JSON-fil med namnet **MyTrigger.js** i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

    > [!IMPORTANT]
    > Innan du sparar JSON-filen ställer du in värdet för **StartTime** -elementet på den aktuella UTC-tiden. Ange värdet för slut tids **elementet till** en timme efter den aktuella UTC-tiden.

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    I JSON-kodfragmentet:
    - **Typ** elementet i utlösaren har angetts till "ScheduleTrigger".
    - **Frekvens** elementet anges till "minut" och **Interval** -elementet är inställt på 15. Därför kör utlösaren pipelinen var 15: e minut mellan start-och slut tiderna.
    - **TimeZone** -elementet anger den tidszon som utlösaren skapas i. Den här inställningen påverkar både **StartTime** **och slut** tid.
    - Slut **tid elementet är** en timme efter värdet för **StartTime** -elementet. Därför kör utlösaren pipelinen 15 minuter, 30 minuter och 45 minuter efter start tiden. Glöm inte att uppdatera start tiden till den aktuella UTC-tiden och slut tiden till en timme efter start tiden. 

        > [!IMPORTANT]
        > För UTC-timezone måste StartTime och slut tid uppfylla formatet ÅÅÅÅ-MM-ddTHH: mm: SS **Z**, medan för andra tids zoner, StartTime och slut tid följer "åååå-mm-ddTHH: mm: SS". 
        > 
        > Enligt ISO 8601 standard markeras det _Z_ -suffixet för tidsstämpeln som datum/tid till UTC-tidszonen och rendera fältet för tids zonen. Även om _Z_ -suffix saknas för UTC-tidszonen leder det till ett fel vid _aktivering_ av utlösare.

    - Utlösaren är associerad med **Adfv2QuickStartPipeline** -pipeline. Lägg till fler **pipelineReference** avsnitt om du vill associera flera pipeliner med en utlösare.
    - Pipelinen i snabb starten tar två **parameter** värden: **inputPath** och **outputPath**. Och du skickar värden för dessa parametrar från utlösaren.

1. Skapa en utlösare med hjälp av cmdleten **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. Bekräfta att statusen för utlösaren har **stoppats** med cmdleten **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Starta utlösaren med cmdleten **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Bekräfta att statusen för utlösaren har **startats** med hjälp av cmdleten **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  Hämta utlösaren körs i Azure PowerShell med hjälp av cmdleten **Get-AzDataFactoryV2TriggerRun** . Om du vill hämta information om utlösarens körningar kör du följande kommando med jämna mellanrum. Uppdatera värdena för **TriggerRunStartedAfter** och **TriggerRunStartedBefore** så att de matchar värdena i din utlösnings definition:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > Utlösarens tid för schema utlösare anges i UTC-tidsstämpel. _TriggerRunStartedAfter_ och _TriggerRunStartedBefore_ förväntar sig också UTC-tidsstämpel

    Information om hur du övervakar utlösare som körs och pipelines körs i Azure Portal finns i [övervaka pipeline-körningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="net-sdk"></a>.NET SDK

Det här avsnittet visar hur du använder .NET SDK för att skapa, starta och övervaka en utlösare. För att se det här exempel arbetet går du först igenom [snabb starten: skapa en data fabrik med hjälp av .NET SDK](quickstart-create-data-factory-dot-net.md). Lägg sedan till följande kod i Main-metoden, som skapar och startar en schema utlösare som körs var 15: e minut. Utlösaren är associerad med en pipeline med namnet **Adfv2QuickStartPipeline** som du skapar som en del av snabb starten.

Om du vill skapa och starta en schema utlösare som körs var 15: e minut lägger du till följande kod i Main-metoden:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Om du vill skapa utlösare i en annan tidszon än UTC, krävs följande inställningar:
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

Om du vill övervaka en Utlös ande körning lägger du till följande kod före den sista `Console.WriteLine` instruktionen i exemplet:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Information om hur du övervakar utlösare som körs och pipelines körs i Azure Portal finns i [övervaka pipeline-körningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="python-sdk"></a>Python SDK

Det här avsnittet visar hur du använder python SDK för att skapa, starta och övervaka en utlösare. För att se det här exempel arbetet går du först igenom [snabb starten: skapa en data fabrik med hjälp av python SDK](quickstart-create-data-factory-python.md). Lägg sedan till följande kodblock efter kod blocket "övervaka pipeline-körning" i python-skriptet. Den här koden skapar en schema utlösare som körs var 15: e minut mellan angivna start-och slut tider. Uppdatera variabeln **start_time** till den aktuella UTC-tiden och variabeln **end_time** till en timme efter den aktuella UTC-tiden.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Information om hur du övervakar utlösare som körs och pipelines körs i Azure Portal finns i [övervaka pipeline-körningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Du kan använda en Azure Resource Manager mall för att skapa en utlösare. Stegvisa instruktioner finns i [skapa en Azure-datafabrik med hjälp av en Resource Manager-mall](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Överför start tiden för utlösaren till en pipeline

Azure Data Factory version 1 stöder läsning eller skrivning av partitionerade data med hjälp av systemvariablerna: **SliceStart**, **SliceEnd**, **WindowStart** och **WindowEnd**. I den aktuella versionen av Azure Data Factory kan du uppnå det här beteendet med hjälp av en pipeline-parameter. Start tiden och den schemalagda tiden för utlösaren anges som värde för pipeline-parametern. I följande exempel skickas den schemalagda tiden för utlösaren som ett värde till **scheduledRunTime** -parametern för pipelinen:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON-schema

Följande JSON-definition visar hur du skapar en schema utlösare med schemaläggning och upprepning:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  Egenskapen **parameters** är en obligatorisk egenskap i elementet **pipelines**. Om din pipeline inte tar emot några parametrar måste du ta med en JSON-definition för egenskapen **parameters**.


### <a name="schema-overview"></a>Översikt över schema

I följande tabell ges en översikt över de viktigaste schemaelementen relaterade till upprepning och schemaläggning i en utlösare:

| JSON-egenskap | Beskrivning |
|:--- |:--- |
| **/St** | Ett datum/tid-värde. För enkla scheman gäller värdet för egenskapen **startTime** den första förekomsten. För komplexa scheman startar utlösaren tidigast vid det angivna värdet för **startTime**. <br> För UTC-tidszonen är format `'yyyy-MM-ddTHH:mm:ssZ'` , för annan tidszon, format `'yyyy-MM-ddTHH:mm:ss'` . |
| **Slut** | Slutdatum och tidpunkt för utlösaren. Utlösaren körs inte efter angivet slutdatum och sluttid. Värdet för egenskapen kan inte ha passerat. Den här egenskapen är valfri.  <br> För UTC-tidszonen är format `'yyyy-MM-ddTHH:mm:ssZ'` , för annan tidszon, format `'yyyy-MM-ddTHH:mm:ss'` . |
| **Informationen** | Tids zonen som utlösaren skapas i. Den här inställningen påverkar **StartTime**, slut **tid och schema**.  Se [lista över tids zoner som stöds](#time-zone-option) |
| **mönster** | Ett upprepningsobjekt som anger upprepningsregler för utlösaren. Upprepningsobjektet har stöd för elementen **frequency** (frekvens), **interval** (intervall), **endTime** (sluttid), **count** (antal) och **schedule** (schema). När du definierar ett upprepningsobjekt är elementet **frequency** obligatoriskt. De andra elementen är valfria. |
| **frequency** | Frekvensen som utlösaren ska upprepas med. Du kan använda värden som ”minute”, ”hour”, ”day”, ”week” och ”month”. |
| **intervall** | Ett positivt heltal som anger intervallet för värdet för **frequency** och som avgör hur ofta utlösaren körs. Om **interval** till exempel är 3 och **frequency** är ”week” (vecka) upprepas utlösaren var tredje vecka. |
| **Ange** | Upprepningsschemat för utlösaren. En utlösare med ett angivet värde för **frequency** ändrar sin upprepning baserat på ett upprepningsschema. Egenskapen **schedule** innehåller ändringar för upprepningen som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonummer.

> [!IMPORTANT]
> För UTC-timezone måste StartTime och slut tid uppfylla formatet ÅÅÅÅ-MM-ddTHH: mm: SS **Z**, medan för andra tids zoner, StartTime och slut tid följer "åååå-mm-ddTHH: mm: SS". 
> 
> Enligt ISO 8601 standard markeras det _Z_ -suffixet för tidsstämpeln som datum/tid till UTC-tidszonen och rendera fältet för tids zonen. Även om _Z_ -suffix saknas för UTC-tidszonen leder det till ett fel vid _aktivering_ av utlösare.

### <a name="schema-defaults-limits-and-examples"></a>Standardvärden för scheman, begränsningar och exempel

| JSON-egenskap | Typ | Obligatorisk | Standardvärde | Giltiga värden | Exempel |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **/St** | Sträng | Ja | Inget | ISO 8601-datum/tid | för UTC-tidszonen `"startTime" : "2013-01-09T09:30:00-08:00Z"` <br> för annan tidszon `"2013-01-09T09:30:00-08:00"` |
| **Informationen** | Sträng | Ja | Inget | [Tids zons värden](#time-zone-option)  | `"UTC"` |
| **mönster** | Objekt | Ja | Inget | Upprepningsobjekt | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **intervall** | Antal | Inga | 1 | 1 till 1 000 | `"interval":10` |
| **Slut** | Sträng | Ja | Inget | Ett datum/tid-värde som representerar en tidpunkt i framtiden. | för UTC-tidszonen `"endTime" : "2013-02-09T09:30:00-08:00Z"` <br> för annan tidszon `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **Ange** | Objekt | Inga | Inget | Schemaobjekt | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>Tids zons alternativ

Här följer några tids zoner som stöds för schemalagda utlösare:

| Tidszon | UTC-förskjutning (icke-sommar) | timeZone-värde | Observera sommar tid | Tids stämplings format |
| :--- | :--- | :--- | :--- | :--- |
| Koordinerad universell tid | 0 | `UTC` | Inga | `'yyyy-MM-ddTHH:mm:ssZ'`|
| Pacific Time (PT) | -8 | `Pacific Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Central tid (CT) | −6 | `Central Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Eastern Time (ET) | -5 | `Eastern Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Greenwich Mean Time (GMT) | 0 | `GMT Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Central europeisk normal tid | +1 | `W. Europe Standard Time` | Ja | `'yyyy-MM-ddTHH:mm:ss'` |
| Indien, normal tid (IST) | + 5:30 | `India Standard Time` | Inga | `'yyyy-MM-ddTHH:mm:ss'` |
| Kina, normal tid | + 8 | `China Standard Time` | Inga | `'yyyy-MM-ddTHH:mm:ss'` |

Den här listan är ofullständig. För en fullständig lista över tids zons alternativ, utforska på sidan för att skapa Data Factory Portal [utlösare](#data-factory-ui)

### <a name="starttime-property"></a>Egenskapen startTime
I följande tabell visas hur egenskapen **startTime** styr körningen av en utlösare:

| startTime-värde | Upprepning utan schema | Upprepning med schema |
|:--- |:--- |:--- |
| Starttid i förfluten tid | Beräknar första framtida körningstid efter starttiden och körs vid den tidpunkten.<br/><br/>Utför efterföljande körningar baserat på beräkningar från senaste körningstid.<br/><br/>Se exemplet som följer den här tabellen. | Utlösaren startar _tidigast_ den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br/><br/>Kör efterföljande körningar baserat på upprepningsschemat. |
| Starttid i framtiden eller aktuell tid | Körs en gång på angiven starttid.<br/><br/>Utför efterföljande körningar baserat på beräkningar från senaste körningstid. | Utlösaren startar _tidigast_ den angivna start tiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br/><br/>Kör efterföljande körningar baserat på upprepningsschemat. |

Låt oss se vad som händer när starttiden har passerat, med upprepning men utan schema. Anta att den aktuella tiden är `2017-04-08 13:00`, starttiden är `2017-04-07 14:00` och upprepningen är varannan dag. ( **Upprepning** svärdet definieras genom att ange **frekvens** egenskapen till "Day" och egenskapen **Interval** till 2.) Observera att **StartTime** -värdet är i det förflutna och inträffar före den aktuella tiden.

Under dessa villkor är den första körningen vid `2017-04-09` `14:00` . Scheduler-motor beräknar körningsförekomster från starttiden. Alla tidigare instanser ignoreras. Motorn använder nästa förekomst som förekommer i framtiden. I det här scenariot är start tiden `2017-04-07` på `2:00pm` , så nästa instans är två dagar från den tiden, som finns `2017-04-09` på `2:00pm` .

Den första körningstiden är samma oavsett om värdet för **startTime** är `2017-04-05 14:00` eller `2017-04-01 14:00`. Efter den första körningen beräknas efterföljande körningar med hjälp av schemat. Därför finns de efterföljande körningarna på `2017-04-11` `2:00pm` , sedan på, `2017-04-13` sedan på `2:00pm` `2017-04-15` `2:00pm` och så vidare.

När inga timmar eller minuter anges i schemat för en utlösare används som standard samma timmar och minuter som i den första körningen.

### <a name="schedule-property"></a>Egenskapen schedule

Å ena sidan kan ett schema begränsa antalet utlösarkörningar. Om en utlösare med månatlig frekvens till exempel har ett schema som bara körs dag 31 så körs utlösaren bara de månader som har en 31:a dag.

Ett schema kan å andra sidan även utöka antalet utlösarkörningar. En utlösare med månatlig frekvens som har schemalagts för att köras dag 1 och 2 körs två snarare än en gång i månaden.

Om du anger flera **schedule**-element utvärderas de från största till minsta schemainställning. Utvärderingen börjar med veckonummer, sedan dag i månaden, veckodag, timme och slutligen minut.

I följande tabell beskrivs **schedule**-elementen i detalj:

| JSON-element | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |
| **fördröjning** | Minuter för den timme då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul>
| **timmarna** | Timmar på dagen då utlösaren körs. | <ul><li>Integer</li><li>Heltalsmatris</li></ul> |
| **weekDays** | Veckodagar som utlösaren körs på. Värdet kan bara anges med en veckofrekvens. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday och Sunday</li><li>Matris med dagvärden (maximal matrisstorlek är 7)</li><li>Dagvärdena är inte skiftlägeskänsliga</li></ul> |
| **monthlyOccurrences** | Dagar i månaden som utlösaren körs på. Värdet kan bara anges med en månadsfrekvens. | <ul><li>Matris med **monthlyOccurrence** -objekt: `{ "day": day,  "occurrence": occurrence }` .</li><li>Attributet **day** är veckodagen som utlösaren körs på. Om egenskapen **monthlyOccurrences** till exempel har **day**-värdet `{Sunday}` innebär det varje söndag i månaden. Attributet **day** är obligatoriskt.</li><li>Attributet **occurrence** är förekomsten av **day**-värdet i månaden. Om egenskapen **monthlyOccurrences** till exempel har **day**- och **occurrence**-värdena `{Sunday, -1}` innebär det den sista söndagen i månaden. Attributet **occurrence** är valfritt.</li></ul> |
| **monthDays** | Dagar i månaden som utlösaren körs på. Värdet kan bara anges med en månadsfrekvens. | <ul><li>Ett värde < = -1 och > =-31</li><li>Ett värde > = 1 och < = 31</li><li>Matris med värden</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>Exempel på scheman för upprepning av utlösare

I det här avsnittet ges exempel på upprepningsscheman med fokus på objektet **schedule** och dess element.

I exemplen antas att värdet för **interval** är 1 och att värdet för **frequency** är giltigt enligt schemadefinitionen. Du kan till exempel inte ha ett **frekvens** värde för "Day" och har även en "monthDays"-ändring i **schemaobjektet** . De här begränsningarna tas upp i tabellen i föregående avsnitt.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` | Kör kl. 05.00 varje dag. |
| `{"minutes":[15], "hours":[5]}` | Kör kl. 05.15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` | Kör kl. 05.15 och 17.15 varje dag |
| `{"minutes":[15,45], "hours":[5,17]}` | Kör kl. 05.15, 5.45, 17.15 och 17.45 varje dag. |
| `{"minutes":[0,15,30,45]}` | Kör var 15:e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Kör varje timme. Den här utlösaren körs varje timme. Minuterna styrs av **startTime**-värdet när du anger ett sådant. Om du inte anger något värde styrs minuterna av tiden för skapandet. Om starttiden eller skapandetiden (beroende på vilken som gäller) till exempel är 00.25 körs utlösaren 00.25, 01.25, 02.25, …, 23:25.<br/><br/>Det här schemat motsvarar en utlösare med **frekvens** svärdet "Hour", ett **intervall** värde på 1, och inget **schema**.  Det här schemat kan användas med andra värden för **frequency** och **interval** om du vill skapa andra utlösare. Till exempel när **frekvens** svärdet är "månad" körs schemat bara en gång i månaden, i stället för varje dag, när **frekvens** svärdet är "dag". |
| `{"minutes":[0]}` | Körs varje hel timme. Den här utlösaren körs varje timma med början vid 00.00, 01.00, 02.00 och så vidare.<br/><br/>Det här schemat motsvarar en utlösare med **frequency**-värdet ”hour” och **startTime**-värdet noll minuter, eller inget **schema** men **frequency**-värdet ”day”. Om värdet för **frekvens** är "vecka" eller "månad" körs schemat en dag i veckan eller en dag i månaden. |
| `{"minutes":[15]}` | Körs 15 minuter efter varje hel timme. Den här utlösaren körs 15 minuter efter varje timme med början vid 00.15, 01.15, 02.15 och så vidare. |
| `{"hours":[17], "weekDays":["saturday"]}` | Körs 17.00 varje lördag. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17.00 varje måndag, onsdag och fredag. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17.15 och 17.45 varje måndag, onsdag och fredag. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15:e minut på vardagar. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15:e minut på vardagar mellan 09.00 och 16.45. |
| `{"weekDays":["tuesday", "thursday"]}` | Körs varje tisdag och torsdag den angivna starttiden. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Körs 06.00 den 28:e dagen varje månad (förutsatt att **frequency**-värdet är ”month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Körs 06.00 den sista dagen i månaden. Om du vill köra en utlösare den sista dagen i månaden ska du använda -1 istället för dag 28, 29, 30 eller 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Körs 06.00 den första och sista dagen varje månad. |
| `{monthDays":[1,14]}` | Körs den första och den fjortonde dagen i varje månad den angivna starttiden. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs den första fredagen i varje månad 05.00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs den första fredagen i varje månad vid den angivna starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Körs den tredje fredagen från slutet av månad, varje månad, vid den angivna starttiden. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs den första och sista fredagen i varje månad 05.15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs den första och sista fredagen i varje månad vid den angivna starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Körs den femte fredagen i varje månad vid den angivna starttiden. När det inte finns någon femte fredag i en månad körs inte pipelinen, eftersom den är schemalagd att bara köras på den femte fredagen. Om du vill köra utlösaren på den sista fredagen i månaden kan du använda -1 istället för 5 som värde för **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Körs var 15:e minut den sista fredagen i månaden. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Körs 05.15, 05.45, 17.15 och 17.45 den tredje onsdagen varje månad. |

## <a name="next-steps"></a>Nästa steg

- Detaljerad information om utlösare finns i [pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#trigger-execution).
- Lär dig hur du refererar till Utlös ande metadata i pipelinen finns i [referens utlösare metadata i pipeline-körningar](how-to-use-trigger-parameterization.md)
