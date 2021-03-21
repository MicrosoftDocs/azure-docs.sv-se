---
title: Azure Monitor arbets böcker – transformera JSON-data med JSONPath
description: Så här använder du JSONPath i Azure Monitor-arbetsböcker för att transformera resultatet av JSON-data som returneras av en efterfrågad slut punkt till det format som du vill använda.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 142853f0d5ed787d0b7aaee1bb118a001f144227
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722933"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Använda JSONPath för att transformera JSON-data i arbets böcker

Arbets böcker kan fråga efter data från många källor. Vissa slut punkter, till exempel [Azure Resource Manager](../../azure-resource-manager/management/overview.md) eller anpassad slut punkt, kan returnera resultat i JSON. Om JSON-data som returneras av den efterfrågade slut punkten inte har kon figurer ATS i ett format som du vill använda kan JSONPath användas för att omvandla resultatet.

JSONPath är ett frågespråk för JSON som liknar XPath för XML. Som XPath tillåter JSONPath extrahering och filtrering av data från JSON-strukturen.

Genom att använda JSONPath-transformering kan arbets boks författare konvertera JSON till en tabell struktur. Tabellen kan sedan användas för att rita [visualiseringar av arbets böcker](./workbooks-overview.md#visualizations).

## <a name="using-jsonpath"></a>Använda JSONPath

1. Ändra arbets boken till redigerings läge genom att klicka på objektet *Redigera* verktygsfält.
2. Använd länken *Lägg* till  >  *Lägg till fråga* för att lägga till en frågeplan i arbets boken.
3. Välj data källan som *JSON*.
4. Använd JSON-redigeraren för att ange följande JSON-kodfragment
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Låt oss anta att JSON-objektet ovan ges som en representation av butikens lager. Vår uppgift är att skapa en tabell över butikens tillgängliga böcker genom att lista sina titlar, författare och priser.

1. Välj fliken *resultat inställningar* och Byt resultat format till JSON- *sökväg*.
2. Använd följande inställningar för JSON-sökväg:

    JSON-Sök väg tabell: `$.store.books` . Det här fältet representerar sökvägen till tabellens rot. I det här fallet bryr vi oss om butikens bok lager. Tabell Sök vägen filtrerar JSON till bok informationen.

   | Kolumn-ID: n | Kolumn-JSON-sökväg |
   |:-----------|:-----------------|
   | Rubrik      | `$.title`        |
   | Författare     | `$.author`       |
   | Pris      | `$.price`        |

    Kolumn-ID: n är kolumn rubriker. Kolumn-JSON sökvägar fält representerar sökvägen från tabell roten till kolumnvärdet.

1. Tillämpa inställningarna ovan genom att klicka på *Kör fråga*

![ Redigera frågetext med resultat formatet JSON-datakälla och JSON-sökväg](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Nästa steg
- [Översikt över arbets böcker](./workbooks-overview.md)
- [Grupper i Azure Monitor arbets böcker](workbooks-groups.md)