---
author: baanders
description: inkludera fil för Azure Digitals dubbla gränser
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 4a69b2ff15fc4857e9fb292d2f753aa68ed875d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370125"
---
### <a name="functional-limits"></a>Funktionella gränser

I följande tabell visas funktionella gränser för Azure Digital-dubbla. 

> [!TIP]
> För att utforma rekommendationer för att fungera inom dessa funktionella gränser, se [metod tips för att utforma modeller](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models).

| Område | Funktion | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Azure-resurs | Antal Azure Digitals dubbla instanser i en region, per prenumeration | 10 | Ja |
| Digitala tvillingenheter | Antal dubbla i en digital Azure-instans | 200 000 | Ja |
| Digitala tvillingenheter | Antal inkommande relationer till en enda | 5 000 | Inga |
| Digitala tvillingenheter | Antal utgående relationer från en enda delad | 5 000 | Inga |
| Digitala tvillingenheter | Maximal storlek (för JSON-innehåll i en begäran eller en PATCH-begäran) för en enskild | 32 KB | Inga |
| Digitala tvillingenheter | Maximal nytto Last storlek för begäran | 32 KB | Inga | 
| Routning | Antal slut punkter för en enskild Azure Digitals dubbla instans | 6 | Inga |
| Routning | Antal vägar för en enda digital Azure-instans | 6 | Ja |
| Modeller | Antal modeller inom en enskild Azure Digital-instans | 10 000 | Ja |
| Modeller | Antal modeller som kan överföras i ett enda API-anrop | 250 | Inga |
| Modeller | Maximal storlek (för JSON-innehåll i en begäran eller en PATCH-begäran) för en enskild modell | 1 MB | Inga |
| Modeller | Antal objekt som har returnerats på en enda sida | 100 | Inga |
| Fråga | Antal objekt som har returnerats på en enda sida | 100 | Ja |
| Söka i data | Antal `AND`  /  `OR` uttryck i en fråga | 50 | Ja |
| Söka i data | Antal mat ris objekt i en `IN`  /  `NOT IN` sats | 50 | Ja |
| Söka i data | Antal tecken i en fråga | 8,000 | Ja |
| Söka i data | Antal `JOINS` i en fråga | 5 | Ja |

### <a name="rate-limits"></a>Hastighetsbegränsningar

Följande tabell visar frekvens gränserna för olika API: er.

| API | Funktion | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Modell-API | Antal begär Anden per sekund | 100 | Ja |
| Digitals dubbla API | Antal begär Anden per sekund | 2 000 | Ja |
| Digitals dubbla API | Antal åtgärder för att skapa/ta bort per sekund i **alla dubbla och relationer** | 50 | Ja |
| Digitals dubbla API | Antal åtgärder för att skapa/uppdatera/ta bort per sekund på en **enskild** eller dess relationer | 10 | Inga |
| Fråge-API | Antal begär Anden per sekund | 500 | Ja |
| Fråge-API | [Fråge enheter](../articles/digital-twins/concepts-query-units.md) per sekund | 4 000 | Ja |
| API för händelse vägar | Antal begär Anden per sekund | 100 | Ja |

### <a name="other-limits"></a>Andra gränser

Gränser för data typer och fält i DTDL-dokument för Azure Digitals-modeller finns i Specifikations dokumentationen i GitHub: [*digital-DTDL Definition Language ()-version 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Information om svars tid och andra begränsningar för frågor finns i [*anvisningar: fråga det dubbla diagrammet*](../articles/digital-twins/how-to-query-graph.md).
