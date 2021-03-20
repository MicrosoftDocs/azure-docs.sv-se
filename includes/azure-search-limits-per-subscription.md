---
title: ta med fil
description: ta med fil
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80272632"
---
Du kan skapa flera tjänster i en prenumeration. Var och en kan tillhandahållas på en speciell nivå. Du är begränsad endast av antalet tjänster som tillåts på varje nivå. Du kan till exempel skapa upp till 12 tjänster på Basic-nivån och en annan 12-tjänst på S1-nivån i samma prenumeration. Mer information om nivåer finns i [Välj en SKU eller nivå för Azure kognitiv sökning](../articles/search/search-sku-tier.md).

Högsta antal tjänst gränser kan höjas vid begäran. Kontakta Azure-supporten om du behöver fler tjänster inom samma prenumeration.

| Resurs            | Kostnads fri<sup>1</sup> | Grundläggande | S1  | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Högsta antal tjänster    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximal skala i Sök enheter (SU)<sup>2</sup> |Ej tillämpligt |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> kostnads fri baseras på delade, inte dedikerade resurser. Det finns inte stöd för att skala upp på delade resurser.

<sup>2</sup> Sök enheter är fakturerings enheter, allokerade som antingen en *replik* eller en *partition*. Du behöver båda resurserna för lagring, indexering och frågor. Mer information om SU-beräkningar finns i [skala resurs nivåer för frågor och index-arbetsbelastningar](../articles/search/search-capacity-planning.md). 