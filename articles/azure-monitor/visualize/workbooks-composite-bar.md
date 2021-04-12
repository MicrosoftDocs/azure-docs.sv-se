---
title: Azure Monitor arbets bok sammansatt stapel åter givning
description: Lär dig mer om det visuella objektet för den sammanställda stapeln i Azure Monitor arbets bok.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 9a02299853174192c6963cbb382ceb1aa06ac088
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101728611"
---
# <a name="composite-bar-renderer"></a>Sammansatt stapel åter givning

Med hjälp av arbets boken kan du återge data med hjälp av sammansatt stapel, en stapel som består av flera staplar.

Bilden nedan visar det sammansatta fältet för databas status som representerar hur många servrar som är online, offline och i återställnings läge.

![Skärm bild av sammansatt stapel för databas status.](./media/workbooks-composite-bar/database-status.png)

Sammansatt bar-rendering stöds för rutnät, paneler och diagram visualiseringar.

## <a name="adding-composite-bar-renderer"></a>Lägger till sammansatt stapel åter givning

1. Ändra arbets boken till redigerings läge genom att välja *Redigera* verktygsfälts objekt.
2. Välj *Lägg till* och *Lägg till fråga*.
3. Ange *data källa* till "JSON" och *visualisering* till "Grid".
4. Lägg till följande JSON-data.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Kör fråga.
6. Välj **kolumn inställningar** för att öppna inställningarna.
7. Välj "total" från *kolumner* och välj "sammansatt stapel" för *kolumn åter givning*.
8. Ange följande inställningar under *Inställningar för sammansatt stapel*.

| Kolumn namn | Färg        |
|-------------|--------------|
| online      | Green        |
| återställa  | Gul       |
| anslutningen     | Röd (ljus) |

9. Lägg till etikett:`["online"] of ["total"] are healthy`
10. I kolumn inställningarna för online, offline och återställning kan du ställa in kolumn åter givning på "Hidden" (valfritt).
11. Välj *Etiketter* överst och uppdatera för kolumnen totalt som "databas status" (valfritt).
12. Välj vid **tillämpa**

Inställningarna för sammansatt stapel ser ut som skärm bilden nedan:

![Skärm bild av inställningarna för sammansatt stapel-kolumn med inställningar som beskrivs ovan.](./media/workbooks-composite-bar/composite-bar-settings.png)

Det sammansatta fältet med ovanstående inställningar:

![Skärm bild av sammansatt stapel.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Inställningar för sammansatt stapel

Välj kolumn namn och motsvarande färg för att återge kolumnen i den färgen som en del av det sammansatta fältet. Du kan infoga, ta bort och flytta rader uppåt och nedåt.

### <a name="label"></a>Etikett

Sammansatt stapel-etikett visas överst i det sammansatta fältet. Du kan använda en blandning av statisk text, kolumner och parameter.  Om etiketten är tom visas värdet för de aktuella kolumnerna som etikett. I föregående exempel, om vi lämnade etikett fältet svart, skulle värdet för total kolumner visas.

Referera till kolumner med `["columnName"]` .

Referera till parametrar med `{paramName}` .

Både kolumn namnet och parameter namnet är Skift läges känsliga. Du kan också skapa etiketter för en länk genom att välja "gör det här objektet som en länk" och sedan lägga till länk inställningar.

### <a name="aggregation"></a>Aggregering

Agg regeringar är användbara för träd/gruppera efter visualiseringar. Data för en kolumn för grupp raden bestäms av agg regerings uppsättningen för den kolumnen. Det finns tre typer av agg regeringar tillämpliga för sammansatta staplar: none, sum och Ärv.

Så här lägger du till Group by-inställningar:

1. I kolumn inställningar går du till den kolumn som du vill lägga till inställningar i.
2. I *träd/gruppera efter inställningar* under *träd typ* väljer du **Gruppera efter**
3. Välj det fält som du vill gruppera efter.

![Skärm bild av Group by-inställningar.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Inget

Ingen agg regering innebär att inga resultat visas för den kolumnen för grupp raderna.

![Skärm bild av sammansatt stapel med ingen agg regering.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Sum

Om agg regeringen anges som sum, kommer kolumnen i grupp raden att visa det sammansatta fältet genom att använda summan av de kolumner som används för att återge det. Etiketten kommer också att använda summan av de kolumner som refereras till.

I exemplet under online, offline och återställning av alla har max agg regerings uppsättningen och agg regeringen för total kolumnen värdet sum.

![Skärm bild av sammansatt stapel med summa agg regering.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Överföra

Om agg regering har angetts som Ärv, kommer kolumnen i grupp raden att visa det sammansatta fältet genom att använda agg regerings uppsättningen för de kolumner som används för att återge den. Kolumnerna som används i Label använder också den agg regering som anges av användaren. Om den aktuella kolumn åter givningen är sammansatt stapel och är refereed i etiketten (till exempel "total" i exemplet ovan) används sum som agg regering för den kolumnen.

I exemplet nedan är online, offline och återställning av alla har max agg regerings uppsättningen och agg regeringen för total-kolumnen är Ärv.

![Skärm bild av sammansatt stapel med Ärv agg regering.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Sortering

Vid rutnäts visualiseringar fungerar sorteringen av raderna för kolumnen med den sammansatta stapeln till att baseras på det värde som är summan av de kolumner som används för att återge den sammansatta stapeln dynamiskt. I föregående exempel är det värde som används för sortering summan av online-, återställnings-och offline-kolumner för den aktuella raden.

## <a name="tiles-visualization"></a>Visualisering av paneler

1. Välj **Lägg till** och *Lägg till fråga*.
2. Ändra data källan till JSON ange data från [föregående exempel](#adding-composite-bar-renderer).
3. Ändra visualiseringen till *paneler*.
4. Kör fråga.
5. Välj **panel inställningar**.
6. Välj *vänster* i panel fält.
7. Ange inställningarna nedan under *fält inställningar*.
    1. Använd kolumn: "Server".
    2. Kolumn åter givning: "text".
8. Välj *nedifrån* i panel fält.
9. Ange inställningarna nedan under *fält inställningar*.
    1. Använd kolumn: "total".
    2. Kolumn åter givning: "sammansatt stapel".
    3. Ange följande inställningar under "inställningar för sammansatt stapel".

    | Kolumn namn | Färg        |
    |-------------|--------------|
    | online      | Green        |
    | återställa  | Gul       |
    | anslutningen     | Röd (ljus) |

    4. Lägg till etikett: `["online"] of ["total"] are healthy` .
10. Välj **Använd**.

Inställningar för sammansatt stapel för paneler:

![Skärm bild av inställningarna för komposit List panel med inställningar som beskrivs ovan.](./media/workbooks-composite-bar/tiles-settings.png)

Vyn för sammansatt stapel för paneler med ovanstående inställningar ser ut så här:

![Skärm bild av sammansatta Stolp paneler.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Diagram visualisering

Följ anvisningarna nedan om du vill göra en sammansatt stapel åter givning för diagram visualisering (Skriv Hive-kluster).

1. Välj **Lägg till** och *Lägg till fråga*.
2. Ändra data källan till JSON ange data från [föregående exempel](#adding-composite-bar-renderer).
3. Ändra visualiseringen till *grafer*.
4. Kör fråga.
5. Välj **diagram inställningar**.
6. Välj *Center innehåll* i Node format-inställningar.
7. Ange inställningarna nedan under *fält inställningar*.
    1. Använd kolumn: "total".
    2. Kolumn åter givning: "sammansatt stapel".
    3. Ange följande inställningar under *Inställningar för sammansatt stapel*.

    |Kolumn namn  |     Färg    |
    |-------------|--------------|
    | online      | Green        |
    | återställa  | Gul       |
    | anslutningen     | Röd (ljus) |

   4. Lägg till etikett: `["online"] of ["total"] are healthy` .
9. Ange inställningarna nedan under *Inställningar för layout*.
    1. Diagram typ: **Hive-kluster**.
    2. Nod-ID Välj: "Server".
    3. Gruppera efter fält: "ingen".
    4. Node-storlek: 100.
    5. Marginal mellan sexhörningar: 5.
    6. Typ av färgning: **ingen**.
1. Välj **Använd**.
    
Inställningar för sammansatt stapel för grafer:

![Skärm bild av inställningar för sammansatta stapeldiagram med inställningar som beskrivs ovan.](./media/workbooks-composite-bar/graphs-settings.png)

Den sammansatta stapeln för graf med ovanstående inställningar ser ut så här:

![Skärm bild av sammansatta Stolp diagram med Hive-kluster.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Nästa steg

* [Distribuera](../visualize/workbooks-automate.md) arbets böcker med Azure Resource Manager.
* [Kontrol lera](./workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.