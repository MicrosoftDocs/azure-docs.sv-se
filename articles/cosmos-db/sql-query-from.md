---
title: FROM-sats i Azure Cosmos DB
description: Läs om SQL-syntaxen och exempel för FROM-satsen för Azure Cosmos DB. Den här artikeln innehåller också exempel på omfattnings resultat och hämta under objekt med hjälp av from-satsen.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 3844a628a8e3773821736324bfdaab3f64ff9da5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336290"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FROM-sats i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

From ( `FROM <from_specification>` )-satsen är valfri, om inte källan filtreras eller projiceras senare i frågan. En fråga som `SELECT * FROM Families` räknar upp över hela `Families` behållaren. Du kan också använda den särskilda identifierande roten för behållaren i stället för att använda behållar namnet.

`FROM`Satsen tillämpar följande regler per fråga:

* Containern kan vara ett alias, till exempel `SELECT f.id FROM Families AS f` eller bara `SELECT f.id FROM Families f`. Här `f` är aliaset för `Families` . SOM är ett valfritt nyckelord för att [aliasa](sql-query-working-with-json.md#aliasing) identifieraren.  

* Det går inte att binda det ursprungliga käll namnet när det har alias. Är till exempel `SELECT Families.id FROM Families f` syntaktiskt som ogiltigt eftersom identifieraren `Families` har fått ett alias och det inte går att lösa dem längre.  

* Alla refererade egenskaper måste vara fullständigt kvalificerade för att undvika tvetydiga bindningar i frånvaro av strikt schema. Till exempel `SELECT id FROM Families f` är syntaktiskt ogiltigt, eftersom egenskapen `id` inte är kopplad.

## <a name="syntax"></a>Syntax
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=
        ROOT
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argument
  
- `<from_source>`  
  
  Anger en data källa, med eller utan ett alias. Om alias inte anges härleds det från `<container_expression>` med hjälp av följande regler:  
  
-  Om uttrycket är ett container_name, kommer container_name att användas som ett alias.  
  
-  Om uttrycket är `<container_expression>` , sedan Property_Name, kommer Property_Name att användas som ett alias. Om uttrycket är ett container_name, kommer container_name att användas som ett alias.  
  
- SOM `input_alias`  
  
  Anger att `input_alias` är en uppsättning värden som returneras av det underliggande behållar uttrycket.  
 
- `input_alias` FÖR  
  
  Anger att `input_alias` ska motsvara den uppsättning värden som erhålls genom att iterera över alla mat ris element i varje matris som returneras av det underliggande behållar uttrycket. Värden som returneras av underliggande container-uttryck som inte är matris ignoreras.  
  
- `<container_expression>`  
  
  Anger det container uttryck som ska användas för att hämta dokumenten.  
  
- `ROOT`  
  
  Anger att dokumentet ska hämtas från standard behållaren som är ansluten.  
  
- `container_name`  
  
  Anger att dokumentet ska hämtas från den angivna behållaren. Namnet på behållaren måste matcha namnet på behållaren som för närvarande är ansluten till.  
  
- `input_alias`  
  
  Anger att dokumentet ska hämtas från den andra källan som definieras av det angivna aliaset.  
  
- `<container_expression> '.' property_name`  
  
  Anger att dokumentet ska hämtas genom att använda `property_name` egenskapen.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Anger att dokumentet ska hämtas genom att komma åt `property_name` egenskapen eller array_index mat ris elementet för alla dokument som hämtats av angivet container uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
Alla alias som anges eller är härledda i `<from_source>(` -erna måste vara unika. Syntaxen `<container_expression>.` Property_Name är samma som `<container_expression>' ['"property_name"']'` . Den senare syntaxen kan dock användas om ett egenskaps namn innehåller ett specialtecken som inte är en identifierare.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hantera saknade egenskaper, saknade mat ris element och odefinierade värden
  
Om ett behållar uttryck använder egenskaper eller mat ris element och det värdet inte finns, ignoreras det värdet och bearbetas inte ytterligare.  
  
### <a name="container-expression-context-scoping"></a>Kontext omfattning för container uttryck  
  
Ett behållar uttryck kan vara behållare-omfattning eller dokument omfattning:  
  
- Ett uttryck är container-scopet, om den underliggande källan för behållar uttrycket är antingen ROOT eller `container_name` . Ett sådant uttryck representerar en uppsättning dokument som hämtats från behållaren direkt, och som inte är beroende av bearbetningen av andra behållar uttryck.  
  
- Ett uttryck är dokument-omfattning, om den underliggande källan för behållar uttrycket `input_alias` introduceras tidigare i frågan. Ett sådant uttryck representerar en uppsättning dokument som hämtas genom att utvärdera behållar uttrycket i omfånget för varje dokument som hör till uppsättningen som är associerad med den behållare som har alias. Den resulterande uppsättningen är en union av uppsättningar som erhålls genom att utvärdera behållar uttrycket för varje dokument i den underliggande uppsättningen.

## <a name="examples"></a>Exempel

### <a name="get-subitems-by-using-the-from-clause"></a>Hämta under objekt med hjälp av from-satsen

FROM-satsen kan minska källan till en mindre delmängd. Om du bara vill räkna upp ett under träd i varje objekt kan underroten bli källan, vilket visas i följande exempel:

```sql
    SELECT *
    FROM Families.children
```

Resultatet är:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Föregående fråga använde en matris som källa, men du kan också använda ett objekt som källa. Frågan tar alla giltiga, definierade JSON-värden i källan som ska ingå i resultatet. Följande exempel skulle utesluta `Families` att inte har något `address.state` värde.

```sql
    SELECT *
    FROM Families.address.state
```

Resultatet är:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-sats](sql-query-select.md)
- [WHERE-satsen](sql-query-where.md)
