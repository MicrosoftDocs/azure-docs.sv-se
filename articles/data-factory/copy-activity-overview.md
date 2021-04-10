---
title: Kopierings aktivitet i Azure Data Factory
description: Läs mer om kopierings aktiviteten i Azure Data Factory. Du kan använda den för att kopiera data från ett käll data lager som stöds till ett mottagar data lager som stöds.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: d52a0bba5fddaa865b8fad74b778ba7a3838b2a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387911"
---
# <a name="copy-activity-in-azure-data-factory"></a>Kopierings aktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory som du använder:"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [Aktuell version](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I Azure Data Factory kan du använda kopierings aktiviteten för att kopiera data mellan data lager som finns lokalt och i molnet. När du har kopierat data kan du använda andra aktiviteter för att ytterligare transformera och analysera den. Du kan också använda kopierings aktiviteten för att publicera omvandlings-och analys resultat för Business Intelligence (BI) och program förbrukning.

![Kopierings aktivitetens roll](media/copy-activity-overview/copy-activity.png)

Kopierings aktiviteten körs i en [integration runtime](concepts-integration-runtime.md). Du kan använda olika typer av integrerings körningar för olika data kopierings scenarier:

* När du kopierar data mellan två data lager som är offentligt tillgängliga via Internet från alla IP-adresser kan du använda Azure integration runtime för kopierings aktiviteten. Integrerings körningen är säker, tillförlitlig, skalbar och [globalt tillgänglig](concepts-integration-runtime.md#integration-runtime-location).
* När du kopierar data till och från data lager som finns lokalt eller i ett nätverk med åtkomst kontroll (till exempel ett virtuellt Azure-nätverk) måste du konfigurera en integration runtime med egen värd.

En integration runtime måste vara kopplad till varje käll-och mottagar data lager. Information om hur kopierings aktiviteten bestämmer vilken integrerings körning som ska användas finns i [bestämma vilken IR som ska användas](concepts-integration-runtime.md#determining-which-ir-to-use).

För att kopiera data från en källa till en mottagare utför tjänsten som kör kopierings aktiviteten följande steg:

1. Läser data från ett käll data lager.
2. Utför serialisering/deserialisering, komprimering/expandering, kolumn mappning och så vidare. Den utför dessa åtgärder baserat på konfigurationen av indata-DataSet, utdata-datauppsättning och kopierings aktivitet.
3. Skriver data till data lagret för mottagare/målet.

![Översikt över kopieringsaktivitet](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Data lager och format som stöds

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Filformat som stöds

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Du kan använda kopierings aktiviteten för att kopiera filer som är mellan två filbaserade data lager, i vilket fall data kopieras effektivt utan någon serialisering eller deserialisering. Dessutom kan du också tolka eller generera filer av ett angivet format, till exempel, du kan göra följande:

* Kopiera data från en SQL Server-databas och skriva till Azure Data Lake Storage Gen2 i Parquet-format.
* Kopiera filer i text format (CSV) från ett lokalt fil system och skriva till Azure Blob Storage i Avro-format.
* Kopiera zippade filer från ett lokalt fil system, expandera dem och skriv sedan de extraherade filerna till Azure Data Lake Storage Gen2.
* Kopiera data i formatet GZIP-komprimerad text (CSV) från Azure Blob Storage och skriv den till Azure SQL Database.
* Många fler aktiviteter som kräver serialisering/deserialisering eller komprimering/expandering.

## <a name="supported-regions"></a>Regioner som stöds

Tjänsten som gör det möjligt att kopiera aktiviteten är tillgänglig globalt i de regioner och geografiska områden som visas i [Azure integration runtime-platser](concepts-integration-runtime.md#integration-runtime-location). Den globalt tillgängliga topologin garanterar effektiv data förflyttning som vanligt vis förhindrar hopp över flera regioner. Se [produkter efter region](https://azure.microsoft.com/regions/#services) för att kontrol lera tillgängligheten för Data Factory och data förflyttning i en angiven region.

## <a name="configuration"></a>Konfiguration

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I allmänhet måste du för att kunna använda kopierings aktiviteten i Azure Data Factory:

1. **Skapa länkade tjänster för käll data lagret och data lagret för mottagare.** Du hittar listan över anslutningar som stöds i avsnittet [data lager och format som stöds](#supported-data-stores-and-formats) i den här artikeln. Information om konfigurations information och vilka egenskaper som stöds finns i avsnittet "länkade tjänst egenskaper" i kopplings artikeln. 
2. **Skapa data uppsättningar för källan och mottagaren.** Information om konfigurations information och vilka egenskaper som stöds hittar du i avsnitten "data uppsättnings egenskaper" i artiklarna källa och mottagar koppling.
3. **Skapa en pipeline med kopierings aktiviteten.** Nästa avsnitt innehåller ett exempel.

### <a name="syntax"></a>Syntax

Följande mall för en kopierings aktivitet innehåller en fullständig lista över vilka egenskaper som stöds. Ange de som passar ditt scenario.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Information om syntax

| Egenskap | Beskrivning | Obligatoriskt? |
|:--- |:--- |:--- |
| typ | För en kopierings aktivitet anger du till `Copy` | Ja |
| tillför | Ange den data uppsättning som du har skapat som pekar på käll data. Kopierings aktiviteten har endast stöd för en enda Indatatyp. | Ja |
| utdata | Ange den data uppsättning som du har skapat som pekar på mottagar data. Kopierings aktiviteten har endast stöd för en enda utdata. | Ja |
| typeProperties | Ange egenskaper för att konfigurera kopierings aktiviteten. | Ja |
| källa | Ange typ av kopierings källa och motsvarande egenskaper för att hämta data.<br/>Mer information finns i avsnittet "Kopiera aktivitets egenskaper" i den kopplings artikel som visas i [data lager och format som stöds](#supported-data-stores-and-formats). | Ja |
| sjönk | Ange typ av kopierings mottagare och motsvarande egenskaper för att skriva data.<br/>Mer information finns i avsnittet "Kopiera aktivitets egenskaper" i den kopplings artikel som visas i [data lager och format som stöds](#supported-data-stores-and-formats). | Ja |
| translator | Ange explicita kolumn mappningar från källan till Sink. Den här egenskapen gäller när standard kopierings beteendet inte uppfyller dina behov.<br/>Mer information finns i [schema mappning i kopierings aktivitet](copy-activity-schema-and-type-mapping.md). | Inga |
| dataIntegrationUnits | Ange ett mått som representerar den mängd potens som [Azure integration runtime](concepts-integration-runtime.md) använder för data kopiering. Dessa enheter kallades tidigare för moln data förflyttnings enheter (DMU). <br/>Mer information finns i [data integrerings enheter](copy-activity-performance-features.md#data-integration-units). | Inga |
| parallelCopies | Ange den parallellitet som du vill att kopierings aktiviteten ska använda vid inläsning av data från källan och skrivning av data till mottagaren.<br/>Mer information finns i [parallell kopiering](copy-activity-performance-features.md#parallel-copy). | Inga |
| bibehålla | Ange om du vill bevara metadata/ACL: er under data kopieringen. <br/>Mer information finns i [bevara metadata](copy-activity-preserve-metadata.md). |Inga |
| enableStaging<br/>stagingSettings | Ange om du vill mellanlagra interims data i Blob Storage i stället för att kopiera data direkt från källa till mottagare.<br/>Information om användbara scenarier och konfigurations information finns i [mellanlagrad kopia](copy-activity-performance-features.md#staged-copy). | Inga |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Välj hur du vill hantera inkompatibla rader när du kopierar data från källa till mottagare.<br/>Mer information finns i [fel tolerans](copy-activity-fault-tolerance.md). | Inga |

## <a name="monitoring"></a>Övervakning

Du kan övervaka kopierings aktiviteten som körs i Azure Data Factory både visuellt och program mässigt. Mer information finns i [övervaka kopierings aktivitet](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Stegvis kopia

Med Data Factory kan du stegvis kopiera delta data från ett käll data lager till ett data lager för mottagare. Mer information finns i [Självstudier: kopiera data stegvis](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering

I avsnittet [Kopiera aktivitets övervakning](copy-activity-monitoring.md) visas statistik för kopierings prestanda för varje aktivitets körning. I [guiden Kopiera aktivitet prestanda och skalbarhet](copy-activity-performance.md) beskrivs viktiga faktorer som påverkar prestanda för data förflyttning via kopierings aktiviteten i Azure Data Factory. Den visar även de prestanda värden som observerats under testningen och beskriver hur kopierings aktiviteten optimeras.

## <a name="resume-from-last-failed-run"></a>Återuppta från senast misslyckade körning

Kopierings aktiviteten har stöd för återuppta från senast misslyckade körning när du kopierar stor storlek på filer som är i binärformat mellan filbaserade butiker och väljer att bevara mappen/filhierarkin från källan till Sink, t. ex. för att migrera data från Amazon S3 till Azure Data Lake Storage Gen2. Det gäller följande filbaserade kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)och [SFTP](connector-sftp.md).

Du kan använda kopierings aktiviteten återuppta på följande två sätt:

- **Försök igen på aktivitets nivå:** Du kan ange antal nya försök för kopierings aktiviteten. Om den här kopierings aktiviteten Miss lyckas under körningen av pipelinen kommer nästa automatiska försök att starta från den senaste utvärderings punkten.
- **Kör om från misslyckad aktivitet:** När pipeline-körningen har slutförts kan du även utlösa en körning från den misslyckade aktiviteten i vyn ADF UI-övervakning eller program mässigt. Om den misslyckade aktiviteten är en kopierings aktivitet körs pipelinen inte bara igen från den här aktiviteten, utan även återupptas från föregående körnings fel punkt.

    ![Kopiera merit förteckning](media/copy-activity-overview/resume-copy.png)

Några saker att tänka på:

- Återuppta sker på filnivå. Om kopierings aktiviteten Miss lyckas när du kopierar en fil, kommer den här filen att kopieras på nytt i nästa körning.
- Ändra inte inställningarna för kopierings aktiviteten mellan kör om för att fortsätta att fungera korrekt.
- När du kopierar data från Amazon S3, Azure Blob, Azure Data Lake Storage Gen2 och Google Cloud Storage, kan kopierings aktiviteten återupptas från godtyckligt antal kopierade filer. Vid resten av filbaserade kopplingar som källa, stöder för närvarande kopierings aktiviteten återuppta från ett begränsat antal filer, vanligt vis på flera tusen och varierar beroende på längden på fil Sök vägarna. filer utöver det här antalet kopieras igen under återkörning.

För andra scenarier än binär fil kopiering startar kopierings aktiviteten Kör igen från början.

## <a name="preserve-metadata-along-with-data"></a>Bevara metadata tillsammans med data

När du kopierar data från källan till Sink, i scenarier som data Lake migration, kan du också välja att bevara metadata och åtkomst kontrol listor tillsammans med data med hjälp av kopierings aktiviteten. Se [bevara metadata](copy-activity-preserve-metadata.md) för mer information.

## <a name="schema-and-data-type-mapping"></a>Schema-och data typs mappning

Se [schema-och data typs mappning](copy-activity-schema-and-type-mapping.md) för information om hur kopierings aktiviteten mappar dina källdata till din mottagare.

## <a name="add-additional-columns-during-copy"></a>Lägg till ytterligare kolumner under kopieringen

Förutom att kopiera data från käll data lagret till Sink kan du också konfigurera för att lägga till ytterligare data kolumner som ska kopieras till mottagaren. Exempel:

- När du kopierar från filbaserad källa lagrar du den relativa fil Sök vägen som en ytterligare kolumn att spåra från vilken fil data kommer från.
- Duplicera den angivna käll kolumnen som en annan kolumn. 
- Lägg till en kolumn med ADF-uttryck för att koppla ADF-systemvariabler som pipeline-namn/pipeline-ID eller lagra andra dynamiska värden från den överordnade aktivitetens utdata.
- Lägg till en kolumn med ett statiskt värde för att uppfylla de behov du behöver.

Du kan hitta följande konfiguration på fliken Kopiera aktivitet källa. Du kan också mappa dessa ytterligare kolumner i Kopiera aktivitets [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping) som vanligt genom att använda definierade kolumn namn. 

![Lägg till ytterligare kolumner i kopierings aktiviteten](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Den här funktionen fungerar med den senaste data uppsättnings modellen. Om du inte ser det här alternativet från användar gränssnittet kan du försöka skapa en ny data uppsättning.

Om du vill konfigurera den program mässigt lägger du till `additionalColumns` egenskapen i din kopierings aktivitets Källa:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| additionalColumns | Lägg till ytterligare data kolumner som ska kopieras till Sink.<br><br>Varje objekt i `additionalColumns` matrisen representerar en extra kolumn. `name`Kolumnens namn definieras och `value` anger kolumnens data värde.<br><br>Tillåtna data värden är:<br>- **`$$FILEPATH`** – en reserverad variabel indikerar att lagra källfilernas relativa sökväg till den mappsökväg som anges i data uppsättningen. Tillämpa på filbaserad källa.<br>- **`$$COLUMN:<source_column_name>`** – ett reserverat variabel mönster indikerar att duplicera den angivna käll kolumnen som en annan kolumn<br>- **Uttryck**<br>- **Statiskt värde** | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>Skapa mottagar tabeller automatiskt

När du kopierar data till SQL Database/Azure Synapse Analytics, om mål tabellen inte finns, har kopierings aktiviteten automatiskt stöd för att skapa den baserat på källdata. Det är att hjälpa dig att snabbt komma igång med att läsa in data och utvärdera SQL Database/Azure Synapse Analytics. När data inmatningen har inhämtats kan du granska och justera tabell schema för mottagare efter dina behov.

Den här funktionen stöds när du kopierar data från en källa till följande Sink-data lager. Du hittar alternativet på *ADF Authoring UI* – > *kopierings aktivitets mottagare* – > *tabell alternativ* – > *Automatisk skapande av tabell* eller `tableOption` egenskap i aktiviteten Sink-nyttolast.

- [Azure SQL Database](connector-azure-sql-database.md)
- [Azure SQL Database Hanterad instans](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Skapa mottagar tabeller](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Feltolerans

Som standard slutar kopierings aktiviteten att kopiera data och returnerar ett problem när käll data rader är inkompatibla med mottagar data rader. För att kopieringen ska lyckas kan du konfigurera kopierings aktiviteten så att den hoppar över och loggar de inkompatibla raderna och bara kopierar de kompatibla data. Mer information finns i [fel tolerans för kopierings aktivitet](copy-activity-fault-tolerance.md) .

## <a name="data-consistency-verification"></a>Verifiering av datakonsekvens

När du flyttar data från käll-till mål lagret innehåller Azure Data Factory kopierings aktivitet ett alternativ som gör att du kan utföra ytterligare konsekvens kontroll för data för att säkerställa att data inte bara har kopierats från käll platsen till mål butiken, men också verifierade att de är konsekventa mellan käll-och mål lagret. När inkonsekventa filer har hittats under data flyttningen kan du antingen avbryta kopierings aktiviteten eller fortsätta att kopiera resten genom att aktivera fel tolerans inställningen för att hoppa över inkonsekventa filer. Du kan hämta de överhoppade fil namnen genom att aktivera logg inställningar för sessionen i kopierings aktiviteten. Mer information finns i [data konsekvens kontroll i kopierings aktiviteten](copy-activity-data-consistency.md) .

## <a name="session-log"></a>Sessionsbiljett
Du kan logga dina kopierade fil namn, vilket kan hjälpa dig att ytterligare se till att data inte bara har kopierats från käll platsen till mål butiken, utan även konsekvent mellan käll-och mål arkivet genom att granska sessionerna för kopierings aktiviteten. Mer information finns i [loggen för sessionen i kopierings aktiviteten](copy-activity-log.md) .

## <a name="next-steps"></a>Nästa steg
Se följande snabb starter, självstudier och exempel:

- [Kopiera data från en plats till en annan plats i samma Azure Blob Storage-konto](quickstart-create-data-factory-dot-net.md)
- [Kopiera data från Azure Blob Storage till Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Kopiera data från en SQL Server-databas till Azure](tutorial-hybrid-copy-powershell.md)
