---
title: Jämförelse av SKU för Azure front dörr standard/Premium
description: 'Den här artikeln innehåller en översikt över SKU: er för standard-och Premium-SKU i Azure och funktions skillnader.'
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 0f5ecef1716a503b7d27d0f5cdde15a4560c0e61
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100588"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Översikt över SKU för Azure front dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Azures front dörr erbjuds för tre olika SKU: er, [Azure-frontend](../front-door-overview.md), Azure-front dörr standard (för hands version) och Azure frontend (för hands version). Azure frontend-standarden/Premium-SKU: er kombinerar funktioner i Azures front dörr, Azure CDN Standard från Microsoft, Azure WAF till en enda säker moln CDN-plattform med intelligent hot skydd.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i kompletterande användnings [**villkor för Microsoft Azure för hands**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

* **Azure front dörr standard SKU** är:

    * Innehålls leverans optimerad
    * Erbjuder både statisk och dynamisk innehålls acceleration
    * Global belastnings utjämning
    * SSL-avlastning
    * Hantering av domäner och certifikat
    * Förbättrad trafik analys 
    * Grundläggande säkerhetsfunktioner

* **Azure front dörr Premium SKU** bygger på funktioner i standard-SKU och lägger till:

    * Omfattande säkerhetsfunktioner över WAF
    * BOT-skydd
    * Stöd för privata länkar
    * Integrering med Microsoft Threat intelligence och Security Analytics. 

![Diagram som visar en jämförelse mellan SKU: er för front dörren.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Jämför funktioner

| Funktion |      Standard      |  Premium |
|----------|:-------------:|------:|
| Anpassade domäner | Ja | Ja |
| SSL-avlastning | Ja | Ja |
| Cachelagring |  Ja  | Ja |
| Komprimering | Ja | Ja   |
| Global belastnings utjämning | Ja  | Ja |
| Layer 7-routning | Ja | Ja |
| URL-omskrivning | Ja | Ja |
| Regelmotor | Ja | Ja |
| Privat ursprung (privat länk) | Nej | Ja |
| Brandvägg för webbaserade program | Nej | Ja |
| Bot-skydd | Nej | Ja |
| Förbättrade mått och diagnostik | Ja | Ja |
| Trafik rapporter | Ja | Ja |
| Säkerhets rapport | Nej | Ja | 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörr](create-front-door-portal.md)
