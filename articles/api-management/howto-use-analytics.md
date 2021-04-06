---
title: Använda API Analytics i Azure API Management | Microsoft Docs
description: 'Använd Analytics i Azure API Management för att hjälpa dig att förstå och kategorisera användningen av dina API: er och API-prestanda.'
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96841510"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Hämta API Analytics i Azure API Management

Azure API Management tillhandahåller inbyggd analys för dina API: er. Analysera användning och prestanda för API: erna i API Management instansen över flera dimensioner, inklusive:

* Tid
* Geografi
* API:er
* API-åtgärder
* Produkter
* Prenumerationer
* Användare
* Begäranden

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Tids linje analys i portalen":::

Använd Analytics för övervakning och fel sökning av dina API: er på hög nivå. Mer information om övervakningsfunktionerna, inklusive nära real tids mått och resurs loggar för diagnostik och granskning finns i [Självstudier: övervaka publicerade API: er](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Analytics – Portal

Använd Azure Portal för att snabbt granska Analytics-data för API Management-instansen.

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen. 
1. I den vänstra menyn, under **övervakning**, väljer du **analys**.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Välj analys för API Management instans i portalen":::  
1. Välj ett tidsintervall för data eller ange ett anpassat tidsintervall.
1. Välj en rapport kategori för analys data, till exempel **Timeline**, **geografi** och så vidare.
1. Du kan också filtrera rapporten efter en eller flera ytterligare kategorier.

## <a name="analytics---rest-api"></a>Analys-REST API

Använd [rapport](/rest/api/apimanagement/2019-12-01/reports) åtgärder i API Management REST API för att hämta och filtrera Analytics-data för din API Management instans.

Tillgängliga åtgärder returnerar rapport poster via API, geografi, API-åtgärder, produkt, begäran, prenumeration, tid eller användare.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Monitor funktioner i API Management finns i [Självstudier: övervaka publicerade API: er](api-management-howto-use-azure-monitor.md)
* Detaljerad HTTP-loggning och övervakning finns i [övervaka dina API: er med Azure API Management, Event Hubs och Moesif](api-management-log-to-eventhub-sample.md).
* Lär dig mer om att integrera [Azure-API Management med Azure Application insikter](api-management-howto-app-insights.md).