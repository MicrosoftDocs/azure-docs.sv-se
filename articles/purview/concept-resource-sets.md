---
title: Förstå resursuppsättningar
description: I den här artikeln beskrivs vilka resurs uppsättningar som är och hur Azure-avdelningens kontroll skapar dem.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: cbf070dce056795ad8e4a5f3e4d609e7d36d631e
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200819"
---
# <a name="understanding-resource-sets"></a>Förstå resursuppsättningar

Den här artikeln hjälper dig att förstå hur Azure-avdelningens kontroll använder resurs uppsättningar för att mappa data till gångar till logiska resurser.
## <a name="background-info"></a>Bakgrunds information

Vid skalning av data bearbetnings system lagras vanligt vis en enda tabell på en disk som flera filer. Det här konceptet representeras i Azure avdelningens kontroll med hjälp av resurs uppsättningar. En resurs uppsättning är ett enda objekt i katalogen som representerar ett stort antal till gångar i lagringen.

Anta till exempel att ditt Spark-kluster har sparat en DataFrame i en Azure Data Lake Storage (ADLS) Gen2-datakälla. Även om tabellen i Spark ser ut som en enda logisk resurs, finns det troligen tusentals Parquet-filer på disken som representerar en partition av den totala DataFrame innehåll. IoT-data och webb logg data har samma utmaning. Anta att du har en sensor som utvärderar loggfiler flera gånger om en sekund. Det tar inte lång tid förrän du har hundratals tusentals loggfiler från den enskilda sensorn.

Azure avdelningens kontroll använder resurs uppsättningar för att åtgärda utmaningen med att mappa ett stort antal data till gångar till en enda logisk resurs.

## <a name="how-azure-purview-detects-resource-sets"></a>Hur Azure avdelningens kontroll identifierar resurs uppsättningar

Azure avdelningens kontroll stöder identifiering av resurs uppsättningar i Azure Blob Storage, ADLS Gen1 och ADLS Gen2.

Azure avdelningens kontroll identifierar automatiskt resurs uppsättningar vid genomsökning. Den här funktionen ser till att alla data som matas in via skanning och jämförs med en uppsättning definierade mönster.

Anta till exempel att du skannar en data källa vars URL är `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure-avdelningens kontroll tittar på Sök vägs segmenten och bestämmer om de matchar några inbyggda mönster. Den har inbyggda mönster för GUID, siffror, datum format, lokaliserings koder (till exempel en-US) och så vidare. I det här fallet matchar nummer mönstret *23*. Azure-avdelningens kontroll förutsätter att den här filen ingår i en resurs uppsättning med namnet `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Eller, för en URL som `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , matchar Azure avdelningens kontroll både lokaliserings mönstret och antalet mönster, vilket skapar en resurs uppsättning med namnet `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Med den här strategin mappar Azure avdelningens kontroll följande resurser till samma resurs uppsättning `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Filtyper som Azure-avdelningens kontroll inte kan identifiera som resurs uppsättningar

Avdelningens kontroll försöker avsiktligt inte klassificera de flesta dokument fil typer som Word, Excel eller PDF som resurs uppsättningar. Undantaget är CSV-format eftersom det är ett gemensamt partitionerat fil format.

## <a name="how-azure-purview-scans-resource-sets"></a>Hur Azure avdelningens kontroll genomsöker resurs uppsättningar

När Azure avdelningens kontroll identifierar resurser som den anser är en del av en resurs uppsättning växlar den från en fullständig genomsökning till en exempel genomsökning. I en exempels ökning öppnar den bara en delmängd av de filer som den tror är i resurs uppsättningen. För varje fil öppnas den med sitt schema och dess klassificerare körs. Azure-avdelningens kontroll hittar sedan den senaste resursen bland de öppna resurserna och använder resursens schema och klassificeringar i posten för hela resurs uppsättningen i katalogen.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Vad Azure avdelningens kontroll lagrar om resurs uppsättningar

Förutom Single schema och klassificeringar lagrar Azure avdelningens kontroll följande information om resurs uppsättningar:

- Data från den senaste partitionens resurs som den djupade genomsöks.
- Sammanställd information om de diskpartitioner som utgör resurs uppsättningen.
- Ett antal partitioner som visar hur många partitioner som hittas.
- Ett schema antal som visar hur många unika scheman som finns i den exempel uppsättning som det gjorde djupt sökningar på. Värdet är antingen ett tal mellan 1 – 5, eller värden som är större än 5, 5 +.
- En lista med partitionstyper när mer än en enda partitionstyp ingår i resurs uppsättningen. En IoT-sensor kan till exempel mata ut både XML-och JSON-filer, men båda är logiskt indelade i samma resurs uppsättning.

## <a name="built-in-resource-set-patterns"></a>Inbyggda resurs uppsättnings mönster

Azure avdelningens kontroll stöder följande resurs uppsättnings mönster. Dessa mönster kan visas som ett namn i en katalog eller som en del av ett fil namn.
### <a name="regex-based-patterns"></a>Regex-baserade mönster

| Mönster namn | Visningsnamn | Beskrivning |
|--------------|--------------|-------------|
| GUID         | LED       | En globalt unik identifierare som definieras i [RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Antal       | M          | En eller flera siffror |
| Datum-/tids format | År Månaderna Dagen M     | Vi har stöd för olika datum-och tids format, men alla visas med {Year} [avgränsare] {month} [avgränsare] {Day} eller serien {N} s. |
| 4ByteHex     | HEXADECIMAL        | Ett fyrsiffrigt HEXADECIMALt tal. |
| Lokalisering | Loc        | En språkkod som definieras i [BCP 47](https://tools.ietf.org/html/bcp47), både-och _-namn stöds (till exempel en_CA och en-ca) |

### <a name="complex-patterns"></a>Komplexa mönster

| Mönster namn | Visningsnamn | Beskrivning |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Fil identifierare för Spark-partition |
| Datum (åååå/mm/dd) insökväg  | {Year}/{Month}/{Day} | Mönster för år/månad/dag som sträcker sig över flera mappar |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Hur resurs uppsättningar visas i Azure avdelningens kontroll-katalogen

När Azure-avdelningens kontroll matchar en grupp med till gångar i en resurs uppsättning försöker den extrahera den mest användbara informationen som ska användas som ett visnings namn i katalogen. Några exempel på standard namngivnings konventionen som används: 

### <a name="example-1"></a>Exempel 1

Kvalificerat namn: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Visnings namn: "namn på Spark-utdata"

### <a name="example-2"></a>Exempel 2

Kvalificerat namn: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Visnings namn: "mina partitionerade data"

### <a name="example-3"></a>Exempel 3

Kvalificerat namn: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Visnings namn: "data"

## <a name="known-issues-with-resource-sets"></a>Kända problem med resurs uppsättningar

Även om resurs uppsättningarna fungerar bra i de flesta fall kan du stöta på följande problem, där Azure-avdelningens kontroll:

- Markerar en till gång felaktigt som en resurs uppsättning
- Placerar en till gång i fel resurs uppsättning
- Markerar en till gång felaktigt som en resurs uppsättning

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure avdelningens kontroll finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
