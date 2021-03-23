---
title: Schemaläggning och körning med Data Factory
description: Läs om schemaläggning och körnings aspekter av Azure Data Factory program modellen.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f613a14f84999fced888ae72de11328bc7c90811
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780012"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory schemaläggning och körning
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten kan du läsa artikeln [pipeline-körning och utlösare](../concepts-pipeline-execution-triggers.md) .

I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. Den här artikeln förutsätter att du förstår grunderna i Data Factory program modell koncept, inklusive aktivitet, pipeliner, länkade tjänster och data uppsättningar. Grundläggande begrepp för Azure Data Factory finns i följande artiklar:

* [Introduktion till Data Factory](data-factory-introduction.md)
* [Pipelines](data-factory-create-pipelines.md)
* [Datauppsättningar](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Start-och slut tider för pipelinen
En pipeline är endast aktiv mellan **Start** tid och **slut** tid. Den körs inte före start tiden eller efter slut tiden. Om pipelinen är pausad utförs den inte oavsett start-och slut tid. För att en pipeline ska kunna köras bör den inte pausas. Du hittar de här inställningarna (Start, End, Paused) i pipeline-definitionen: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Mer information om de här egenskaperna finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . 


## <a name="specify-schedule-for-an-activity"></a>Ange schema för en aktivitet
Det är inte den pipeline som körs. Det är aktiviteter i pipelinen som körs i den övergripande kontexten för pipelinen. Du kan ange ett återkommande schema för en aktivitet genom att använda **Scheduler** -avsnittet i AKTIVITETS-JSON. Du kan till exempel schemalägga en aktivitet så att den körs varje timme enligt följande:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Som du ser i följande diagram skapar ett schema för en aktivitet en serie rullande-fönster med i Start-och slut tiderna för pipelinen. Rullande Windows är en serie med icke-överlappande, sammanhängande tidsintervall i fast storlek. Dessa logiska rullande-fönster för en aktivitet kallas för **aktivitets fönster**.

![Exempel på Schemaläggaren för aktiviteter](media/data-factory-scheduling-and-execution/scheduler-example.png)

**Scheduler** -egenskapen för en aktivitet är valfri. Om du anger den här egenskapen måste den matcha den takt som du anger i definitionen av data uppsättningen för utdata för aktiviteten. Schemat styrs för närvarande av utdatamängd. Därför måste du skapa en data uppsättning för utdata även om aktiviteten inte genererar några utdata. 

## <a name="specify-schedule-for-a-dataset"></a>Ange schema för en data uppsättning
En aktivitet i en Data Factory pipeline kan ta noll eller fler data **uppsättningar** och skapa en eller flera data uppsättningar. För en aktivitet kan du ange takt som indata är tillgängliga för eller när utdata skapas med hjälp av avsnittet **tillgänglighet** i data uppsättnings definitionerna. 

**Frekvens** i avsnittet **tillgänglighet** anger tidsenheten. De tillåtna värdena för frekvens är: minut, timme, dag, vecka och månad. Egenskapen **Interval** i avsnittet tillgänglighet anger en multiplikator för frekvens. Exempel: om frekvensen är inställd på dag och intervall är inställt på 1 för en data uppsättning för utdata skapas utdata varje dag. Om du anger frekvensen som minut rekommenderar vi att du anger intervallet till högst 15. 

I följande exempel är indata tillgängliga varje timme och utdata skapas varje timme ( `"frequency": "Hour", "interval": 1` ). 

**Data mängd för indata:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Data uppsättning för utdata**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

För närvarande **driver data uppsättning av utdata schemat**. Med andra ord används det schema som angetts för data uppsättningen för utdata för att köra en aktivitet vid körning. Därför måste du skapa en data uppsättning för utdata även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. 

I följande pipeline-definition används **Scheduler** -egenskapen för att ange schemat för aktiviteten. Den här egenskapen är valfri. För närvarande måste schemat för aktiviteten matcha det schema som angetts för data uppsättningen för utdata.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

I det här exemplet körs aktiviteten varje timme mellan start-och slut tiderna för pipelinen. Utdatan skapas per timme för Windows i tre timmar (8 FM-9 AM, 9 FM-10 AM och 10 AM-11). 

Varje enhet av data som konsumeras eller skapas av en aktivitets körning kallas för en **data sektor**. Följande diagram visar ett exempel på en aktivitet med en indata-datauppsättning och en data uppsättning för utdata: 

![Tillgänglighets schema](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Diagrammet visar data sektorerna per timme för data uppsättningen för indata och utdata. Diagrammet visar tre ingående segment som är klara för bearbetning. 10-11 AM-aktivitet pågår, vilket producerar utdata för 10-11 AM-bit. 

Du kan komma åt det tidsintervall som är associerat med den aktuella sektorn i data uppsättnings-JSON med hjälp av variabler: [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) och [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). På samma sätt kan du komma åt tidsintervallet som är associerat med ett aktivitets fönster genom att använda WindowStart och WindowEnd. Schemat för en aktivitet måste matcha schemat för den utgående data uppsättningen för aktiviteten. Därför är värdena SliceStart och SliceEnd samma som WindowStart-och WindowEnd-värdena. Mer information om dessa variabler finns i artikeln [Data Factory Functions and system Variables](data-factory-functions-variables.md#data-factory-system-variables) .  

Du kan använda dessa variabler för olika syfte i din aktivitets-JSON. Du kan till exempel använda dem för att välja data från data uppsättningar för indata och utdata som representerar tids serie data (till exempel: 8 AM till 9 AM). I det här exemplet används också **WindowStart** och **WindowEnd** för att välja relevanta data för en aktivitets körning och kopiera den till en blob med lämplig **folderPath**. **FolderPath** är parameterstyrda att ha en separat mapp i varje timme.  

I föregående exempel är det schema som har angetts för data uppsättningar för indata och utdata samma (varje timme). Om data uppsättningen för indata-datauppsättningen för aktiviteten är tillgänglig med en annan frekvens, var 15: e minut, så körs den aktivitet som skapar den här data uppsättningen fortfarande en gång i timmen eftersom den utgående data uppsättningen är det som driver aktivitets schemat. Mer information finns i [modell data uppsättningar med olika frekvenser](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Tillgänglighet och principer för data uppsättning
Du har sett användningen av frekvens-och intervall egenskaper i avsnittet tillgänglighet för data uppsättnings definition. Det finns några andra egenskaper som påverkar schemaläggningen och körningen av en aktivitet. 

### <a name="dataset-availability"></a>Tillgänglighet för data uppsättning 
I följande tabell beskrivs de egenskaper som du kan använda i avsnittet **tillgänglighet** :

| Egenskap | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för data uppsättnings sektorns produktion.<br/><br/><b>Frekvens som stöds</b>: minut, timme, dag, vecka, månad |Ja |NA |
| interval |Anger en multiplikator för frekvens<br/><br/>"Frekvens x-intervall" anger hur ofta sektorn produceras.<br/><br/>Om du vill att data uppsättningen ska segmenteras per timme anger du <b>frekvens</b> till <b>timme</b>och <b>intervall</b> till <b>1</b>.<br/><br/><b>Obs!</b>om du anger frekvensen som minut rekommenderar vi att du anger intervallet till högst 15 |Ja |NA |
| stil |Anger om sektorn ska skapas i början/slutet av intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Om frekvensen är inställd på månad och format är inställt på EndOfInterval, produceras sektorn den sista dagen i månaden. Om formatet är inställt på StartOfInterval produceras sektorn den första dagen i månaden.<br/><br/>Om frekvens är inställt på dag och format är inställt på EndOfInterval skapas sektorn under den senaste timmen på dagen.<br/><br/>Om frekvensen är inställd på timme och formatet är inställt på EndOfInterval, skapas sektorn i slutet av timmen. För till exempel en sektor på en timme – 2-timmarsperiod skapas sektorn på 2 PM. |Inga |EndOfInterval |
| anchorDateTime |Definierar den absoluta position i tid som används av Scheduler för att beräkna data uppsättningens sektor gränser. <br/><br/><b>Obs!</b>om AnchorDateTime har datum delar som är mer detaljerade än frekvensen ignoreras fler detaljerade delar. <br/><br/>Om <b>intervallet</b> exempelvis är <b>per timme</b> (frekvens: timme och intervall: 1) och <b>AnchorDateTime</b> innehåller <b>minuter och sekunder</b>, ignoreras de <b>minuter och sekunder</b> som delar av AnchorDateTime. |Inga |01/01/0001 |
| offset |TimeSpan som startar och slutar på alla mängd uppsättnings segment flyttas. <br/><br/><b>Obs</b>: om både anchorDateTime och offset anges, är resultatet det kombinerade skiftet. |Inga |NA |

### <a name="offset-example"></a>förskjutnings exempel
Som standard börjar de dagliga `"frequency": "Day", "interval": 1` sektorerna () med 12 am UTC-tid (midnatt). Om du vill att start tiden ska vara 6 UTC-tid i stället anger du förskjutningen enligt följande kodfragment: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime-exempel
I följande exempel skapas data uppsättningen en gång var 23: e timme. Den första sektorn startar vid den tidpunkt som anges av anchorDateTime, som är inställd på `2017-04-19T08:00:00` (UTC-tid).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Exempel på offset/format
Följande data uppsättning är en månatlig data uppsättning och produceras den tredje i varje månad med 8:00 AM ( `3.08:00:00` ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Princip för data uppsättning
En data uppsättning kan ha en definierad validerings princip som anger hur data som genereras av en sektor körning kan val IDE ras innan de är klara för användning. I sådana fall, när sektorn har slutfört körningen, ändras statusen för utgående sektor till att **vänta** med en under status för **verifiering**. När sektorerna har verifierats ändras sektor statusen till **klar**. Om en data sektor har producerats men inte klarat verifieringen, bearbetas inte aktivitets körningar för underordnade segment som är beroende av den här sektorn. [Övervaka och hantera pipeliner](data-factory-monitor-manage-pipelines.md) täcker de olika tillstånden för data sektorer i Data Factory.

I **princip** avsnittet i data uppsättnings definitionen definieras villkoren eller villkoret som data uppsättnings sektorerna måste uppfylla. I följande tabell beskrivs de egenskaper som du kan använda i **princip** avsnittet:

| Principnamn | Beskrivning | Tillämpas på | Obligatorisk | Standardvärde |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Kontrollerar att data i en Azure- **BLOB** uppfyller minimi kraven för storlek (i megabyte). |Azure-blobb |Inga |NA |
| minimumRows | Verifierar att data i **Azure SQL Database** eller **Azure-tabellen** innehåller det lägsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabell</li></ul> |Inga |NA |

#### <a name="examples"></a>Exempel
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Mer information om dessa egenskaper och exempel finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . 

## <a name="activity-policies"></a>Aktivitetsprinciper
Principer påverkar körnings beteendet för en aktivitet, särskilt när en tabell sektor bearbetas. I följande tabell finns information.

| Egenskap | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| samtidighet |Integer <br/><br/>Max värde: 10 |1 |Antalet samtidiga körningar av aktiviteten.<br/><br/>Det avgör antalet parallella aktivitets körningar som kan ske på olika sektorer. Om en aktivitet till exempel behöver gå igenom en stor uppsättning tillgängliga data, desto högre samtidiga värde, desto snabbare data bearbetning. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Bestämmer ordningen på data sektorer som bearbetas.<br/><br/>Om du till exempel har 2 segment (en på 4pm och en annan på 17) och båda väntar på att köras. Om du ställer in executionPriorityOrder som NewestFirst bearbetas sektorn på 5 PM först. På samma sätt som om du anger att executionPriorityORder ska vara OldestFIrst bearbetas sektorn på 4 PM. |
| retry |Integer<br/><br/>Max värdet kan vara 10 |0 |Antal försök innan data bearbetningen för sektorn markeras som ett haveri. Aktivitets körningen för en data sektor görs igen till det angivna antalet försök. Återförsöket görs så snart som möjligt efter fel. |
| timeout |TimeSpan |00:00:00 |Tids gräns för aktiviteten. Exempel: 00:10:00 (betyder timeout 10 minuter)<br/><br/>Om ett värde inte anges eller är 0, är tids gränsen oändlig.<br/><br/>Om data bearbetnings tiden i en sektor överskrider tids gräns värdet avbryts det och systemet försöker försöka utföra bearbetningen igen. Antalet återförsök beror på egenskapen försök igen. När timeout inträffar anges statusen till stängningsåtgärd. |
| förskjutning |TimeSpan |00:00:00 |Ange fördröjningen innan data bearbetning av sektorn startar.<br/><br/>Körningen av en aktivitet för en data sektor startas efter fördröjningen efter den förväntade körnings tiden.<br/><br/>Exempel: 00:10:00 (betyder fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Max värde: 10 |1 |Antalet nya försök innan sektor körningen misslyckades.<br/><br/>longRetry-försök fördelas med longRetryInterval. Om du behöver ange en tid mellan återförsök ska du använda longRetry. Om både återförsök och longRetry har angetts innehåller varje longRetry-försök nya försök och det högsta antalet försök är att försöka igen * longRetry.<br/><br/>Om vi till exempel har följande inställningar i aktivitets principen:<br/>Nytt försök: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det bara finns en sektor att köra (status väntar) och aktivitets körningen Miss lyckas varje gång. Från början skulle det finnas tre körnings försök i följd. Efter varje försök skulle sektor statusen att försöka igen. Efter de första tre försöken är segmentets status LongRetry.<br/><br/>Efter en timme (det vill säga longRetryInteval värde), skulle det finnas en annan uppsättning av tre körnings försök i följd. Därefter skulle sektor statusen att Miss lyckas och inga fler återförsök skulle göras. Därför gjordes 6 försök.<br/><br/>Om körningen lyckas är segment statusen klar och inga fler återförsök görs.<br/><br/>longRetry kan användas i situationer där beroende data anländer till icke-deterministiska tider eller om den övergripande miljön är flaky under vilken data bearbetning sker. I sådana fall kan du göra nya försök en efter det att en annan kanske inte kan hjälpa dig och göra det när ett tidsintervall ger önskad utdata.<br/><br/>Varnings ord: Ange inte höga värden för longRetry eller longRetryInterval. Vanligt vis innebär högre värden andra problem med systemet. |
| longRetryInterval |TimeSpan |00:00:00 |Fördröjningen mellan långsamma försök |

Mer information finns i [pipeline](data-factory-create-pipelines.md) -artikeln. 

## <a name="parallel-processing-of-data-slices"></a>Parallell bearbetning av data sektorer
Du kan ange start datum för pipelinen tidigare. När du gör detta beräknar Data Factory automatiskt alla data sektorer i det förflutna och börjar bearbeta dem. Exempel: om du skapar en pipeline med start datumet 2017-04-01 och det aktuella datumet är 2017-04-10. Om takt för data uppsättningen för utdata är dagligen börjar Data Factory bearbeta alla sektorer från 2017-04-01 till 2017-04-09 omedelbart eftersom start datumet redan har passerat. Sektorn från 2017-04-10 bearbetas inte ännu eftersom värdet för Style-egenskapen i Availability-avsnittet är EndOfInterval som standard. Den äldsta sektorn bearbetas först eftersom standardvärdet för executionPriorityOrder är OldestFirst. En beskrivning av egenskapen Style finns i avsnittet [data uppsättnings tillgänglighet](#dataset-availability) . En beskrivning av avsnittet executionPriorityOrder finns i avsnittet [aktivitets principer](#activity-policies) . 

Du kan konfigurera backend-fyllda data sektorer som ska bearbetas parallellt genom att ange den **samtidiga** egenskapen i **princip** avsnittet för aktivitets-JSON. Den här egenskapen bestämmer antalet parallella aktivitets körningar som kan inträffa på olika sektorer. Standardvärdet för samtidighets egenskapen är 1. Därför bearbetas en sektor i taget som standard. Det maximala värdet är 10. När en pipeline måste gå igenom en stor uppsättning tillgängliga data, desto högre samtidiga värde, så påskyndas data bearbetningen. 

## <a name="rerun-a-failed-data-slice"></a>Köra en felaktig data sektor igen
När ett fel uppstår när en data sektor bearbetas kan du ta reda på varför bearbetningen av en sektor misslyckades med hjälp av Azure Portal blad eller övervaka och hantera appar. Mer information finns i [övervaka och hantera pipelines med Azure Portal blad](data-factory-monitor-manage-pipelines.md) eller [övervaknings-och hanterings program](data-factory-monitor-manage-app.md) .

Tänk på följande exempel, som visar två aktiviteter. Activity1 och aktivitet 2. Activity1 förbrukar en sektor av Dataset1 och skapar en sektor av Dataset2, som används som indata av Activity2 för att skapa en sektor av den slutliga data uppsättningen.

![Felaktig sektor](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagrammet visar att de tre senaste sektorerna har uppstått ett problem med att skapa 9-10 AM-sektorn för Dataset2. Data Factory spårar automatiskt beroende för Time Series-datauppsättningen. Därför startar den inte aktivitets körningen för den underordnade sektorn 9-10.

Med Data Factory övervaknings-och hanterings verktyg kan du öka detalj nivån i diagnostikloggar för den felande sektorn för att enkelt hitta rotor saken till problemet och åtgärda det. När du har åtgärdat problemet kan du enkelt starta aktivitets körningen för att producera den felande sektorn. Mer information om hur du kör om och förstår tillstånds över gångar för data sektorer finns i [övervaka och hantera pipelines med Azure Portal blad](data-factory-monitor-manage-pipelines.md) eller [övervaknings-och hanterings program](data-factory-monitor-manage-app.md).

När du har kört om 9-10 AM-sektionen för **Dataset2** startar Data Factory körningen för 9-10 am-Dependent-segmentet på den slutgiltiga data uppsättningen.

![Kör om misslyckad sektor](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Flera aktiviteter i en pipeline
Du kan fler än en aktivitet i en pipeline. Om du har flera aktiviteter i en pipeline och utdata för en aktivitet inte är indata för en annan aktivitet, kan aktiviteterna köras parallellt om indata-sektorerna för aktiviteterna är klara.

Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Aktiviteterna kan finnas i samma pipeline eller i olika pipeliner. Den andra aktiviteten körs bara när den första har slutförts.

Tänk till exempel följande fall där en pipeline har två aktiviteter:

1. Aktivitet a1 som kräver extern data uppsättning D1 och genererar utdata-datauppsättning D2.
2. Aktivitet a2 som kräver indata från data uppsättningen D2 och ger utdata från data uppsättningen D3.

I det här scenariot finns aktiviteterna a1 och a2 i samma pipeline. Aktivitets a1 körs när externa data är tillgängliga och den schemalagda tillgänglighets frekvensen har uppnåtts. Aktivitets a2 körs när de schemalagda sektorerna från D2 blir tillgängliga och den schemalagda tillgänglighets frekvensen nås. Om det finns ett fel i någon av sektorerna i data uppsättningen D2, körs inte a2 för denna sektor förrän det blir tillgängligt.

Diagramvyn med båda aktiviteterna i samma pipeline ser ut som i följande diagram:

![Länkning av aktiviteter i samma pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Som tidigare nämnts kan aktiviteterna finnas i olika pipeliner. I ett sådant scenario skulle diagramvyn se ut som i följande diagram:

![Länkning av aktiviteter i två pipeliner](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Se avsnittet Kopiera sekventiellt i bilagan för ett exempel.

## <a name="model-datasets-with-different-frequencies"></a>Modell data uppsättningar med olika frekvenser
I exemplen var frekvenserna för indata och utdata och fönstret aktivitets schema samma. Vissa scenarier kräver möjlighet att skapa utdata med en frekvens som skiljer sig från en eller flera indatas frekvenser. Data Factory stöder modellering av dessa scenarier.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Exempel 1: skapa en daglig utmatnings rapport för indata som är tillgängliga varje timme
Överväg ett scenario där du har indata från sensorer tillgängliga varje timme i Azure Blob Storage. Du vill skapa en daglig sammanställd rapport med statistik som medelvärde, maximum och minimum för dagen med [Data Factory Hive-aktivitet](data-factory-hive-activity.md).

Så här kan du modellera det här scenariot med Data Factory:

**Indata-datauppsättning**

De indatafilerna i timmen tas bort i mappen för den angivna dagen. Tillgängligheten för indatamängden anges i **timmen** (frekvens: timme, intervall: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Data uppsättning för utdata**

En utdatafil skapas varje dag i mappen Day. Tillgängligheten för utdata anges till **dag** (frekvens: dag och intervall: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Aktivitet: Hive-aktivitet i en pipeline**

Hive-skriptet tar emot lämplig *datetime* -information som parametrar som använder variabeln **WindowStart** , vilket visas i följande kodfragment. Hive-skriptet använder den här variabeln för att läsa in data från rätt mapp för dagen och köra agg regeringen för att generera utdata.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

Följande diagram visar scenariot från en data beroende punkt i vyn.

![Data beroende](./media/data-factory-scheduling-and-execution/data-dependency.png)

Utmatnings sektorn för varje dag är beroende av 24 tim segment från en indata-datauppsättning. Data Factory beräknar dessa beroenden automatiskt genom att räkna ut indata-sektorerna som infaller inom samma tids period som den utgående sektor som ska produceras. Om någon av de 24 indataportarna inte är tillgänglig väntar Data Factory tills Indataporten är klar innan den dagliga aktivitets körningen påbörjas.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Exempel 2: Ange beroende med uttryck och Data Factory funktioner
Vi tänker på ett annat scenario. Anta att du har en Hive-aktivitet som bearbetar två indata-datauppsättningar. En av dem har nya data dagligen, men en av dem får nya data varje vecka. Anta att du ville göra en koppling mellan de två inmatningarna och skapa utdata varje dag.

Den enkla metoden där Data Factory automatiskt avgränsade rätt indata-segment för bearbetning genom att justera till utgångs data sektorns tids period fungerar inte.

Du måste ange att för varje aktivitets körning ska Data Factory använda den senaste vecko data sektorn för vecko indata-datauppsättningen. Använd Azure Data Factory funktioner som du ser i följande kodfragment för att implementera det här beteendet.

**INPUT1: Azure-Blob**

Den första indatamängden är Azure-blobben som uppdateras dagligen.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: Azure-Blob**

Input2 är Azure-blobben som uppdateras varje vecka.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Utdata: Azure-Blob**

En utdatafil skapas varje dag i mappen för dagen. Tillgängligheten för output anges till **Day** (frekvens: dag, intervall: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Aktivitet: Hive-aktivitet i en pipeline**

Hive-aktiviteten tar två indata och skapar en utgående sektor varje dag. Du kan ange varje dag som indata-segment ska vara beroende av föregående veckas indata-sektor för veckovis indata enligt följande.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Se [Data Factory Functions och systemvariabler](data-factory-functions-variables.md) för en lista över funktioner och systemvariabler som Data Factory stöder.

## <a name="appendix"></a>Bilaga

### <a name="example-copy-sequentially"></a>Exempel: kopiera sekventiellt
Det går att köra flera kopierings åtgärder en efter varandra på ett sekventiellt/ordnat sätt. Du kan till exempel ha två kopierings aktiviteter i en pipeline (CopyActivity1 och CopyActivity2) med följande data uppsättningar för indata-utdata:   

CopyActivity1

Indata: data uppsättning. Utdata: Dataset2.

CopyActivity2

Inmatade: Dataset2.  Utdata: Dataset3.

CopyActivity2 skulle endast köras om CopyActivity1 har körts och Dataset2 är tillgänglig.

Här är exempel på pipeline-JSON:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observera att i exemplet anges utdata-datauppsättningen för den första kopierings aktiviteten (Dataset2) som indata för den andra aktiviteten. Den andra aktiviteten körs därför bara när data uppsättningen för utdata från den första aktiviteten är klar.  

I exemplet kan CopyActivity2 ha olika ingångar, t. ex. Dataset3, men du anger Dataset2 som indatamängd för CopyActivity2, så aktiviteten körs inte förrän CopyActivity1 har slutförts. Exempel:

CopyActivity1

Inmatade: Dataset1. Utdata: Dataset2.

CopyActivity2

Indata: Dataset3, Dataset2. Utdata: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observera att i exemplet anges två indata-datauppsättningar för den andra kopierings aktiviteten. När flera indata anges används endast den första data uppsättningen för att kopiera data, men andra data uppsättningar används som beroenden. CopyActivity2 skulle endast starta när följande villkor är uppfyllda:

* CopyActivity1 har slutförts och Dataset2 är tillgängligt. Den här data uppsättningen används inte vid kopiering av data till Dataset4. Det fungerar bara som ett schemaläggnings beroende för CopyActivity2.   
* Dataset3 är tillgänglig. Den här data uppsättningen representerar de data som kopieras till målet. 
