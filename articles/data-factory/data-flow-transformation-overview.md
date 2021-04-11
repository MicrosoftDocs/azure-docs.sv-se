---
title: Översikt över mappning av data flödes omvandling
description: En översikt över de olika omvandlingarna som är tillgängliga i mappnings data flödet
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: bb5021c0125c3140ef44a1ec3304b9d0ac40c30f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060235"
---
# <a name="mapping-data-flow-transformation-overview"></a>Översikt över mappning av data flödes omvandling

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Nedan visas en lista över de omvandlingar som stöds för närvarande i mappnings data flödet. Klicka på varje omvandling för att lära dig mer om konfigurationen.

| Name | Kategori | Beskrivning |
| ---- | -------- | ----------- |
| [Aggregera](data-flow-aggregate.md) | Schema modifierare | Definiera olika typer av agg regeringar som SUM, MIN, MAX och COUNT grupperade efter befintliga eller beräknade kolumner. | 
| [Ändra rad](data-flow-alter-row.md) | Rads modifierare | Ange INSERT-, DELETE-, Update-och upsert-principer på rader. |
| [Villkorsstyrd delning](data-flow-conditional-split.md) | Flera indata/utdata | Dirigera rader med data till olika data strömmar baserat på matchnings villkor. |
| [Härledd kolumn](data-flow-derived-column.md) | Schema modifierare | skapa nya kolumner eller ändra befintliga fält med hjälp av data flödets uttrycks språk. | 
| [Finns](data-flow-exists.md) | Flera indata/utdata | Kontrol lera om dina data finns i en annan källa eller Stream. | 
| [Filter](data-flow-filter.md) | Rads modifierare | Filtrera en rad baserat på ett villkor. |
| [Platta ut](data-flow-flatten.md) | Schema modifierare |  Ta mat ris värden i hierarkiska strukturer, till exempel JSON och avregistrera dem i enskilda rader. |
| [Join](data-flow-join.md) | Flera indata/utdata |  Kombinera data från två källor eller strömmar. |
| [Sökning](data-flow-lookup.md) | Flera indata/utdata | Referens data från en annan källa. |
| [Ny gren](data-flow-new-branch.md) | Flera indata/utdata | Tillämpa flera uppsättningar av åtgärder och omvandlingar mot samma data ström. |
| [Parse](data-flow-new-branch.md) | Formatering | Parsa text kolumner i data strömmen som är strängar för JSON, avgränsad text eller XML-formaterad text. |
| [Pivotera](data-flow-pivot.md) | Schema modifierare | En agg regering där en eller flera grupperade kolumner har sina distinkta rad värden omvandlade till enskilda kolumner. |
| [Rangordning](data-flow-rank.md) | Schema modifierare | Generera en ordnad rangordning baserat på sorterings villkor |
| [Välj](data-flow-select.md) | Schema modifierare | Alias-kolumner och strömmande namn, och släpp eller ordna om kolumner |
| [Kanalmottagare](data-flow-sink.md) | - | Ett slutgiltigt mål för dina data |
| [Sortera](data-flow-sort.md) | Rads modifierare | Sortera inkommande rader i den aktuella data strömmen |
| [Källa](data-flow-source.md) | - | En data källa för data flödet |
| [Surrogatnyckel](data-flow-surrogate-key.md) | Schema modifierare | Lägg till ett mellanliggande nyckel värde som inte är mellan företag |
| [Union](data-flow-union.md) | Flera indata/utdata | Kombinera flera data strömmar lodrätt |
| [Normalisera](data-flow-unpivot.md) | Schema modifierare | Pivotera kolumner i rad värden |
| [Fönster](data-flow-window.md) | Schema modifierare |  Definiera Window-baserade agg regeringar för kolumner i dina data strömmar. |
| [Parse](data-flow-parse.md) | Schema modifierare |  Parsa kolumn data till JSON eller avgränsad text |
