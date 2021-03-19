---
title: 'Gör så här: Sök i Data Catalog'
description: Den här artikeln ger en översikt över hur du söker i en data katalog.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 7799266bf9cece1ed789d6fab64ec970a09fbfcb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588481"
---
# <a name="search-the-azure-purview-data-catalog"></a>Sök i Azure avdelningens kontroll-Data Catalog

Data identifiering är det första steget i arbets belastningen data analys eller data styrning för data konsumenter. Det kan ta lång tid att identifiera data eftersom du kanske inte vet var du hittar de data som du vill ha. Trots att du har hittat data kan du vara osäker på om du kan lita på data och ta ett beroende på det.

Målet med sökning i Azure avdelningens kontroll är att påskynda processen med data identifiering för att snabbt hitta de data som är viktiga. Den här artikeln beskriver hur du söker i Azure avdelningens kontroll Data Catalog för att snabbt hitta de data du söker efter.

## <a name="search-the-catalog-for-assets"></a>Sök i katalogen för till gångar

I Azure avdelningens kontroll finns Sök fältet överst i avdelningens kontroll Studio-UX.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Skärm bild som visar platsen för Sök fältet i Azure-avdelningens kontroll" border="true":::

När du klickar på Sök fältet kan du se din senaste Sök historik och nyligen använda till gångar. Välj "Visa alla" om du vill se alla nyligen visade till gångar.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Skärm bild som visar Sök fältet innan eventuella nyckelord har angetts" border="true":::

Ange i nyckelord som hjälper dig att identifiera din till gång, till exempel namn, datatyp, klassificeringar och ord listans villkor. När du anger i nyckelord som rör din önskade till gång, visar Azure avdelningens kontroll förslag på vad du kan söka efter och potentiella till gångar matchar. Slutför sökningen genom att klicka på "Visa Sök resultat" eller trycka på "retur".

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Skärm bild som visar Sök fältet när en användare går in i nyckelord" border="true":::

Sidan Sök resultat visar en lista över till gångar som matchar de nyckelord som anges i prioritetsordning. Det finns olika faktorer som kan påverka resultatet av en till gångs relevans. Du kan filtrera ned listan mer genom att välja vissa data lager, klassificeringar, kontakter, etiketter och ord listor som gäller för den till gång du söker.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Skärm bild som visar resultatet av en sökning" border="true":::

 Klicka på din önskade till gång för att visa sidan till gångs information där du kan visa egenskaper, till exempel schema, härkomst och till gångs ägare.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Skärm bild som visar sidan till gångs information" border="true":::

## <a name="search-query-syntax"></a>Sök frågans syntax

Alla Sök frågor består av nyckelord och operatorer. Ett nyckelord är något som skulle vara en del av en till gångs egenskaper. Potentiella nyckelord kan vara klassificering, ord lista, till gångs beskrivning eller ett till gångs namn. Ett nyckelord kan bara vara en del av den egenskap som du vill matcha till. Använd nyckelord och operatorerna i listan nedan för att se till att Azure-avdelningens kontroll returnerar de till gångar som du letar efter. 

Nedan visas de operatorer som kan användas för att skapa en Sök fråga. Operatörer kan kombineras så många gånger som det behövs i en enskild fråga.

| Operator | Definition | Exempel |
| -------- | ---------- | ------- |
| ELLER | Anger att en till gång måste ha minst ett av de två nyckelorden. Måste vara i versaler. Ett tomt utrymme är också en OR-operator.  | Frågan `hive OR database` returnerar till gångar som innehåller "Hive" eller "databas" eller både och. |
| AND | Anger att en till gång måste ha båda nyckelorden. Måste vara i versaler | Frågan `hive AND database` returnerar till gångar som innehåller både "Hive" och "Database". |
| NOT | Anger att en till gång inte får innehålla nyckelordet till höger om NOT-satsen | Frågan `hive NOT database` returnerar till gångar som innehåller Hive, men inte databasen. |
| () | Grupperar en uppsättning nyckelord och operatorer tillsammans. När du kombinerar flera operatorer, anger parentesen ordningen på åtgärderna. | Frågan `hive AND (database OR warehouse)` returnerar till gångar som innehåller "Hive" och antingen "Database" eller "Warehouse" eller båda. |
| "" | Anger exakt innehåll i en fras som frågan måste matcha. | Frågan `"hive database"` returnerar till gångar som innehåller frasen "Hive-databas" i egenskaperna |
| * | Ett jokertecken som matchar på ett till många tecken. Får inte vara första bokstaven i ett nyckelord. | Frågan `hiv\` * returnerar till gångar som har egenskaper som börjar med "HIV", till exempel "Hive" eller "Hive-Table". |
| ? | Ett jokertecken som matchar på ett enstaka-teckensnitt. Får inte vara första bokstaven i ett nyckelord | Frågan `hiv?` returnerar till gångar som har egenskaper som börjar med "HIV" och är fyra bokstäver som "Hive" eller "HIVA". |

> [!Note]
> Ange alltid booleska operatorer (**och**, **eller**, **inte**) med versaler. I annat fall spelar det ingen roll eller gör extra blank steg.

## <a name="next-steps"></a>Nästa steg

- [Skapa, importera och exportera ord listans villkor](how-to-create-import-export-glossary.md)
- [Så här hanterar du term mallar för företags ord lista](how-to-manage-term-templates.md)
