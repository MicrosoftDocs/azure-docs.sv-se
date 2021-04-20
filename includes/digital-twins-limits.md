---
author: baanders
description: include file for Azure Digital Twins limits
ms.service: digital-twins
ms.topic: include
ms.date: 4/8/2021
ms.author: baanders
ms.openlocfilehash: 34fec713c3764987f07bc7fb89ecb0a0d770a840
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728032"
---
### <a name="functional-limits"></a>Funktionella gränser

I följande tabell visas funktionsgränserna för Azure Digital Twins. 

> [!TIP]
> Modelleringsrekommendationer för att arbeta inom dessa funktionella gränser finns [i Metodtips för att utforma modeller.](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models)

| Område | Funktion | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Azure-resurs | Antal Azure Digital Twins instanser i en region per prenumeration | 10 | Yes |
| Digitala tvillingenheter | Antal tvillingar i en Azure Digital Twins instans | 200 000 | Yes |
| Digitala tvillingenheter | Antal inkommande relationer till en enda tvilling | 5 000 | No |
| Digitala tvillingenheter | Antal utgående relationer från en enda tvilling | 5 000 | No |
| Digitala tvillingenheter | Maximal storlek (JSON-brödtext i en PUT- eller PATCH-begäran) för en enskild tvilling | 32 kB | No |
| Digitala tvillingenheter | Maximal storlek för nyttolast för begäran | 32 kB | No | 
| Routning | Antal slutpunkter för en enskild Azure Digital Twins instans | 6 | No |
| Routning | Antal vägar för en enskild Azure Digital Twins instans | 6 | Yes |
| Modeller | Antal modeller inom en enda Azure Digital Twins instans | 10 000 | Yes |
| Modeller | Antal modeller som kan laddas upp i ett enda API-anrop | 250 | No |
| Modeller | Maximal storlek (JSON-brödtext i en PUT- eller PATCH-begäran) för en enskild modell | 1 MB | No |
| Modeller | Antal objekt som returneras på en enda sida | 100 | No |
| Fråga | Antal objekt som returneras på en enda sida | 100 | Ja |
| Söka i data | Antal uttryck `AND`  /  `OR` i en fråga | 50 | Ja |
| Söka i data | Antal matrisobjekt i en `IN`  /  `NOT IN` -sats | 50 | Ja |
| Söka i data | Antal tecken i en fråga | 8,000 | Ja |
| Söka i data | Antal i `JOINS` en fråga | 5 | Yes |

### <a name="rate-limits"></a>Hastighetsbegränsningar

I följande tabell visas hastighetsbegränsningarna för olika API:er.

| API | Funktion | Standardgräns | Justerbar? |
| --- | --- | --- | --- |
| Modell-API | Antal begäranden per sekund | 100 | Yes |
| Digital Twins API | Antal läsbegäranden per sekund | 1 000 | Yes |
| Digital Twins API | Antal korrigeringsbegäranden per sekund | 1 000 | Yes |
| Digital Twins API | Antal åtgärder för att skapa/ta bort per sekund **för alla tvillingar och relationer** | 50 | Yes |
| Digital Twins API | Antal åtgärder för att skapa/uppdatera/ta bort per sekund på en **enskild tvilling** eller dess relationer | 10 | No |
| Fråge-API | Antal begäranden per sekund | 500 | Yes |
| Fråge-API | [Frågeenheter](../articles/digital-twins/concepts-query-units.md) per sekund | 4 000 | Yes |
| API för händelsevägar | Antal begäranden per sekund | 100 | Yes |

### <a name="other-limits"></a>Andra gränser

Begränsningar för datatyper och fält i DTDL-dokument för Azure Digital Twins-modeller finns i dess specifikationsdokumentation i GitHub: [*Digital Twins Definition Language (DTDL) – version 2.*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)
 
Information om frågesvarstid och andra frågebegränsningar finns i Så här [*frågar du tvillingdiagrammet*](../articles/digital-twins/how-to-query-graph.md).
