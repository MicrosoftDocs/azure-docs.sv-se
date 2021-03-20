---
title: Migrera Azure CDN profil från Verizon standard till Verizon Premium
description: Lär dig mer om att migrera en profil från Verizon standard till Verizon Premium.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: e58c1177fb3c4d241a3efe1759b3900abbd04ca1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778395"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrera en Azure CDN profil från standard Verizon till Premium Verizon

När du skapar en profil för Azure Content Delivery Network (CDN) för att hantera dina slut punkter, erbjuder Azure CDN fyra olika produkter som du kan välja bland. Information om de olika produkterna och deras tillgängliga funktioner finns i [jämföra Azure CDN produkt funktioner](cdn-features.md).

Om du har skapat en **Azure CDN Standard från Verizon** -profilen och använder den för att hantera dina CDN-slutpunkter, har du möjlighet att uppgradera den till en **Azure CDN Premium från Verizon** -profil. När du uppgraderar bevaras dina CDN-slutpunkter och alla dina data. 

> [!IMPORTANT]
> När du har uppgraderat till en **Azure CDN Premium från Verizon** -profilen kan du inte senare konvertera tillbaka den till en **Azure CDN Standard från Verizon** -profilen.
> 

Kontakta [Microsoft Support](https://azure.microsoft.com/support/options/)om du vill uppgradera en **Azure CDN Standard från Verizon** -profilen.

## <a name="profile-comparison"></a>Jämförelse av profil
**Azure CDN Premium från Verizon** -profiler har följande viktiga skillnader från **Azure CDN Standard från Verizon** -profiler:
- För vissa Azure CDN funktioner som [komprimering](cdn-improve-performance.md), [regler för cachelagring](cdn-caching-rules.md)och [geo-filtrering](cdn-restrict-access-by-country.md)kan du inte använda Azure CDN-gränssnittet. du måste använda Verizon-portalen via knappen **Hantera** .
- API: till skillnad från vanliga Verizon kan du inte använda API: et för att styra de funktioner som nås från Premium Verizon-portalen. Du kan dock använda API: t för att kontrol lera andra vanliga funktioner, till exempel skapa/ta bort en slut punkt, rensa/läsa in cachelagrade till gångar och aktivera/inaktivera en anpassad domän.
- Priser: Premium Verizon har en annan pris struktur för data överföringar än vanliga Verizon. Mer information finns i [Content Delivery Network prissättning](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium från Verizon** -profiler har följande ytterligare funktioner:
- [Token-autentisering](cdn-token-auth.md): tillåter användare att hämta och använda en token för att hämta säkra resurser.
- [Regel motor](./cdn-verizon-premium-rules-engine.md): gör det möjligt att anpassa hur HTTP-begäranden hanteras.
- Avancerade analys verktyg:
   - [Detaljerad HTTP-analys](cdn-advanced-http-reports.md)
   - [Kant prestanda analys](cdn-edge-performance.md)
   - [Realtidsanalys](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Nästa steg
Läs mer om regel motorn i [Azure CDN regel motor referens](./cdn-verizon-premium-rules-engine-reference.md).