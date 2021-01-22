---
author: baanders
description: inkludera fil för Azure Digitals dubbla gränser
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 08ae189253c6141a6b84035b71016ff5099ec162
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689651"
---
### <a name="functional-limits"></a>Funktionella gränser

I tabellen nedan visas funktionella gränser för Azure Digital-dubbla.

| Område | Funktion | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Azure-resurs | Antal Azure Digitals dubbla instanser i en region, per prenumeration | 10 | Yes |
| Digitala tvillingenheter | Antal dubbla i en digital Azure-instans | 200 000 | Yes |
| Digitala tvillingenheter | Antal inkommande relationer till en enda | 5 000 | No |
| Digitala tvillingenheter | Antal utgående relationer från en enda delad | 5 000 | No |
| Digitala tvillingenheter | Maximal storlek (för JSON-innehåll i en begäran eller en PATCH-begäran) för en enskild | 32 KB | No |
| Digitala tvillingenheter | Maximal nytto Last storlek för begäran | 32 KB | No | 
| Routning | Antal slut punkter för en enskild Azure Digitals dubbla instans | 6 | No |
| Routning | Antal vägar för en enda digital Azure-instans | 6 | Yes |
| Modeller | Antal modeller inom en enskild Azure Digital-instans | 10 000 | Yes |
| Modeller | Antal modeller som kan överföras i ett enda API-anrop | 250 | No |
| Modeller | Antal objekt som har returnerats på en enda sida | 100 | No |
| Söka i data | Antal objekt som har returnerats på en enda sida | 100 | Ja |
| Söka i data | Antal `AND`  /  `OR` uttryck i en fråga | 50 | Ja |
| Söka i data | Antal mat ris objekt i en `IN`  /  `NOT IN` sats | 50 | Ja |
| Söka i data | Antal tecken i en fråga | 8,000 | Ja |
| Söka i data | Antal `JOINS` i en fråga | 5 | Yes |

### <a name="rate-limits"></a>Hastighetsbegränsningar

Följande tabell visar frekvens gränserna för olika API: er.

| API | Funktion | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Modell-API | Antal begär Anden per sekund | 100 | Yes |
| Digitals dubbla API | Antal begär Anden per sekund | 2 000 | Yes |
| Digitals dubbla API | Antal åtgärder för att skapa/ta bort per sekund i **alla dubbla och relationer** | 50 | Yes |
| Digitals dubbla API | Antal åtgärder för att skapa/uppdatera/ta bort per sekund på en **enskild** eller dess relationer | 10 | No |
| Fråge-API | Antal begär Anden per sekund | 500 | Yes |
| Fråge-API | [Fråge enheter](../articles/digital-twins/concepts-query-units.md) per sekund | 4 000 | Yes |
| API för händelse vägar | Antal begär Anden per sekund | 100 | Yes |

### <a name="other-limits"></a>Andra gränser

Gränser för data typer och fält i DTDL-dokument för Azure Digitals-modeller finns i Specifikations dokumentationen i GitHub: [*digital-DTDL Definition Language ()-version 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
Information om svars tid och andra begränsningar för frågor finns i [*anvisningar: fråga det dubbla diagrammet*](../articles/digital-twins/how-to-query-graph.md).
