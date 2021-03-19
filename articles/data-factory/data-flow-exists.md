---
title: Exists-transformering i mappnings data flödet
description: Sök efter befintliga rader med hjälp av exists-omvandlingen i Azure Data Factory mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82982640"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Exists-transformering i mappnings data flödet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Exists-omvandlingen är en omvandling för rad filtrering som kontrollerar om dina data finns i en annan källa eller Stream. Utdataströmmen innehåller alla rader i den vänstra strömmen som antingen finns eller inte finns i den högra strömmen. Exists-omvandlingen liknar ```SQL WHERE EXISTS``` och ```SQL WHERE NOT EXISTS``` .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Konfiguration

1. Välj vilken data ström du vill söka efter i list rutan till **höger ström** .
1. Ange om du vill att data ska finnas eller inte finns i inställningen för **exist-typ** .
1. Välj om du vill använda ett **anpassat uttryck** eller inte.
1. Välj vilka nyckel kolumner du vill jämföra som dina villkor. Som standard söker data flödet efter likhet mellan en kolumn i varje data ström. Om du vill jämföra via ett beräknat värde hovrar du över kolumn List rutan och väljer **beräknad kolumn**.

![Finns inställningar](media/data-flow/exists.png "finns 1")

### <a name="multiple-exists-conditions"></a>Det finns flera villkor

Om du vill jämföra flera kolumner från varje data ström, lägger du till ett nytt exists-villkor genom att klicka på plus ikonen bredvid en befintlig rad. Varje ytterligare villkor är anslutet av en "och"-sats. Jämförelse mellan två kolumner är samma som följande uttryck:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Anpassat uttryck

Om du vill skapa ett uttryck för en fri form som innehåller andra operatorer än "och" och "lika med", väljer du fältet **anpassat uttryck** . Ange ett anpassat uttryck via data flödets uttrycks verktyg genom att klicka på den blå rutan.

![Finns anpassade inställningar](media/data-flow/exists1.png "finns anpassad")

## <a name="broadcast-optimization"></a>Sändnings optimering

![Sändnings anslutning](media/data-flow/broadcast.png "Sändnings anslutning")

I kopplingar, sökningar och exists-omvandling, om en eller båda data strömmar får plats i arbetsnodens minne, kan du optimera prestandan genom att aktivera **sändning**. Som standard bestämmer Spark-motorn om en sida ska sändas automatiskt eller inte. Välj **fast** om du vill välja vilken sida som ska sändas manuellt.

Vi rekommenderar inte att du inaktiverar sändning via alternativet **inaktivera** om inte dina kopplingar körs i tids gräns fel.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Exempel

Exemplet nedan är en exists-omvandling med namnet `checkForChanges` som tar vänster ström `NameNorm2` och rätt ström `TypeConversions` .  Exists-villkoret är det uttryck `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` som returnerar true om både `EMPID` `Region` kolumnen och i varje data ström matchar. Som vi söker efter existerar `negate` är falskt. Vi aktiverar inga sändningar på fliken optimera så att det `broadcast` har värdet `'none'` .

I Data Factory UX ser den här omvandlingen ut som på bilden nedan:

![Finns exempel](media/data-flow/exists-script.png "Finns exempel")

Data flödes skriptet för den här omvandlingen är i kodfragmentet nedan:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Nästa steg

Liknande omvandlingar är [Lookup](data-flow-lookup.md) och [Join](data-flow-join.md).
