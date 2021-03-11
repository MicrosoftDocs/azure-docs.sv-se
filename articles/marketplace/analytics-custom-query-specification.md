---
title: Anpassad fråga-specifikation
description: Lär dig hur du använder anpassade frågor för att program mässigt extrahera data från Analytics-tabeller för dina erbjudanden på Microsofts kommersiella marknads plats.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584202"
---
# <a name="custom-query-specification"></a>Anpassad fråga-specifikation

Partner kan använda den här fråge specifikationen för att enkelt formulera anpassade frågor för att extrahera data från Analytics-tabeller. Frågorna kan bara användas för att välja de kolumner och mått som matchar ett visst kriterium. Språk specifikationen är kärnan i den data uppsättnings definition där en anpassad fråga kan skrivas.

## <a name="datasets"></a>Datauppsättningar

På samma sätt som vissa frågor körs mot en databas som har tabeller och kolumner, fungerar en anpassad fråga på data uppsättningar som har kolumner och mått. En fullständig lista över tillgängliga data uppsättningar för att utforma en fråga kan erhållas med hjälp av data uppsättnings-API: et.

Här är ett exempel på en data uppsättning som visas som en JSON.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Delar av en data uppsättning

- Ett data uppsättnings namn är som ett databas tabell namn. Till exempel ISVUsage. En data uppsättning har en lista med kolumner som kan väljas, till exempel MarketplaceSubscriptionId.
- En data uppsättning har också mått, som är till exempel agg regerings funktioner i en databas. Till exempel NormalizedUsage.
- Det finns fast tid i sträck över vilka data som kan exporteras.

### <a name="formulating-a-query-on-a-dataset"></a>Formulera en fråga på en data uppsättning

Detta är några exempel frågor som visar hur du extraherar olika typer av data.

| Söka i data | Beskrivning |
| ------------ | ------------- |
| **Välj** MarketplaceSubscriptionId, CustomerId **från** ISVUsage **TIMESPAN LAST_MONTH** | Den här frågan kommer att få varje unik `MarketplaceSubscriptionId` och dess motsvarande `CustomerId` under den senaste månaden. |
| **Välj** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **från** ISVUSAGE **order by** EstimatedExtendedChargeCC, **gräns** 10 | Den här frågan hämtar de 10 främsta prenumerationerna i fallande ordning efter antalet licenser som säljs under varje prenumeration. |
| **Välj** CustomerId, NormalizedUsage, RawUsage **från** ISVUSAGE **order by** NormalizedUsage **där** NormalizedUsage > 100000 **order by** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | Den här frågan kommer att hämta NormalizedUsage och RawUsage för alla kunder som har NormalizedUsage som är större än 100 000. |
| **Välj** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **från** ISVUsage **där** CustomerId in (' 2a31c234-1f4e-4c60-909e-76d234f93161 ', ' 80780748-3f9a-11eb-b378-0242ac130002 ') | Den här frågan hämtar `MarketplaceSubscriptionId` och intäkterna som genereras för varje månad med de två `CustomerId` värdena: `2a31c234-1f4e-4c60-909e-76d234f93161` och `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Fråga efter specifikation

I det här avsnittet beskrivs frågedefinitionen och strukturen.

### <a name="grammar-reference"></a>Grammatisk referens

Den här tabellen beskriver de symboler som används i frågor.

| Symbol | Innebörd |
| ------------ | ------------- |
| ? | Valfritt |
| * | Noll eller flera |
| + | En eller flera |
| &#124; | Eller/en av listan |
| | |

### <a name="query-definition"></a>Frågedefinition

I frågeuttrycket finns följande satser: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? och TimeSpan?.

- **SelectClause**: **Välj** ColumOrMetricName (, ColumOrMetricName) *
    - **ColumOrMetricName**: kolumner och mått som definierats i data uppsättningen
- **FromClause**: **från** datasetname spatialdata
    - **Datasetname spatialdata**: namnet på data uppsättningen som definierats i data uppsättningen
- **WhereClause**: **där** FilterCondition (**och** FilterCondition) *
    - **FilterCondition**: ColumOrMetricName-operatörens värde
        - **Operator**: = | > | < | >= | <= |! = | SOM | LIKNAR INTE | I | INTE I
        - **Värde**: nummer | StringLiteral | MultiNumberList | MultiStringList 
            - **Nummer**:-? [0-9] + (. [0-9] [0-9] *)?
            - **StringLiteral**: ' [a-za-Z0-9_] * '
            - **MultiNumberList**: (Number (, Number) *)
            - **MultiStringList**: (StringLiteral (, StringLiteral) *)
- **OrderClause**: **order by** OrderCondition (, OrderCondition) *
    - **OrderCondition**: ColumOrMetricName (**ASC**  |  **DESC**) *
    - **LimitClause**: **begränsa** [0-9] +
    - **TimeSpan**: **TimeSpan** (idag | IGÅR | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | GILTIGHET

### <a name="query-structure"></a>Frågans struktur

En rapport fråga består av flera delar:

- SELECT
- FROM
- WHERE
- ORDNA EFTER
- GRÄNSERNA
- INTERVALL

Varje del beskrivs nedan.

#### <a name="select"></a>SELECT

Den här delen av frågan anger vilka kolumner som ska exporteras. De kolumner som kan väljas är fälten som anges i `selectableColumns` och i `availableMetrics` avsnitt i en data uppsättning. De slutgiltiga exporterade raderna kommer alltid att innehålla distinkta värden i de markerade kolumnerna. Det finns till exempel inga duplicerade rader i den exporterade filen. Mått kommer att beräknas för varje distinkt kombination av de markerade kolumnerna.

**Exempel**:
- **Välj** `OfferName` , `NormalizedUsage`

#### <a name="from"></a>FROM

Den här delen av frågan anger den data uppsättning som data ska exporteras från. Data uppsättnings namnet som anges här måste vara ett giltigt data mängds namn som returneras av data uppsättnings-API: et.

**Exempel**:
- SOM `ISVUsage`
- SOM `ISVOrder`

#### <a name="where"></a>WHERE

Den här delen av frågan används för att ange filter villkor för data uppsättningen. Endast rader som matchar alla villkor i den här satsen kommer att finnas i den slutliga exporterade filen. Filter villkoret kan finnas på någon av kolumnerna som anges i `selectableColumns` och `availableMetrics` . Värdena som anges i filter villkoret kan vara en lista med tal eller en lista med strängar endast när operatorn är `IN` eller `NOT IN` . Värdena kan alltid anges som en litteral sträng och de kommer att konverteras till de ursprungliga kolumn typerna. Flera filter villkor måste åtskiljas med en `AND` åtgärd.

**Exempel**:

- MarketplaceSubscriptionId = ' 868368da-957d-4959-8992-3c12dc7e6260 '
- CustomerName **som** % Contosso%
- CustomerId **Not in** (1000, 1001, 1002)
- OrderQuantity = 100
- OrderQuantity = 100
    - MarketplaceSubscriptionId = ' 7b487ac0-ce12-b732-dcd6-91a1e4e74a50 ' och CustomerId = ' 0f8b7fa0-eb83-a183-1225-ca153ef807aa '

#### <a name="order-by"></a>ORDNA EFTER

Den här delen av frågan anger ordnings villkoren för de exporterade raderna. Kolumnerna där sortering kan definieras måste vara från `selectableColumns` och från `availableMetrics` data uppsättningen. Om ingen sorterings riktning anges används den som standard i `DESC` kolumnen. Sortering kan definieras för flera kolumner genom att avgränsa villkoren med kommatecken.

**Exempel**:

- **Sortera efter** NormalizedUsage **ASC**, ESTIMATEDEXTENDEDCHARGE (CC) **DESC**
- **Sortera efter** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>GRÄNSERNA

Den här delen av frågan anger antalet rader som ska exporteras. Talet som du anger måste vara ett positivt heltal som inte är noll.

#### <a name="timespan"></a>INTERVALL

Den här delen av frågan anger den tids period under vilken data måste exporteras. De möjliga värdena ska vara från `availableDateRanges` fältet i data uppsättnings definitionen.

### <a name="case-sensitivity-in-query-specification"></a>Skift läges känslighet i fråga om specifikation

Specifikationen är helt Skift läges okänslig. Fördefinierade nyckelord, kolumn namn och värden kan anges med versaler eller gemener.

## <a name="see-also"></a>Se även

- [Lista över system frågor](analytics-system-queries.md)
