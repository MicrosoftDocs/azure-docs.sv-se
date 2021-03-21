---
title: 'Azure-front dörr: slut punkts hanteraren'
description: Den här artikeln innehåller en översikt över slut punkts hanteraren för Azures front dörr.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100537"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Vad är Azure front dörr standard/Premium (för hands version) slut punkts hanterare?

> [!NOTE]
> * Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [Azures front dörrs dokument](../front-door-overview.md).

Slut punkts hanteraren ger en översikt över slut punkter som du har konfigurerat för din Azure-frontend. En slut punkt är en logisk gruppering av en domän och deras associerade konfigurationer. Med slut punkts hanteraren kan du hantera din samling av slut punkter för CRUD-åtgärd (skapa, läsa, uppdatera och ta bort). Du kan hantera följande element för dina slut punkter via slut punkts hanteraren:

* Domains
* Ursprungs grupper
* Vägar
* Säkerhet

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Skärm bild av slut punkts hanteraren utan konfigurationer." lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Slut punkts hanteraren visar hur många instanser av varje element som skapas inom en slut punkt. Associations status för varje element visas också. Du kan till exempel skapa flera domäner och ursprungs grupper och tilldela kopplingen mellan dem med olika vägar.

> [!IMPORTANT]
> * Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i kompletterande användnings [**villkor för Microsoft Azure för hands**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="linked-view"></a>Länkad vy

Med den länkade vyn i slut punkts hanteraren kan du enkelt identifiera associationen mellan dina Azures front dörrs element, till exempel:

* Vilka domäner är kopplade till den aktuella slut punkten?
* Vilken ursprungs grupp är associerad med vilken domän?
* Vilken WAF-princip är associerad med vilken domän?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Skärm bild av slut punkts hanteraren med konfigurationer." lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en front dörrs standard/Premium](create-front-door-portal.md).
