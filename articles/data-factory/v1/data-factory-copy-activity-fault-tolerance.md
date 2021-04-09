---
title: Lägg till fel tolerans i Azure Data Factory kopierings aktivitet genom att hoppa över inkompatibla rader
description: Lär dig hur du lägger till fel tolerans i Azure Data Factory kopierings aktivitet genom att hoppa över inkompatibla rader under kopiering
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 10e4bedae5b7c429152a3503fff2cb2769d66eb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377184"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Lägg till fel tolerans i kopierings aktiviteten genom att hoppa över inkompatibla rader

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-copy-activity-fault-tolerance.md)
> * [Version 2 (aktuell version)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [fel tolerans i kopierings aktiviteten för Data Factory](../copy-activity-fault-tolerance.md).

Azure Data Factory [kopierings aktivitet](data-factory-data-movement-activities.md) ger dig två sätt att hantera inkompatibla rader när du kopierar data mellan käll-och mottagar data lager:

- Du kan avbryta och avbryta kopierings aktiviteten när inkompatibla data påträffas (standard beteende).
- Du kan fortsätta att kopiera alla data genom att lägga till fel tolerans och hoppa över inkompatibla data rader. Dessutom kan du logga de inkompatibla raderna i Azure Blob Storage. Du kan sedan granska loggen för att lära dig orsaken till felet, korrigera data på data källan och försöka kopiera aktiviteten igen.

## <a name="supported-scenarios"></a>Scenarier som stöds
Kopierings aktiviteten stöder tre scenarier för att identifiera, hoppa över och logga inkompatibla data:

- **Inkompatibilitet mellan källdatatypen och den interna mottagartypen**

    Exempel: kopiera data från en CSV-fil i Blob Storage till en SQL-databas med en schema definition som innehåller tre kolumner av typen **int** . CSV-filrader som innehåller numeriska data, till exempel `123,456,789` kopieras till mottagar lagret. Men de rader som innehåller icke-numeriska värden, som `123,456,abc` identifieras som inkompatibla och hoppas över.

- **Antalet kolumner mellan källan och mottagaren matchar inte varandra**

    Exempel: kopiera data från en CSV-fil i Blob Storage till en SQL-databas med en schema definition som innehåller sex kolumner. CSV-filrader som innehåller sex kolumner kopieras till mottagar lagret. CSV-filrader som innehåller mer eller färre än sex kolumner identifieras som inkompatibla och hoppas över.

- **Primärnyckelsfel vid skrivning till SQL Server/Azure SQL Database/Azure Cosmos DB**

    Exempel: kopiera data från en SQL-Server till en SQL-databas. En primär nyckel definieras i Sink-SQL-databasen, men ingen sådan primär nyckel definieras i SQL Server-källan. Det går inte att kopiera de duplicerade raderna som finns i källan till mottagaren. Kopierings aktiviteten kopierar bara den första raden i data källan till mottagaren. Efterföljande käll rader som innehåller värdet för duplicerad primär nyckel identifieras som inkompatibla och hoppas över.

>[!NOTE]
>Den här funktionen gäller inte när kopierings aktiviteten har kon figurer ATS för att anropa extern data inläsnings mekanism, inklusive [Azure Synapse Analytics PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) eller [Amazon RedShift-inläsning](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). För att läsa in data i Azure Synapse Analytics med PolyBase använder du polybases inbyggda fel tolerans stöd genom att ange "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" i kopierings aktiviteten.

## <a name="configuration"></a>Konfiguration
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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Aktivera hoppa över inkompatibla rader under kopian eller inte. | Sant<br/>False (standard) | Inga |
| **redirectIncompatibleRowSettings** | En grupp egenskaper som kan anges när du vill logga de inkompatibla raderna. | &nbsp; | Inga |
| **linkedServiceName** | Den länkade tjänsten med Azure Storage för att lagra loggen som innehåller de överhoppade raderna. | Namnet på en länkad [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) -eller [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) -tjänst som refererar till den lagrings instans som du vill använda för att lagra logg filen. | Inga |
| **sökväg** | Sökvägen till logg filen som innehåller de överhoppade raderna. | Ange den Blob Storage-sökväg som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga |

## <a name="monitoring"></a>Övervakning
När körningen av kopierings aktiviteten är klar kan du se antalet rader som hoppats över i avsnittet övervakning:

![Övervakaren hoppade över inkompatibla rader](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Om du konfigurerar för att logga de inkompatibla raderna kan du hitta logg filen på den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` i logg filen kan du se de rader som hoppades över och rotor saken till inkompatibiliteten.

Både den ursprungliga informationen och motsvarande fel loggas i filen. Ett exempel på logg filens innehåll är följande:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du Azure Data Factory kopierings aktivitet i [Flytta data med hjälp av kopierings aktivitet](data-factory-data-movement-activities.md).
