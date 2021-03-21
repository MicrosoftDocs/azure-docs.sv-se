---
title: Feltolerans för kopieringsaktivitet i Azure Data Factory
description: Lär dig mer om hur du lägger till fel tolerans för att kopiera aktiviteter i Azure Data Factory genom att hoppa över inkompatibla data.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fe1470661c006399ea176af1112d271524b2a1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390971"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Feltolerans för kopieringsaktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuell version](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du kopierar data från käll-till mål lagret tillhandahåller Azure Data Factory kopierings aktivitet en viss nivå av fel toleranser för att förhindra avbrott från fel i mitten av data flytten. Du kan till exempel kopiera miljon tals rader från käll-till mål lager där en primär nyckel har skapats i mål databasen, men käll databasen saknar definierade primära nycklar. När du kopierar duplicerade rader från källan till målet kommer du att trycka på överträdelsen av PK-överträdelsen på mål databasen. För tillfället erbjuder kopierings aktiviteten två sätt att hantera sådana fel: 
- Du kan avbryta kopierings aktiviteten när ett fel har påträffats. 
- Du kan fortsätta att kopiera resten genom att aktivera fel tolerans för att hoppa över inkompatibla data. Hoppa till exempel till den duplicerade raden i det här fallet. Dessutom kan du logga överhoppade data genom att aktivera sessionsinformation i kopierings aktiviteten. Du kan se [loggen för sessionen i kopierings aktiviteten](copy-activity-log.md) för mer information.

## <a name="copying-binary-files"></a>Kopiera binära filer 

ADF stöder följande fel tolerans scenarier vid kopiering av binära filer. Du kan välja att avbryta kopierings aktiviteten eller fortsätta att kopiera resten i följande scenarier:

1. De filer som ska kopieras av ADF tas bort av andra program samtidigt.
2. Vissa mappar eller filer tillåter inte att ADF används eftersom ACL: er för dessa filer eller mappar kräver högre behörighets nivå än anslutnings informationen som kon figurer ATS i ADF.
3. En eller flera filer har inte verifierats för att vara konsekventa mellan käll-och mål arkivet om du aktiverar verifierings inställningen för data konsekvens i ADF.

### <a name="configuration"></a>Konfiguration 
När du kopierar binära filer mellan lagrings lager kan du aktivera fel tolerans enligt följande: 

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
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
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
skipErrorFile | En grupp egenskaper som anger vilka typer av försök som du vill hoppa över under data flytten. | | Inga
fileMissing | Ett nyckel/värde-par i skipErrorFile egenskaps uppsättning för att avgöra om du vill hoppa över filer som tas bort av andra program när ADF kopieras i taget. <br/> – Sant: du vill kopiera resten genom att hoppa över de filer som tas bort av andra program. <br/> -Falskt: du vill avbryta kopierings aktiviteten när filer tas bort från käll arkivet i mitten av data förflyttningen. <br/>Tänk på att den här egenskapen har angetts till true som standard. | Sant (standard) <br/>Falskt | Inga
fileForbidden | Ett nyckel/värde-par i skipErrorFile egenskaps uppsättning för att avgöra om du vill hoppa över de specifika filerna, när ACL: er för dessa filer eller mappar kräver högre behörighets nivå än den anslutning som kon figurer ATS i ADF. <br/> – Sant: du vill kopiera resten genom att hoppa över filerna. <br/> -Falskt: du vill avbryta kopierings aktiviteten en gång när du har problem med att få behörighet till mappar eller filer. | Sant <br/>Falskt (standard) | Inga
dataInconsistency | Ett nyckel/värde-par i skipErrorFile egenskaps uppsättning för att avgöra om du vill hoppa över inkonsekventa data mellan käll-och mål arkivet. <br/> – Sant: du vill kopiera resten genom att hoppa över inkonsekventa data. <br/> -Falskt: du vill avbryta kopierings aktiviteten när inkonsekventa data har hittats. <br/>Tänk på att den här egenskapen endast är giltig när du anger validateDataConsistency som true. | Sant <br/>Falskt (standard) | Inga
invalidFileName | Ett nyckel/värde-par i skipErrorFile egenskaps uppsättning för att avgöra om du vill hoppa över de specifika filerna, om fil namnen är ogiltiga för mål arkivet. <br/> – Sant: du vill kopiera resten genom att hoppa över filerna med ogiltiga fil namn. <br/> -Falskt: du vill avbryta kopierings aktiviteten när några filer har ogiltiga fil namn. <br/>Tänk på att den här egenskapen fungerar när du kopierar binärfiler från lagrings lager till ADLS Gen2 eller kopierar binära filer från AWS S3 till enbart lagrings lager. | Sant <br/>Falskt (standard) | Inga
logSettings  | En grupp egenskaper som kan anges när du vill logga de överhoppade objekt namnen. | &nbsp; | Inga
linkedServiceName | Den länkade tjänsten för [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) för att lagra loggfilerna för sessionen. | Namnen på en `AzureBlobStorage` eller `AzureBlobFS` typ länkad tjänst, som refererar till den instans som du använder för att lagra logg filen. | Inga
path | Sökvägen till loggfilerna. | Ange den sökväg som du använder för att lagra loggfilerna. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga

> [!NOTE]
> Följande är förutsättningarna för att aktivera fel tolerans i kopierings aktivitet när du kopierar binära filer.
> För att hoppa över specifika filer när de tas bort från käll arkivet:
> - Käll data uppsättningen och mottagar data uppsättningen måste vara i binärformat och det går inte att ange komprimerings typen. 
> - De data lagrings typer som stöds är Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure-File Storage, fil system, FTP, SFTP, Amazon S3, Google Cloud Storage och HDFS.
> - Om du anger flera filer i käll data uppsättningen, som kan vara en mapp, jokertecken eller en lista med filer, kan kopierings aktiviteten hoppa över de specifika felfilerna. Om en enskild fil anges i käll data uppsättningen som ska kopieras till målet Miss kopie ras kopierings aktiviteten om något fel uppstår.
>
> För att hoppa över specifika filer när deras åtkomst är förbjuden från käll arkivet:
> - Käll data uppsättningen och mottagar data uppsättningen måste vara i binärformat och det går inte att ange komprimerings typen. 
> - De typer av data lager som stöds är Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, SFTP, Amazon S3 och HDFS.
> - Om du anger flera filer i käll data uppsättningen, som kan vara en mapp, jokertecken eller en lista med filer, kan kopierings aktiviteten hoppa över de specifika felfilerna. Om en enskild fil anges i käll data uppsättningen som ska kopieras till målet Miss kopie ras kopierings aktiviteten om något fel uppstår.
>
> För att hoppa över specifika filer när de verifieras vara inkonsekventa mellan käll-och mål lagret:
> - Du kan få mer information från data konsekvens dokument [här](./copy-activity-data-consistency.md).

### <a name="monitoring"></a>Övervakning 

#### <a name="output-from-copy-activity"></a>Utdata från kopierings aktivitet
Du kan hämta antalet filer som läses, skrivs och hoppas över via utdata från varje kopierings aktivitets körning. 

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

#### <a name="session-log-from-copy-activity"></a>Sessionsbiljett från kopierings aktivitet

Om du konfigurerar för att logga de överhoppade fil namnen kan du hitta logg filen från den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Loggfilerna måste vara CSV-filerna. Logg filens schema är följande:

Kolumn | Beskrivning 
-------- | -----------  
Timestamp | Tidsstämpeln när ADF hoppar över filen.
Nivå | Logg nivån för det här objektet. Den kommer att vara i varnings nivån för objektet som visar att filen hoppas över.
OperationName | Funktions sätt för automatisk kopierings aktivitet på varje fil. Det blir "FileSkip" för att ange den fil som ska hoppas över.
OperationItem | Fil namnen som ska hoppas över.
Meddelande | Mer information för att illustrera varför filen hoppas över.

Exemplet på en loggfil är följande: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
I ovanstående logg kan du se bigfile.csv har hoppats över på grund av att ett annat program har tagit bort den här filen när ADF kopierade den. Och 3_nopermission.txt har hoppats över eftersom ADF inte har tillåtelse att komma åt den på grund av behörighets problem.


## <a name="copying-tabular-data"></a>Kopiera tabell data 

### <a name="supported-scenarios"></a>Scenarier som stöds
Kopierings aktiviteten stöder tre scenarier för att identifiera, hoppa över och logga inkompatibla tabell data:

- **Inkompatibilitet mellan käll data typ och inbyggd typ av mottagare**. 

    Exempel: kopiera data från en CSV-fil i Blob Storage till en SQL-databas med en schema definition som innehåller tre kolumner av typen INT. CSV-filrader som innehåller numeriska data, till exempel 123 456 789, kopieras till mottagar lagret. Men raderna som innehåller icke-numeriska värden, till exempel 123 456, ABC identifieras som inkompatibla och hoppas över.

- **Antalet kolumner mellan källan och sinken matchar inte**.

    Exempel: kopiera data från en CSV-fil i Blob Storage till en SQL-databas med en schema definition som innehåller sex kolumner. CSV-filrader som innehåller sex kolumner kopieras till mottagar lagret. CSV-filrader som innehåller fler än sex kolumner identifieras som inkompatibla och hoppas över.

- **Fel vid primär nyckel vid skrivning till SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Exempel: kopiera data från en SQL-Server till en SQL-databas. En primär nyckel definieras i Sink-SQL-databasen, men ingen sådan primär nyckel definieras i SQL Server-källan. Det går inte att kopiera de duplicerade raderna som finns i källan till mottagaren. Kopierings aktiviteten kopierar bara den första raden i data källan till mottagaren. Efterföljande käll rader som innehåller värdet för duplicerad primär nyckel identifieras som inkompatibla och hoppas över.

>[!NOTE]
>- Om du vill läsa in data i Azure Synapse Analytics med PolyBase konfigurerar du polybases inställningar för ursprunglig fel tolerans genom att ange principer för nekande via "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" i kopierings aktiviteten. Du kan fortfarande aktivera omdirigering av PolyBase-inkompatibla rader till BLOB eller ADLS som normalt som visas nedan.
>- Den här funktionen gäller inte när kopierings aktiviteten har kon figurer ATS för att anropa [Amazon RedShift-inläsning](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Den här funktionen gäller inte när kopierings aktiviteten har kon figurer ATS för att anropa en [lagrad procedur från en SQL-mottagare](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink).

### <a name="configuration"></a>Konfiguration
I följande exempel visas en JSON-definition som hoppar över inkompatibla rader i kopierings aktiviteten:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
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
}, 
```

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Anger om inkompatibla rader ska hoppas över vid kopiering eller inte. | Sant<br/>False (standard) | Inga
logSettings | En grupp egenskaper som kan anges när du vill logga de inkompatibla raderna. | &nbsp; | Inga
linkedServiceName | Den länkade tjänsten för [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) för att lagra loggen som innehåller de överhoppade raderna. | Namnen på en `AzureBlobStorage` eller `AzureBlobFS` typ länkad tjänst, som refererar till den instans som du använder för att lagra logg filen. | Inga
path | Sökvägen till de loggfiler som innehåller de överhoppade raderna. | Ange den sökväg som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga

### <a name="monitor-skipped-rows"></a>Övervaka överhoppade rader
När körningen av kopierings aktiviteten är klar kan du se antalet rader som hoppades över i resultatet av kopierings aktiviteten:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Om du konfigurerar för att logga inkompatibla rader kan du hitta logg filen från den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Loggfilerna är CSV-filerna. Logg filens schema är följande:

Kolumn | Beskrivning 
-------- | -----------  
Timestamp | Tidsstämpeln när ADF hoppar över inkompatibla rader
Nivå | Logg nivån för det här objektet. Den kommer att finnas i varnings nivån om det här objektet visar de överhoppade raderna
OperationName | Funktions sätt för automatisk kopierings aktivitet på varje rad. Det blir "TabularRowSkip" för att ange att den särskilda inkompatibla raden har hoppats över
OperationItem | De överhoppade raderna från käll data lagret.
Meddelande | Mer information för att illustrera varför den här raden är inkompatibel.


Ett exempel på logg filens innehåll är följande:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

Från exempel logg filen ovan kan du se att en rad, fil1, data2, DATA3, har hoppats över på grund av typ konverterings problem från käll-till mål lagret. En annan rad, "DATA4, DATA5, data6", har hoppats över på grund av ett problem med PK-överträdelsen från käll-till mål lagret. 


## <a name="copying-tabular-data-legacy"></a>Kopiera tabell data (bakåtkompatibelt):

Följande är det äldre sättet att aktivera fel tolerans för att endast kopiera tabell data. Om du skapar en ny pipeline eller aktivitet, uppmanas du att [Starta härifrån i stället.](#copying-tabular-data)

### <a name="configuration"></a>Konfiguration
I följande exempel visas en JSON-definition som hoppar över inkompatibla rader i kopierings aktiviteten:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Anger om inkompatibla rader ska hoppas över vid kopiering eller inte. | Sant<br/>False (standard) | Inga
redirectIncompatibleRowSettings | En grupp egenskaper som kan anges när du vill logga de inkompatibla raderna. | &nbsp; | Inga
linkedServiceName | Den länkade tjänsten [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) för att lagra loggen som innehåller de överhoppade raderna. | Namnen på en `AzureStorage` eller `AzureDataLakeStore` typ länkad tjänst, som refererar till den instans som du vill använda för att lagra logg filen. | Inga
path | Sökvägen till logg filen som innehåller de överhoppade raderna. | Ange den sökväg som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga

### <a name="monitor-skipped-rows"></a>Övervaka överhoppade rader
När körningen av kopierings aktiviteten är klar kan du se antalet rader som hoppades över i resultatet av kopierings aktiviteten:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Om du konfigurerar för att logga inkompatibla rader kan du hitta logg filen på den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Loggfilerna kan bara vara CSV-filerna. De ursprungliga data som hoppas över kommer att loggas med kommatecken som kolumn avgränsare om det behövs. Vi lägger till två fler kolumner "ErrorCode" och "ErrorMessage" i ytterligare till de ursprungliga källdata i logg filen, där du kan se den bakomliggande orsaken till inkompatibiliteten. ErrorCode och ErrorMessage anges med dubbla citat tecken. 

Ett exempel på logg filens innehåll är följande:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda](copy-activity-performance.md)