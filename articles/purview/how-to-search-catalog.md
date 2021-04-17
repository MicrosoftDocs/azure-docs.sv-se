---
title: 'Gör så här: sök Data Catalog'
description: Den här artikeln ger en översikt över hur du söker i en datakatalog.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564921"
---
# <a name="search-the-azure-purview-data-catalog"></a>Sök i Azure Purview-Data Catalog

Dataidentifiering är det första steget för en arbetsbelastning för dataanalys eller datastyrning för datakonsumenter. Dataidentifiering kan vara tidskrävande eftersom du kanske inte vet var du hittar de data du vill ha. Även efter att du har hittat data kan du ha svårt att avgöra om du kan lita på data och ta ett beroende på dem.

Målet med sökning i Azure Purview är att påskynda processen för dataidentifiering för att snabbt hitta de data som är viktiga. Den här artikeln beskriver hur du söker i Azure Purview-datakatalogen för att snabbt hitta de data du letar efter.

## <a name="search-the-catalog-for-assets"></a>Sök i katalogen efter tillgångar

I Azure Purview finns sökfältet längst upp i Purview Studio UX.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Skärmbild som visar platsen för Azure Purview-sökfältet" border="true":::

När du klickar på sökfältet kan du se din senaste sökhistorik och nyligen använda tillgångar. Välj "Visa alla" för att se alla nyligen visade tillgångar.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Skärmbild som visar sökfältet innan några nyckelord har angetts" border="true":::

Ange nyckelord som hjälper dig att identifiera din tillgång, till exempel dess namn, datatyp, klassificeringar och ordlistor. När du anger nyckelord som rör din önskade tillgång visar Azure Purview förslag på vad du ska söka efter och potentiella matchningar av tillgångar. Slutför sökningen genom att klicka på "Visa sökresultat" eller trycka på "Retur".

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Skärmbild som visar sökfältet som en användare anger i nyckelord" border="true":::

Sökresultatsidan visar en lista över tillgångar som matchar de nyckelord som anges i relevansordning. Det finns olika faktorer som kan påverka relevanspoängen för en tillgång. Du kan filtrera ned listan mer genom att välja specifika datalager, klassificeringar, kontakter, etiketter och ordlista som gäller för den tillgång som du letar efter.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Skärmbild som visar resultatet av en sökning" border="true":::

 Klicka på den önskade tillgången för att visa sidan med tillgångsinformation där du kan visa egenskaper som schema, härledning och tillgångsägare.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Skärmbild som visar sidan med tillgångsinformation" border="true":::

## <a name="search-query-syntax"></a>Sök frågesyntax

Alla sökfrågor består av nyckelord och operatorer. Ett nyckelord är något som skulle ingå i en tillgångs egenskaper. Potentiella nyckelord kan vara en klassificering, ordlista, tillgångsbeskrivning eller ett resursnamn. Ett nyckelord kan bara vara en del av den egenskap som du vill matcha mot. Använd nyckelord och operatorerna nedan för att se till att Azure Purview returnerar de tillgångar som du letar efter. 

Nedan visas de operatorer som kan användas för att skapa en sökfråga. Operatorer kan kombineras så många gånger som behövs i en enskild fråga.

| Operator | Definition | Exempel |
| -------- | ---------- | ------- |
| ELLER | Anger att en tillgång måste ha minst ett av de två nyckelorden. Måste vara i versaler. Ett tomt utrymme är också en OR-operator.  | Frågan `hive OR database` returnerar tillgångar som innehåller "hive", "databas" eller både och. |
| AND | Anger att en tillgång måste ha båda nyckelorden. Måste vara i versaler | Frågan `hive AND database` returnerar tillgångar som innehåller både "hive" och "database". |
| NOT | Anger att en tillgång inte får innehålla nyckelordet till höger om NOT-satsen | Frågan `hive NOT database` returnerar tillgångar som innehåller "hive", men inte "databas". |
| () | Grupperar en uppsättning nyckelord och operatorer tillsammans. När du kombinerar flera operatorer anger parenteser ordningen på åtgärderna. | Frågan `hive AND (database OR warehouse)` returnerar tillgångar som innehåller "hive" och antingen "database" eller "warehouse" eller båda. |
| "" | Anger exakt innehåll i en fras som frågan måste matcha mot. | Frågan `"hive database"` returnerar tillgångar som innehåller frasen "hive database" i deras egenskaper |
| * | Ett jokertecken som matchar ett till många tecken. Kan inte vara det första tecknet i ett nyckelord. | Frågan `dat*` returnerar tillgångar som har egenskaper som börjar med "dat", till exempel "data" eller "databas". |
| ? | Ett jokertecken som matchar ett enskilt tecken. Kan inte vara det första tecknet i ett nyckelord | Frågan returnerar tillgångar som har egenskaper som börjar med "dat" och är fyra bokstäver, till exempel `dat?` "datum" eller "data". |

> [!Note]
> Ange alltid booleska operatorer (**AND**, **OR**, **NOT**) i versaler. Annars spelar ärendet ingen roll, och det gör inte heller extra blanksteg.

## <a name="next-steps"></a>Nästa steg

- [Skapa, importera och exportera ordlista](how-to-create-import-export-glossary.md)
- [Hantera termmallar för företagsordlistor](how-to-manage-term-templates.md)
