---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80272609"
---
| Datatyp | Funktioner som tillåts i lambda-uttryck med `any` | Funktioner som tillåts i lambda-uttryck med `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Allt utom `search.ismatch` och `search.ismatchscoring` | Samma |
| `Collection(Edm.String)` | Jämförelser med `eq` eller `search.in` <br/><br/> Kombinera under uttryck med `or` | Jämförelser med `ne` eller `not search.in()` <br/><br/> Kombinera under uttryck med `and` |
| `Collection(Edm.Boolean)` | Jämförelser med `eq` eller `ne` | Samma |
| `Collection(Edm.GeographyPoint)` | Använda `geo.distance` med `lt` eller `le` <br/><br/> `geo.intersects` <br/><br/> Kombinera under uttryck med `or` | Använda `geo.distance` med `gt` eller `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Kombinera under uttryck med `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Jämförelser med hjälp av `eq` ,,,, `ne` `lt` `gt` `le` eller `ge` <br/><br/> Kombinera jämförelser med andra del uttryck med `or` <br/><br/> Kombinera jämförelser utom `ne` andra del uttryck med `and` <br/><br/> Uttryck med kombinationer av `and` och `or` i [Disjunctive normal form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Jämförelser med hjälp av `eq` ,,,, `ne` `lt` `gt` `le` eller `ge` <br/><br/> Kombinera jämförelser med andra del uttryck med `and` <br/><br/> Kombinera jämförelser utom `eq` andra del uttryck med `or` <br/><br/> Uttryck med kombinationer av `and` och `or` i [Conjunctive normal form (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
