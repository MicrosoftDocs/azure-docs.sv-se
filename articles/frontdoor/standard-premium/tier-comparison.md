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
ms.openlocfilehash: 1753f2bb649e73d7a5fe6c1cc32361a418ea7f63
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181813"
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
| Privat ursprung (privat länk) | Inga | Ja |
| Brandvägg för webbaserade program | Endast anpassade regler | Ja |
| Bot-skydd | Inga | Ja |
| Förbättrade mått och diagnostik | Ja | Ja |
| Trafik rapport | Ja | Ja |
| Säkerhets rapport | Inga | Ja | 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörr](create-front-door-portal.md)
