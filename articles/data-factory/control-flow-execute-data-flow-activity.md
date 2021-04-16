---
title: Dataflöde aktivitet
description: Hur du kör dataflöden inifrån en datafabrikspipeline.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 04/14/2021
ms.openlocfilehash: b0d42b42ab44b51294833e40b7fa0174256c655a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517185"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Dataflöde aktivitet i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd aktivitetsaktiviteten Dataflöde transformera och flytta data via mappning av dataflöden. Om dataflöden är nytt för dig kan du se [Mappa Dataflöde översikt](concepts-data-flow-overview.md)

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Typegenskaper

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
dataflöde | Referensen till Dataflöde körs | DataFlowReference | Yes
integrationRuntime | Beräkningsmiljön som dataflödet körs på. Om inget anges används azure integration runtime för automatisk lösning. | IntegrationRuntimeReference | No
compute.coreCount | Antalet kärnor som används i Spark-klustret. Kan bara anges om den automatiskt lösa Azure Integration Runtime används | 8, 16, 32, 48, 80, 144, 272 | No
compute.computeType | Den typ av beräkning som används i Spark-klustret. Kan bara anges om den automatiskt lösa Azure Integration Runtime används | "General", "ComputeOptimized", "MemoryOptimized" | No
staging.linkedService | Om du använder en Azure Synapse Analytics eller mottagare anger du det lagringskonto som används för PolyBase-mellanlagring.<br/><br/>Om din Azure Storage har konfigurerats med VNet-tjänstslutpunkt måste du använda hanterad identitetsautentisering med "tillåt betrodd Microsoft-tjänst" aktiverat för [lagringskontot.](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)Se Effekten av att använda VNet-tjänstslutpunkter med Azure Storage . Lär dig även de konfigurationer som [krävs för Azure Blob](connector-azure-blob-storage.md#managed-identity) [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) respektive .<br/> | LinkedServiceReference | Endast om dataflödet läser eller skriver till en Azure Synapse Analytics
staging.folderPath | Om du använder en Azure Synapse Analytics eller mottagare, mappsökvägen i bloblagringskontot som används för PolyBase-mellanlagring | Sträng | Endast om dataflödet läser eller skriver till Azure Synapse Analytics
traceLevel | Ange loggningsnivå för körning av dataflödesaktivitet | Fine, Grov, Ingen | No

![Kör Dataflöde](media/data-flow/activity-data-flow.png "Kör Dataflöde")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Ändra dataflödesbearbetningens storlek dynamiskt vid körning

Egenskaperna Antal kärnor och Beräkningstyp kan ställas in dynamiskt för att justera efter storleken på dina inkommande källdata vid körning. Använd pipeline-aktiviteter som Lookup eller Get Metadata för att hitta storleken på datauppsättningens källdata. Använd sedan Lägg till dynamiskt innehåll i Dataflöde aktivitetsegenskaper.

![Dynamiska Dataflöde](media/data-flow/dyna1.png "Dynamiskt dataflöde")

[Här är en kort videokurs som förklarar den här tekniken](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Dataflöde integration runtime

Välj vilken Integration Runtime ska användas för din Dataflöde aktivitetskörning. Som standard Data Factory den automatiskt lösa Azure Integration Runtime med fyra arbetskärnor. Den här IR:en har en beräkningstyp för generell användning och körs i samma region som din fabrik. För operationaliserade pipelines rekommenderar vi starkt att du skapar dina egna Azure Integration Runtimes som definierar specifika regioner, beräkningstyp, antal kärnor och TTL för körning av dataflödesaktivitet.

En minsta beräkningstyp för Generell användning (beräkningsoptimerad rekommenderas inte för stora arbetsbelastningar) med en konfiguration på 8+8 (totalt 16 virtuella kärnor) och en 10-minutersrekommendation är minimirekommendationen för de flesta produktionsarbetsbelastningar. Genom att ange en liten TTL kan Azure IR underhålla ett varmt kluster som inte medför flera minuters starttid för ett kallt kluster. Du kan påskynda körningen av dina dataflöden ännu mer genom att välja "Snabb återanvändning" i Azure IR av dataflödeskonfigurationer. Mer information finns i [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> Det Integration Runtime valet i Dataflöde-aktiviteten gäller endast *för utlösta körningar* av din pipeline. Felsökning av din pipeline med dataflöden körs i det kluster som anges i felsökningssessionen.

### <a name="polybase"></a>PolyBase

Om du använder en Azure Synapse Analytics mottagare eller källa måste du välja en mellanlagringsplats för polybase-batchbelastningen. PolyBase tillåter batchinläsning i grupp i stället för att läsa in data rad för rad. PolyBase minskar drastiskt inläsningstiden till Azure Synapse Analytics.

## <a name="logging-level"></a>Loggningsnivå

Om du inte behöver alla pipelinekörningar av dina dataflödesaktiviteter för att helt logga alla utförliga telemetriloggar kan du välja att ställa in din loggningsnivå på "Basic" eller "None". När du kör dina dataflöden i "utförligt" läge (standard) begär du att ADF fullständigt loggar aktivitet på varje enskild partitionsnivå under datatransformering. Detta kan vara en dyr åtgärd, så att bara aktivera utförlig när felsökning kan förbättra det övergripande dataflödet och pipelineprestandan. Läget "Basic" loggar endast omvandlingens varaktigheter medan "Ingen" endast ger en sammanfattning av varaktigheter.

![Loggningsnivå](media/data-flow/logging.png "Ange loggningsnivå")

## <a name="sink-properties"></a>Egenskaper för mottagare

Med grupperingsfunktionen i dataflöden kan du både ange körningsordningen för dina mottagare samt gruppera mottagare tillsammans med samma gruppnummer. För att hantera grupper kan du be ADF att köra mottagare parallellt i samma grupp. Du kan också ange att mottagaresgruppen ska fortsätta även efter att en av mottagare påträffar ett fel.

Standardbeteendet för dataflödes mottagare är att köra varje mottagare sekventiellt, på ett seriellt sätt och att misslyckas dataflödet när ett fel påträffas i mottagaren. Dessutom är alla mottagare som standard samma grupp om du inte går in i dataflödesegenskaperna och anger olika prioriteter för mottagare.

![Egenskaper för mottagare](media/data-flow/sink-properties.png "Ange egenskaper för mottagare")

## <a name="parameterizing-data-flows"></a>Parametrisera dataflöden

### <a name="parameterized-datasets"></a>Parametriserade datauppsättningar

Om ditt dataflöde använder parametriserade datauppsättningar anger du parametervärdena på **fliken** Inställningar.

![Köra Dataflöde parametrar](media/data-flow/params.png "Parametrar")

### <a name="parameterized-data-flows"></a>Parametriserade dataflöden

Om ditt dataflöde är parametriserat anger du de dynamiska värdena för dataflödesparametrarna på **fliken** Parametrar. Du kan använda antingen uttrycksspråket för ADF-pipelinen eller dataflödesuttrycksspråket för att tilldela dynamiska eller literala parametervärden. Mer information finns i [Dataflöde Parametrar](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Parametriserade beräkningsegenskaper.

Du kan parameterisera antalet kärnor eller beräkningstypen om du använder den automatiska lösningskörningen för Azure Integration och anger värden för compute.coreCount och compute.computeType.

![Exempel Dataflöde kör parameter](media/data-flow/parameterize-compute.png "Parameterexempel")

## <a name="pipeline-debug-of-data-flow-activity"></a>Pipelinefelsökning av Dataflöde aktivitet

Om du vill köra en felsökningspipelinekörning med en Dataflöde-aktivitet måste du slå på felsökningsläget för dataflöden via skjutreglaget **Dataflöde skjutreglaget** för felsökning i det övre fältet. Med felsökningsläget kan du köra dataflödet mot ett aktivt Spark-kluster. Mer information finns i [Felsökningsläge.](concepts-data-flow-debug-mode.md)

![Felsökningsknapp](media/data-flow/debug-button-3.png "Felsökningsknapp")

Felsökningspipelinen körs mot det aktiva felsökningsklustret, inte integreringskörningsmiljön som anges Dataflöde aktivitetsinställningarna. Du kan välja beräkningsmiljön för felsökning när du startar felsökningsläget.

## <a name="monitoring-the-data-flow-activity"></a>Övervaka Dataflöde aktivitet

Aktiviteten Dataflöde har en särskild övervakningsupplevelse där du kan visa partitionering, fastid och information om datakällan. Öppna övervakningsfönstret via glasögonikonen under **Åtgärder**. Mer information finns i [Övervaka dataflöden.](concepts-data-flow-monitoring.md)

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Använda Dataflöde aktivitetsresultat i en efterföljande aktivitet

Dataflödesaktiviteten matar ut mått om antalet rader som skrivs till varje mottagare och rader som läses från varje källa. Dessa resultat returneras i avsnittet `output` i aktivitetskörningsresultatet. De mått som returneras har formatet json nedan.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Om du till exempel vill komma till antalet rader som skrivits till en mottagare med namnet "sink1" i en aktivitet med namnet "dataflowActivity" använder du `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

Om du vill hämta antalet rader som lästs från en källa med namnet "source1" som användes i den mottagaren använder du `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Om en mottagare har noll rader skrivna visas den inte i mått. Förekomsten kan verifieras med hjälp av `contains` funktionen . Kontrollerar till `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` exempel om några rader har skrivits till sink1.

## <a name="next-steps"></a>Nästa steg

Se kontrollflödesaktiviteter som stöds av Data Factory: 

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Köra pipelineaktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Tills-aktivitet](control-flow-until-activity.md)
