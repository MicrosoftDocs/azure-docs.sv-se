---
title: Sessionshantering i kopierings aktivitet
description: Lär dig mer om hur du aktiverar sessionsinformation i kopierings aktiviteten i Azure Data Factory.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: 7cb00d62556babbd8e43e2fac2faa815a63943ed
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385276"
---
#  <a name="session-log-in-copy-activity"></a>Sessionshantering i kopierings aktivitet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Du kan logga dina kopierade fil namn i kopierings aktiviteten, vilket kan hjälpa dig att ytterligare se till att data inte bara har kopierats från käll platsen till mål butiken, utan även konsekvent mellan käll-och mål arkivet genom att granska de kopierade filerna i loggen för kopierings aktiviteter.  

När du aktiverar fel tolerans inställningen i kopierings aktiviteten för att hoppa över felaktiga data kan de överhoppade filerna och rader som hoppas över också loggas.  Du kan få mer information om [fel tolerans i kopierings aktiviteten](copy-activity-fault-tolerance.md). 

## <a name="configuration"></a>Konfiguration
I följande exempel visas en JSON-definition för att aktivera sessions logg i kopierings aktivitet: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
    },
    "validateDataConsistency": true,
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
}
```

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | När värdet är True har du möjlighet att logga kopierade filer, hoppa över filer eller överhoppade rader.  | Sant<br/>False (standard) | Inga
logLevel | "Info" loggar alla kopierade filer, överhoppade filer och rader som hoppats över. "Varning" kommer att logga överhoppade filer och endast ignorerade rader.  | Information<br/>Varning (standard) | Inga
enableReliableLogging | När det är sant kommer kopierings aktiviteten i tillförlitligt läge att tömma loggar direkt när varje fil kopieras till målet.  När du kopierar enorma mängder filer med tillförlitligt loggnings läge aktiverat i kopierings aktiviteten, bör du förvänta dig att kopierings data flödet skulle påverkas, eftersom dubbla Skriv åtgärder krävs för varje fil kopiering. En begäran är till mål lagringen och en annan begäran är till logg lagrings lagret.  Kopierings aktivitet i läget för bästa prestanda tömmer loggar med batch-poster inom en tids period, där kopierings data flödet blir mycket mindre påverkat. Loggning av fullständighet och tids linje är inte garanterat i det här läget eftersom det finns några möjligheter att den senaste batchen av logg händelser inte har tömts till logg filen när kopierings aktiviteten misslyckades. Nu visas några filer som kopieras till målet loggas inte.  | Sant<br/>False (standard) | Inga
logLocationSettings | En grupp egenskaper som kan användas för att ange platsen där sessionens loggar ska lagras. | | Inga
linkedServiceName | Den länkade tjänsten för [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) för att lagra loggfilerna för sessionen. | Namnen på en `AzureBlobStorage` `AzureBlobFS` länkad eller typ länkad tjänst, som refererar till den instans som du använder för att lagra loggfilerna. | Inga
path | Sökvägen till loggfilerna. | Ange den sökväg där du vill lagra loggfilerna. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga


## <a name="monitoring"></a>Övervakning

### <a name="output-from-copy-activity"></a>Utdata från kopierings aktivitet
När kopierings aktiviteten körs fullständigt kan du se sökvägen till loggfilerna från utdata för varje kopierings aktivitet. Du kan hitta loggfilerna från sökvägen: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Loggfilerna är CSV-filerna. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

> [!NOTE]
> När `enableCopyActivityLog` egenskapen är inställd på `Enabled` , genereras logg fils namnen som genereras av systemet.

### <a name="the-schema-of-the-log-file"></a>Logg filens schema

Följande är en logg fils schema.

Kolumn | Beskrivning 
-------- | -----------  
Timestamp | Tidsstämpeln när ADF läser, skriver eller hoppar över objektet.
Nivå | Logg nivån för det här objektet. Det kan vara "varning" eller "info".
OperationName | Funktions sätt för automatisk kopierings aktivitet för varje objekt. Det kan vara ' FileRead ', ' FileWrite ', ' FileSkip ' eller ' TabularRowSkip '.
OperationItem | Fil namnen eller överhoppade rader.
Meddelande | Mer information som visar om filen har lästs från käll arkivet eller skrivits till mål butiken. Det kan också vara orsaken till att filen eller raderna har hoppats över.

Följande är ett exempel på en loggfil. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
I logg filen ovan kan du se sample1.csv har hoppats över eftersom den inte kunde verifieras för att vara konsekvent mellan käll-och mål arkivet. Du kan få mer information om varför sample1.csv blir inkonsekvent beror på att den har ändrats av andra program när ADF Copy-aktivitet kopieras på samma gång. Du kan också se sample2.csv har kopierats från käll platsen till mål lagret.

Du kan analysera loggfilerna med flera analys motorer.  Det finns några exempel på hur du använder SQL-frågor för att analysera logg filen genom att importera CSV-loggfil till SQL Database där tabell namnet kan vara SessionLogDemo.  

-   Ge mig den kopierade fil listan. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Ge mig fil listan kopierad inom ett visst tidsintervall. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Ge mig en viss fil med den kopierade tiden och metadata. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Ge mig en lista över filer med metadata som kopierats inom ett tidsintervall. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Ge mig listan över överhoppade filer. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Ge mig orsaken till varför en viss fil hoppades över. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Ge mig listan över filer som hoppades över på grund av samma orsak: "BLOB-filen finns inte". 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Ge mig det fil namn som kräver längsta tid att kopiera.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Fel tolerans för kopierings aktivitet](copy-activity-fault-tolerance.md)
- [Data konsekvens för kopierings aktivitet](copy-activity-data-consistency.md)
