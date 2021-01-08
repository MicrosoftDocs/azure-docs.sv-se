---
title: Blob Storage och Azure Data Lake Gen2-utdata från Azure Stream Analytics
description: I den här artikeln beskrivs Blob Storage och Azure Data Lake gen 2 som utdata för Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8f8fa524a69d0431fe98cdcd4d1f12addc576e86
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019592"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Blob Storage och Azure Data Lake Gen2-utdata från Azure Stream Analytics

Data Lake Storage Gen2 gör Azure Storage grunden för att skapa företags data sjöar på Azure. Data Lake Storage Gen2 är utformad från start-till-tjänst-flera petabyte med information medan hundratals Gigabit av data flödet används, så att du enkelt kan hantera enorma mängder data. En grundläggande del av Data Lake Storage Gen2 är att lägga till ett hierarkiskt namn område till Blob Storage.

Azure Blob Storage erbjuder en kostnads effektiv och skalbar lösning för att lagra stora mängder ostrukturerade data i molnet. En introduktion till Blob Storage och dess användning finns i [Ladda upp, ladda ned och lista blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="output-configuration"></a>Konfiguration av utdata

I följande tabell visas egenskaps namn och beskrivningar för att skapa en BLOB eller ADLS Gen2 utdata.

| Egenskapsnamn       | Beskrivning                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Utdataalias        | Ett eget namn som används i frågor för att dirigera frågeresultatet till blob-lagringen. |
| Lagringskonto     | Namnet på det lagrings konto där du ska skicka dina utdata.               |
| Lagrings konto nyckel | Den hemliga nyckeln som är kopplad till lagrings kontot.                              |
| Lagrings behållare   | En logisk gruppering för blobbar som lagras i Azure-Blob Service. När du laddar upp en blob till Blob Service måste du ange en behållare för denna blob. |
| Sökvägsmönster | Valfritt. Det fil Sök väg mönster som används för att skriva Blobbarna i den angivna behållaren. <br /><br /> I Sök vägs mönstret kan du välja att använda en eller flera instanser av variablerna datum och tid för att ange frekvensen som blobbar skrivs: <br /> {Date}, {Time} <br /><br />Du kan använda anpassad BLOB-partitionering för att ange ett anpassat {Field}-namn från dina händelse data till partitionering av blobbar. Fält namnet är alfanumeriskt och kan innehålla blank steg, bindestreck och under streck. Begränsningar för anpassade fält är följande: <ul><li>Fält namn är inte Skift läges känsliga. Tjänsten kan till exempel inte skilja mellan kolumn "ID" och kolumn "ID".</li><li>Kapslade fält är inte tillåtna. Använd i stället ett alias i jobb frågan för att "förenkla" fältet.</li><li>Det går inte att använda uttryck som fält namn.</li></ul> <br />Den här funktionen gör det möjligt att använda anpassade inställningar för datum/tid-format i sökvägen. Anpassade datum-och tids format måste anges en i taget, omgivna av nyckelordet {datetime: \<specifier> }. Tillåtna indata för \<specifier> är åååå, mm, M, DD, d, hh, H, mm, M, SS eller s. Nyckelordet {datetime: \<specifier> } kan användas flera gånger i sökvägen för att utforma anpassade datum-och tids inställningar. <br /><br />Exempel: <ul><li>Exempel 1: cluster1/logs/{date}/{time}</li><li>Exempel 2: cluster1/logs/{date}</li><li>Exempel 3: cluster1/{client_id}/{date}/{time}</li><li>Exempel 4: cluster1/{datetime: SS}/{myField} där frågan är: Välj data. fält som fält från indata.</li><li>Exempel 5: cluster1/Year = {datetime: åååå}/månad = {datetime: MM}/dag = {datetime: DD}</ul><br />Tidstämpeln för den skapade mappstrukturen följer UTC och inte lokal tid. [System. timestamp](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-time-handling#choose-the-best-starting-time) är den tid som används för all tidsbaserad partitionering.<br /><br />Fil namns användning använder följande konvention: <br /><br />{Path-prefix, mönster}/schemaHashcode_Guid_Number. extension<br /><br /> Detta GUID representerar den unika identifierare som tilldelats till en intern skrivare som skapas för skrivning till en BLOB-fil. Talet representerar index för BLOB-blocket. <br /><br /> Exempel på utdatafiler:<ul><li>Mina utdata/20170901/00/45434_gguid_1.csv</li>  <li>Mina utdata/20170901/01/45434_gguid_1.csv</li></ul> <br />Mer information om den här funktionen finns i [Azure Stream Analytics Anpassad partitionering av BLOB-utdata](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Datum format | Valfritt. Om token-token används i prefixets sökväg kan du välja det datum format där filerna är ordnade. Exempel: ÅÅÅÅ/MM/DD |
| Tids format | Valfritt. Om tidstoken används i prefixets sökväg anger du tids formatet som filerna är ordnade i. För närvarande är det enda värde som stöds är HH. |
| Händelseserialiseringsformat | Serialiserings format för utgående data. JSON, CSV, Avro och Parquet stöds. |
|Minsta antal rader |Antalet minsta rader per batch. För Parquet skapar varje batch en ny fil. Det aktuella standardvärdet är 2 000 rader och det tillåtna Max värdet är 10 000 rader.|
|Längsta tid |Maximal vänte tid per batch. Efter den här tiden kommer batchen att skrivas till utdata även om minimi kravet på rader är uppfyllt. Det aktuella standardvärdet är 1 minut och det högsta tillåtna värdet är 2 timmar. Om BLOB-utdata har Sök vägs mönster frekvens, kan vänte tiden inte vara högre än tidsintervallet för partitionen.|
| Kodning    | Om du använder CSV eller JSON-format måste du ange en kodning. UTF-8 är det enda kodnings format som stöds just nu. |
| Avgränsare   | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av CSV-data. Värden som stöds är komma, semikolon, blank steg, TABB och lodrätt fält. |
| Format      | Gäller endast för JSON-serialisering. **Raden separerad** anger att utdata är formaterade genom att ha varje JSON-objekt avgränsat med en ny rad. Om du väljer **rad avgränsad** läses ett objekt i taget av JSON. Själva hela innehållet skulle inte vara en giltig JSON. **Matris** anger att utdata är formaterad som en matris med JSON-objekt. Matrisen stängs bara när jobbet stoppas eller Stream Analytics har flyttats vidare till nästa tids period. I allmänhet är det bättre att använda line-separerad JSON, eftersom det inte kräver någon särskild hantering medan utdatafilen fortfarande skrivs till. |

## <a name="blob-output-files"></a>BLOB-utdatafiler

När du använder Blob Storage som utdata skapas en ny fil i blobben i följande fall:

* Om filen överskrider det maximala antalet tillåtna block (för närvarande 50 000). Du kan nå maximalt antal tillåtna block utan att nå maximalt tillåten BLOB-storlek. Om utgångs hastigheten till exempel är hög kan du se fler byte per block och fil storleken är större. Om utmatnings hastigheten är låg, har varje block färre data och fil storleken är mindre.
* Om det finns en schema ändring i utdata och utdataformatet kräver fast schema (CSV och Avro).
* Om ett jobb startas om, antingen externt av en användare som stoppar det och startar det eller internt för system underhåll eller fel återställning.
* Om frågan är helt partitionerad och en ny fil skapas för varje utdata-partition.
* Om användaren tar bort en fil eller en behållare för lagrings kontot.
* Om utdata är tidspartitionerad med hjälp av mönstret för sökvägar och en ny BLOB används när frågan flyttas till nästa timma.
* Om utdata har partitionerats av ett anpassat fält och en ny BLOB skapas per partitionsnyckel om den inte finns.
* Om utdata har partitionerats av ett anpassat fält där-kardinalitet för partitionsnyckel överskrider 8 000 och en ny BLOB skapas per partitionsnyckel.

## <a name="partitioning"></a>Partitionering

För partitionsnyckel använder du {date} och {Time} tokens från dina händelse fält i Sök vägs mönstret. Välj datum format, till exempel ÅÅÅÅ/MM/DD, DD/MM/ÅÅÅÅ eller MM-DD-ÅÅÅÅ. HH används för tids formatet. BLOB-utdata kan partitioneras med ett enda anpassat Event-attribut {FieldName} eller {datetime: \<specifier> }. Antalet utgående skrivare följer indata-partitionering för [fullständiga kan göras parallella-frågor](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata

För maximal meddelande storlek, se [Azure Storage gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Maximal blob block storlek är 4 MB och maximalt antal BLOB-bock är 50 000. |

## <a name="next-steps"></a>Nästa steg

* [Använd hanterad identitet (förhands granskning) för att autentisera ditt Azure Stream Analytics jobb till Azure Blob Storage](blob-output-managed-identity.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
