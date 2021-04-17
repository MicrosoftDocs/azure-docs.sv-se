---
title: Förstå resursuppsättningar
description: Den här artikeln förklarar vad resursuppsättningar är och hur Azure Purview skapar dem.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 330a6e54ee88781f71c4a861051aab94f8eef81f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587909"
---
# <a name="understanding-resource-sets"></a>Förstå resursuppsättningar

Den här artikeln hjälper dig att förstå hur Azure Purview använder resursuppsättningar för att mappa datatillgångar till logiska resurser.
## <a name="background-info"></a>Bakgrundsinformation

Databehandlingssystem i stor skala lagrar vanligtvis en enda tabell på en disk som flera filer. Det här konceptet representeras i Azure Purview med hjälp av resursuppsättningar. En resursuppsättning är ett enda objekt i katalogen som representerar ett stort antal tillgångar i lagringen.

Anta till exempel att ditt Spark-kluster har bevarat en DataFrame i en Azure Data Lake Storage (ADLS) Gen2-datakälla. Även om tabellen i Spark ser ut som en enda logisk resurs finns det förmodligen tusentals Parquet-filer på disken, som var och en representerar en partition av det totala DataFrame-innehållet. IoT-data och webbloggdata har samma utmaning. Anta att du har en sensor som matar ut loggfiler flera gånger i sekunden. Det tar inte lång tid förrän du har hundratusentals loggfiler från den enskilda sensorn.

För att hantera utmaningen med att mappa ett stort antal datatillgångar till en enda logisk resurs använder Azure Purview resursuppsättningar.

## <a name="how-azure-purview-detects-resource-sets"></a>Så identifierar Azure Purview resursuppsättningar

Azure Purview stöder identifiering av resursuppsättningar i Azure Blob Storage, ADLS Gen1 och ADLS Gen2.

Azure Purview identifierar automatiskt resursuppsättningar vid genomsökning. Den här funktionen tittar på alla data som matas in via genomsökning och jämför dem med en uppsättning definierade mönster.

Anta till exempel att du söker igenom en datakälla vars URL är `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure Purview tittar på sökvägssegmenten och avgör om de matchar några inbyggda mönster. Den har inbyggda mönster för GUID, siffror, datumformat, lokaliseringskoder (till exempel en-us) och så vidare. I det här fallet matchar talmönstret *23*. Azure Purview förutsätter att den här filen är en del av en resursuppsättning med namnet `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Eller för en URL som matchar Azure Purview både lokaliseringsmönstret och nummermönstret, vilket `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` skapar en resursuppsättning med namnet `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Med den här strategin mappar Azure Purview följande resurser till samma `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` resursuppsättning:

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Filtyper som Azure Purview inte identifierar som resursuppsättningar

Purview försöker avsiktligt inte klassificera de flesta dokumentfiltyper som Word, Excel eller PDF som resursuppsättningar. Undantaget är CSV-format eftersom det är ett vanligt partitionerat filformat.

## <a name="how-azure-purview-scans-resource-sets"></a>Så genomsöker Azure Purview resursuppsättningar

När Azure Purview identifierar resurser som den tror är en del av en resursuppsättning växlar den från en fullständig genomsökning till en exempelgenomsökning. I en exempelgenomsökning öppnas bara en delmängd av de filer som den tror finns i resursuppsättningen. För varje fil som den öppnar använder den sitt schema och kör sina klassificerare. Azure Purview hittar sedan den senaste resursen bland de öppnade resurserna och använder resursens schema och klassificeringar i posten för hela resursuppsättningen i katalogen.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Vad Azure Purview lagrar om resursuppsättningar

Förutom enskilda scheman och klassificeringar lagrar Azure Purview följande information om resursuppsättningar:

- Data från den senaste partitionsresursen genomsöks i djup.
- Sammanställ information om de partitionsresurser som utgör resursuppsättningen.
- Ett partitionsantal som visar hur många partitionsresurser som hittades.
- Ett schemaantal som visar hur många unika scheman som hittades i exempeluppsättningen som den gjorde djupgenomsökningar på. Det här värdet är antingen ett tal mellan 1–5 eller för värden större än 5, 5+.
- En lista över partitionstyper när mer än en enda partitionstyp ingår i resursuppsättningen. En IoT-sensor kan till exempel mata ut både XML- och JSON-filer, även om båda är logiskt en del av samma resursuppsättning.

## <a name="built-in-resource-set-patterns"></a>Inbyggda resursuppsättningsmönster

Azure Purview stöder följande resursuppsättningsmönster. Dessa mönster kan visas som ett namn i en katalog eller som en del av ett filnamn.
### <a name="regex-based-patterns"></a>Regex-baserade mönster

| Mönsternamn | Visningsnamn | Beskrivning |
|--------------|--------------|-------------|
| GUID         | {GUID}       | En globalt unik identifierare enligt definitionen i [RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Antal       | {N}          | En eller flera siffror |
| Datum-/tidsformat | {Year} {Month} {Day} {N}     | Vi stöder olika datum-/tidsformat, men alla representeras med {Year}[avgränsare]{Month}[avgränsare]{Day} eller serie med {N}s. |
| 4byteHex     | {HEX}        | Ett 4-siffrigt HEX-tal. |
| Lokalisering | {LOC}        | En språktagg som definieras [i BCP 47](https://tools.ietf.org/html/bcp47), både - och _ namn stöds (till exempel en_ca och en-ca) |

### <a name="complex-patterns"></a>Komplexa mönster

| Mönsternamn | Visningsnamn | Beskrivning |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Filidentifierare för Spark-partition |
| Date(yyyy/mm/dd)InPath  | {Year}/{Month}/{Day} | Mönster för år/månad/dag som sträcker sig över flera mappar |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Så här visas resursuppsättningar i Azure Purview-katalogen

När Azure Purview matchar en grupp med tillgångar i en resursuppsättning försöker det extrahera den mest användbara informationen som ska användas som visningsnamn i katalogen. Några exempel på den namngivningskonvention som är standard: 

### <a name="example-1"></a>Exempel 1

Kvalificerat namn: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Visningsnamn: "namn på Spark-utdata"

### <a name="example-2"></a>Exempel 2

Kvalificerat namn: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Visningsnamn: "mina partitionerade data"

### <a name="example-3"></a>Exempel 3

Kvalificerat namn: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Visningsnamn: "data"

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Anpassa resursuppsättningsgruppering med hjälp av mönsterregler

om du genomsöker ett lagringskonto använder Azure Purview en uppsättning definierade mönster för att avgöra om en grupp av tillgångar är en resursuppsättning. I vissa fall kanske inte resursuppsättningsgruppering i Azure Purview återspeglar din datae egendom korrekt. Dessa problem kan vara:

- Felaktig märkning av en tillgång som en resursuppsättning
- Placera en tillgång i fel resursuppsättning
- Felaktig märkning av en tillgång som att den inte är en resursuppsättning

Om du vill anpassa eller åsidosätta hur Azure Purview identifierar vilka tillgångar som är grupperade som resursuppsättningar och hur de visas i katalogen kan du definiera mönsterregler i hanteringscentret. Stegvisa instruktioner och syntax finns i Mönsterregler [för resursuppsättning.](how-to-resource-set-pattern-rules.md)
## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure Purview finns [i Snabbstart: Skapa ett Azure Purview-konto.](create-catalog-portal.md)
