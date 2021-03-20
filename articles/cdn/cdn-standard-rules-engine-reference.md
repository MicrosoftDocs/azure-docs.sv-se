---
title: Motor referens för standard regler för Azure CDN | Microsoft Docs
description: Referens dokumentation för matchnings villkor och åtgärder i standard regel motorn för Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93242216"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referens för standardregelmotor för Azure CDN

I [standard regel motorn](cdn-standard-rules-engine.md) för Azure Content Delivery Network (Azure CDN) består en regel av ett eller flera matchnings villkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av matchnings villkor och-funktioner som är tillgängliga i standard regel motorn för Azure CDN.

Regel motorn är utformad för att vara den slutliga behörigheten för hur vissa typer av begär Anden bearbetas av standard Azure CDN.

**Vanliga användnings områden för reglerna**:

- Åsidosätt eller definiera en anpassad princip för cachelagring.
- Omdirigera begär Anden.
- Ändra HTTP-begäran och svarshuvuden.

## <a name="terminology"></a>Terminologi

Ange [matchnings villkor](cdn-standard-rules-engine-match-conditions.md) och [åtgärder](cdn-standard-rules-engine-actions.md)för att definiera en regel i regel motorn:

 ![Regel struktur för Azure CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Varje regel kan ha upp till tio matchnings villkor och fem åtgärder. Varje Azure CDN slut punkt kan ha upp till 25 regler. 

Som ingår i den här gränsen är en *Global standard regel*. Den globala regeln har inga matchnings villkor. åtgärder som definieras i en global regel utlöses alltid.

   > [!IMPORTANT]
   > Ordningen i vilken flera regler visas påverkar hur regler hanteras. De åtgärder som anges i en regel kan skrivas över av en efterföljande regel.

## <a name="limits-and-pricing"></a>Begränsningar och priser 

Varje Azure CDN slut punkt kan ha upp till 25 regler. Varje regel kan ha upp till tio matchnings villkor och fem åtgärder. Prissättningen för regel motorn följer nedanstående dimensioner: 
- Regler: $1 per regel per månad 
- Bearbetade begär Anden: $0,60 per miljon begär Anden
- De första 5 reglerna är fortfarande fria

## <a name="syntax"></a>Syntax

Hur specialtecken behandlas i en regel beror på hur olika matchnings villkor och åtgärder hanterar text värden. Ett matchnings villkor eller en åtgärd kan tolka text på något av följande sätt:

- [Litterala värden](#literal-values)
- [Jokertecken](#wildcard-values)


### <a name="literal-values"></a>Litterala värden

Text som tolkas som ett litteralt värde behandlar alla specialtecken *förutom symbolen%* som en del av värdet som måste matchas i en regel. Ett villkor för en litteral matchning är till exempel `'*'` uppfyllt endast när det exakta värdet `'*'` hittas.

Ett procent tecken används för att indikera URL-kodning (till exempel `%20` ).

### <a name="wildcard-values"></a>Jokertecken

För närvarande stöder vi jokertecknet i **villkoret UrlPath match** i standard regel motorn. \*Tecknet är ett jokertecken som representerar ett eller flera tecken. 

## <a name="next-steps"></a>Nästa steg

- [Matchnings villkor i standard regel motorn](cdn-standard-rules-engine-match-conditions.md)
- [Åtgärder i standard regel motorn](cdn-standard-rules-engine-actions.md)
- [Kräv HTTPS med hjälp av standardregelmotorn](cdn-standard-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)
