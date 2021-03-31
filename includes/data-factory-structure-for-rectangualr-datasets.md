---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67188013"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Ange struktur definition för rektangulära data uppsättningar
Struktur avsnittet i data uppsättnings-JSON är ett **valfritt** avsnitt för rektangulära tabeller (med rader & kolumner) och innehåller en uppsättning kolumner för tabellen. Du kommer att använda avsnittet struktur för att ange typ information för typ konverteringar eller för kolumn mappningar. I följande avsnitt beskrivs dessa funktioner i detalj. 

Varje kolumn innehåller följande egenskaper:

| Egenskap | Beskrivning | Obligatorisk |
| --- | --- | --- |
| name |Kolumnens namn. |Ja |
| typ |Kolumnens datatyp. Se avsnittet typ konverteringar nedan för mer information om när ska du ange typ information |Inga |
| substrat |.NET-baserad kultur som ska användas när typen har angetts och är .NET-typen datetime eller DateTimeOffset. Standardvärdet är "en-US". |Inga |
| format |Format strängen som ska användas när typen har angetts och är .NET-typen datetime eller DateTimeOffset. |Inga |

I följande exempel visas JSON-sektionen för en tabell som har tre kolumner UserID, Name och lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Använd följande rikt linjer när du ska inkludera "struktur information" och vad som ska tas med i avsnittet **struktur** .

* **För strukturerade data källor** som lagrar data schema och skriver information tillsammans med själva data (källor som SQL Server, Oracle, Azure Table osv.) bör du bara ange avsnittet "struktur" om du vill göra kolumn mappningen av vissa käll kolumner till vissa kolumner i mottagare och deras namn inte är desamma (se information i avsnittet kolumn mappning nedan). 
  
    Som nämnts ovan är typ informationen valfri i avsnittet "struktur". För strukturerade källor skriver du information finns redan som en del av data uppsättnings definitionen i data lagret, så du bör inte inkludera typ information när du inkluderar avsnittet "struktur".
* **För schema vid läsning av data källor (specifikt Azure-Blob)**  kan du välja att lagra data utan att lagra något schema eller ange information med data. För dessa typer av data källor bör du inkludera "struktur" i följande två fall:
  * Du vill göra en kolumn mappning.
  * När data uppsättningen är en källa i en kopierings aktivitet kan du ange typ information i "struktur" och Data Factory ska använda den här typen av information för omvandling till interna typer för mottagaren. Mer information finns i artikeln [Flytta data till och från Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) .

### <a name="supported-net-based-types"></a>Tillåtna. NET-baserade typer
Data Factory stöder följande CLS-kompatibla .NET-baserade typ värden för att tillhandahålla typ information i "struktur" för schema vid läsning av data källor som Azure blob.

* Int16
* Int32 
* Int64
* Enkel
* Double
* Decimal
* Byte []
* Bool
* Sträng 
* GUID
* Datumtid
* DateTimeOffset
* Tidsintervall 

För datetime-& DateTimeOffset kan du också ange strängen "kultur" & "format" för att under lätta parsningen av din anpassade datetime-sträng. Se exempel för typ konvertering nedan.

