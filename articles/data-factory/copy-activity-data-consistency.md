---
title: Konsekvens kontroll av data i kopierings aktivitet
description: Lär dig mer om hur du aktiverar data konsekvens verifiering i kopierings aktivitet i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: 274250fecdf69b6a488c33ff25df3728a1c90af0
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556383"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Konsekvens kontroll av data i kopierings aktivitet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du flyttar data från käll-till mål lagret innehåller Azure Data Factory kopierings aktivitet ett alternativ som gör att du kan utföra ytterligare konsekvens kontroll för data för att säkerställa att data inte bara har kopierats från käll platsen till mål butiken, men också verifierade att de är konsekventa mellan käll-och mål lagret. När inkonsekventa filer har hittats under data flyttningen kan du antingen avbryta kopierings aktiviteten eller fortsätta att kopiera resten genom att aktivera fel tolerans inställningen för att hoppa över inkonsekventa filer. Du kan hämta de överhoppade fil namnen genom att aktivera logg inställningar för sessionen i kopierings aktiviteten. Du kan se [loggen för sessionen i kopierings aktiviteten](copy-activity-log.md) för mer information.

## <a name="supported-data-stores-and-scenarios"></a>Data lager och scenarier som stöds

-   Verifiering av data konsekvens stöds av alla anslutningar utom FTP, sFTP och HTTP. 
-   Verifiering av data konsekvens stöds inte i utvecklings kopierings scenariot.
-   När du kopierar binära filer är data konsekvens kontrollen endast tillgänglig när PreserveHierarchy-beteendet är inställt i kopierings aktivitet.
-   När du kopierar flera binärfiler i en enskild kopierings aktivitet med konsekvens kontroll av data aktive rad, har du möjlighet att antingen avbryta kopierings aktiviteten eller fortsätta att kopiera resten genom att aktivera fel tolerans inställningen för att hoppa över inkonsekventa filer. 
-   När du kopierar en tabell i en enskild kopierings aktivitet med data konsekvens kontroll aktive rad, Miss lyckas kopierings aktiviteten om antalet rader som läses från källan skiljer sig från antalet rader som kopieras till målet plus antalet inkompatibla rader som hoppades över.


## <a name="configuration"></a>Konfiguration
I följande exempel visas en JSON-definition för att aktivera data konsekvens kontroll i kopierings aktiviteten: 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
validateDataConsistency | Om du anger sant för den här egenskapen, kommer kopierings aktiviteten att kontrol lera fil storlek, lastModifiedDate och MD5-kontrollsumma för varje binär fil som kopieras från käll-till mål lagret när du kopierar binärfiler. När du kopierar tabell data kontrollerar kopierings aktiviteten det totala antalet rader efter att jobbet har slutförts för att se till att det totala antalet rader som läses från källan är detsamma som antalet rader som kopieras till målet plus antalet inkompatibla rader som hoppades över. Tänk på att kopierings prestandan kommer att påverkas genom att aktivera det här alternativet.  | Sant<br/>False (standard) | No
dataInconsistency | Ett nyckel/värde-par i skipErrorFile egenskaps uppsättning för att avgöra om du vill hoppa över inkonsekventa filer. <br/> – Sant: du vill kopiera resten genom att hoppa över inkonsekventa filer.<br/> -Falskt: du vill avbryta kopierings aktiviteten när en inkonsekvent fil har hittats.<br/>Tänk på att den här egenskapen endast är giltig när du kopierar binära filer och anger validateDataConsistency som true.  | Sant<br/>False (standard) | No
logSettings | En grupp egenskaper som kan anges för att aktivera sessionsinformation för att logga överhoppade filer. | | No
linkedServiceName | Den länkade tjänsten för [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) för att lagra loggfilerna för sessionen. | Namnen på en `AzureBlobStorage` `AzureBlobFS` länkad eller typ länkad tjänst, som refererar till den instans som du använder för att lagra loggfilerna. | No
path | Sökvägen till loggfilerna. | Ange den sökväg där du vill lagra loggfilerna. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | No

>[!NOTE]
>- När du kopierar binära filer från, eller till Azure Blob eller Azure Data Lake Storage Gen2, kan du använda automatisk verifiering av MD5-kontrollsumma på Block nivå för att använda [Azure Blob API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) och [Azure Data Lake Storage Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers). Om ContentMD5 på filer finns på Azure Blob eller Azure Data Lake Storage Gen2 som data källor, kontrollerar ADF fil nivåns MD5-kontroll för att kontrol lera filer även efter att filerna har lästs. När du har kopierat filer till Azure-blobben eller Azure Data Lake Storage Gen2 som data mål, skriver ADF ContentMD5 till Azure-blobben eller Azure Data Lake Storage Gen2 som kan förbrukas ytterligare av underordnade program för konsekvens kontroll av data.
>- ADF verifierar fil storleken vid kopiering av binärfiler mellan lagrings lager.

## <a name="monitoring"></a>Övervakning

### <a name="output-from-copy-activity"></a>Utdata från kopierings aktivitet
När kopierings aktiviteten körs fullständigt kan du se resultatet av data konsekvens verifieringen från utdata för varje kopierings aktivitet som körs:

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
Du kan se information om verifiering av data konsekvens från "dataConsistencyVerification Property".

Värde för **VerificationResult**: 
-   **Verifierad**: dina kopierade data har verifierats för att vara konsekventa mellan käll-och mål arkivet. 
-   **NotVerified**: dina kopierade data har inte verifierats vara konsekventa eftersom du inte har aktiverat validateDataConsistency i kopierings aktiviteten. 
-   **Stöds** inte: dina kopierade data har inte verifierats vara konsekventa eftersom konsekvens kontroll av data inte stöds för detta specifika kopierings par. 

Värde för **InconsistentData**: 
-   **Hittades**: ADF Copy-aktiviteten hittade inkonsekventa data. 
-   **Hoppade över**: ADF Copy-aktivitet har hittat och hoppat över inkonsekventa data. 
-   **Ingen**: ADF Copy-aktivitet har inte hittat inkonsekventa data. Det kan antingen bero på att dina data har verifierats vara konsekventa mellan käll-och mål lagret, eller att du har inaktiverat validateDataConsistency i kopierings aktiviteten. 

### <a name="session-log-from-copy-activity"></a>Sessionsbiljett från kopierings aktivitet

Om du konfigurerar för att logga den inkonsekventa filen kan du hitta logg filen från den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Loggfilerna är CSV-filerna. 

Schemat för en loggfil är följande:

Kolumn | Beskrivning 
-------- | -----------  
Timestamp | Tids stämplingen när ADF hoppar över inkonsekventa filer.
Nivå | Logg nivån för det här objektet. Den kommer att vara i varnings nivån för objektet som visar att filen hoppas över.
OperationName | Funktions sätt för automatisk kopierings aktivitet på varje fil. Det blir "FileSkip" för att ange den fil som ska hoppas över.
OperationItem | Fil namnet som ska hoppas över.
Meddelande | Mer information för att illustrera varför filer hoppas över.

Exemplet på en loggfil är följande: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
I logg filen ovan kan du se sample1.csv har hoppats över eftersom den inte kunde verifieras för att vara konsekvent mellan käll-och mål arkivet. Du kan få mer information om varför sample1.csv blir inkonsekvent beror på att den har ändrats av andra program när ADF Copy-aktivitet kopieras på samma gång. 



## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Fel tolerans för kopierings aktivitet](copy-activity-fault-tolerance.md)