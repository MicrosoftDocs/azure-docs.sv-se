---
title: Övervaka kopieringsaktivitet
description: Lär dig mer om att övervaka körningen av kopierings aktiviteten i Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: 58860e404dff3030e51ff2977eaee081a15247f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388302"
---
# <a name="monitor-copy-activity"></a>Övervaka kopieringsaktivitet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du övervakar kopierings aktivitets körningen i Azure Data Factory. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="monitor-visually"></a>Övervaka visuellt

När du har skapat och publicerat en pipeline i Azure Data Factory kan du associera den med en utlösare eller manuellt starta en ad hoc-körning. Du kan övervaka alla dina pipelines körs internt i Azure Data Factory användar upplevelse. Lär dig mer om Azure Data Factory övervakning i allmänhet från [visuell övervakning Azure Data Factory](monitor-visually.md).

Om du vill övervaka körningen av kopierings aktiviteten går du till Data Factory- **redigeraren & övervaka** användar gränssnitt. På fliken **övervaka** ser du en lista över pipeliner som körs. Klicka på länken **pipelin namn** för att få åtkomst till listan över aktivitets körningar i pipeline-körningen.

![Övervaka pipelinekörning](./media/copy-activity-overview/monitor-pipeline-run.png)

På den här nivån kan du se länkar för att kopiera aktivitetens indata, utdata och fel (om kopierings aktiviteten körs Miss lyckas), samt statistik som varaktighet/status. Om du klickar på knappen **information** (glasögon) bredvid kopiera aktivitets namnet får du djupgående information om kopierings aktiviteten. 

![Körning av övervaka kopierings aktivitet](./media/copy-activity-overview/monitor-copy-activity-run.png)

I den här grafiska övervaknings vyn visar Azure Data Factory information om körningen av kopierings aktiviteten, inklusive lästa/skrivna volymer, antal filer/rader med data som har kopierats från källa till mottagare, data flöde, de konfigurationer som tillämpas för ditt kopierings scenario, steg för kopierings aktiviteten med motsvarande varaktighet och information. Se [den här tabellen](#monitor-programmatically) för varje möjlig mått och dess detaljerade beskrivning. 

När du kör en kopierings aktivitet i Data Factory visas i vissa fall **"prestanda justerings tips"**  längst upp i vyn kopiera aktivitets övervakning som du ser i exemplet. Tipsen visar vilken Flask hals som identifieras av ADF för den speciella kopierings körningen, tillsammans med förslag på vad som ska ändras för att förbättra kopieringen av data. Läs mer om [automatiska prestanda justerings tips](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Den nedre **körnings informationen och varaktigheten** beskriver de viktigaste stegen som kopierings aktiviteten går igenom, vilket är särskilt användbart för att felsöka kopierings prestandan. Flask halsen för din kopierings körning är den som har den längsta varaktigheten. Se [fel sökning av prestanda för kopierings aktivitet](copy-activity-performance-troubleshooting.md) på för vad varje steg representerar och detaljerad fel söknings vägledning.

**Exempel: kopiera från Amazon S3 till Azure Data Lake Storage Gen2**

![Övervaka körnings information för kopierings aktivitet](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Övervaka program mässigt

Information om körningen av kopierings aktiviteten och prestanda egenskaperna returneras också i avsnittet **Kopiera aktivitets körnings resultat**  >   , som används för att återge vyn UI-övervakning. Nedan visas en fullständig lista över egenskaper som kan returneras. Du ser bara de egenskaper som gäller för ditt kopierings scenario. Information om hur du övervakar aktivitets körningar programmatiskt i allmänhet finns i [program mässigt övervaka en Azure-datafabrik](monitor-programmatically.md).

| Egenskapsnamn  | Beskrivning | Enhet i utdata |
|:--- |:--- |:--- |
| dataRead | Den faktiska mängden data som läses från källan. | Int64-värde, i byte |
| dataWritten | Den faktiska monteringen av skrivna data som skrivs/allokeras till mottagaren. Storleken kan skilja sig från `dataRead` storlek, eftersom den relaterar hur varje data lager lagrar data. | Int64-värde, i byte |
| filesRead | Antalet filer som lästs från filbaserad källa. | Int64-värde (ingen enhet) |
| filesWritten | Antalet filer som skrivits/allokerats till filbaserad mottagare. | Int64-värde (ingen enhet) |
| filesSkipped | Antalet filer som hoppades över från den filbaserade källan. | Int64-värde (ingen enhet) |
| dataConsistencyVerification | Information om verifiering av data konsekvens där du kan se om dina kopierade data har verifierats för att vara konsekventa mellan käll-och mål lagret. Läs mer i [den här artikeln](copy-activity-data-consistency.md#monitoring). | Matris |
| sourcePeakConnections | Det högsta antalet samtidiga anslutningar som upprättats till käll data lagret under kopierings aktivitets körningen. | Int64-värde (ingen enhet) |
| sinkPeakConnections | Det högsta antalet samtidiga anslutningar som upprättats till mottagar data lagret under kopierings aktiviteten. | Int64-värde (ingen enhet) |
| rowsRead | Antalet rader som har lästs från källan. Måttet gäller inte när du kopierar filer som-är utan att parsa dem, till exempel när data uppsättningarna källa och mottagare är binärformat, eller annan format typ med identiska inställningar. | Int64-värde (ingen enhet) |
| rowsCopied | Antal rader som kopierats till Sink. Måttet gäller inte när du kopierar filer som-är utan att parsa dem, till exempel när data uppsättningarna källa och mottagare är binärformat, eller annan format typ med identiska inställningar.  | Int64-värde (ingen enhet) |
| rowsSkipped | Antal inkompatibla rader som hoppades över. Du kan aktivera inkompatibla rader som ska hoppas över genom att ange `enableSkipIncompatibleRow` true. | Int64-värde (ingen enhet) |
| copyDuration | Kopierings körningens längd. | Int32-värde, i sekunder |
| dataflöde | Hastighet för data överföring. | Flytt ALS nummer, i kbit/s |
| sourcePeakConnections | Det högsta antalet samtidiga anslutningar som upprättats till käll data lagret under kopierings aktivitets körningen. | Int32-värde (ingen enhet) |
| sinkPeakConnections| Det högsta antalet samtidiga anslutningar som upprättats till mottagar data lagret under kopierings aktiviteten.| Int32-värde (ingen enhet) |
| sqlDwPolyBase | Om PolyBase används när data kopieras till Azure Synapse Analytics. | Boolesk |
| redshiftUnload | Anger om borttagning används när data kopieras från RedShift. | Boolesk |
| hdfsDistcp | Om DistCp används när data kopieras från HDFS. | Boolesk |
| effectiveIntegrationRuntime | Integrerings körningen (IR) eller körningar som används för att starta aktivitets körningen i formatet `<IR name> (<region if it's Azure IR>)` . | Text (sträng) |
| usedDataIntegrationUnits | De effektiva enheterna för data integrering under kopiering. | Int32-värde |
| usedParallelCopies | Det effektiva parallelCopies under kopieringen. | Int32-värde |
| logPath | Sökväg till sessionen över överhoppade data i blob-lagringen. Se [fel tolerans](copy-activity-overview.md#fault-tolerance). | Text (sträng) |
| executionDetails | Mer information om de steg som kopierings aktiviteten går igenom och motsvarande steg, varaktigheter, konfigurationer och så vidare. Vi rekommenderar inte att du analyserar det här avsnittet eftersom det kan ändras. För att bättre förstå hur det hjälper dig att förstå och felsöka kopierings prestanda, se avsnittet [övervaka visuellt](#monitor-visually) objekt. | Matris |
| perfRecommendation | Kopiera optimerings tips för prestanda. Se [tips för prestanda justering](copy-activity-performance-troubleshooting.md#performance-tuning-tips) för mer information. | Matris |
| billingReference | Fakturerings förbrukningen för den aktuella körningen. Lär dig mer om att [övervaka förbrukningen på aktivitetens körnings nivå](plan-manage-costs.md#monitor-consumption-at-activity-run-level). | Objekt |
| durationInQueue | Köns varaktighet i sekunder innan kopierings aktiviteten börjar köras. | Objekt |

**Exempel:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

\-[Översikt över kopierings aktivitet](copy-activity-overview.md)

\- [Kopiera aktivitetsprestanda](copy-activity-performance.md)