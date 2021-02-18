---
title: Azure API Management diagnostisera och lösa problem
description: Lär dig hur du felsöker problem med ditt API i Azure API Management med verktyget diagnostisera och lösa i Azure Portal.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d41dcb939f981ce9cb6d3eae328cb2eb9adc20c2
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654505"
---
# <a name="azure-api-management-diagnostics-overview"></a>Översikt över Azure API Management Diagnostics

När du har skapat och hanterat ett API i Azure API Management vill du vara för beredd för eventuella problem som kan uppstå, från 404 inte hitta fel till 502 felaktigt Gateway-fel. API Management Diagnostics är en intelligent och interaktiv upplevelse som hjälper dig att felsöka ditt API publicerat i APIM utan att någon konfiguration krävs. När du stöter på problem med dina publicerade API: er kan API Management diagnostik ta reda på vad som är fel och vägleda dig till rätt information för att snabbt felsöka och lösa problemet.

Även om den här upplevelsen är mest användbar när du har problem med ditt API under de senaste 24 timmarna, är alla diagnostiska grafer alltid tillgängliga så att du kan analysera dem.

## <a name="open-api-management-diagnostics"></a>Öppna API Management diagnostik

Om du vill komma åt API Management diagnostik navigerar du till API Management tjänst instansen i [Azure Portal](https://portal.azure.com). I det vänstra navigerings fältet väljer du **diagnostisera och lösa problem**.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Skärm bild som visar hur du navigerar till diagnostik.":::



## <a name="intelligent-search"></a>Intelligent sökning

Du kan söka efter problem eller problem i Sök fältet högst upp på sidan. Sökningen hjälper dig också att hitta de verktyg som kan hjälpa dig att felsöka eller lösa dina problem. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="skärm bild av intelligent sökning.":::


## <a name="troubleshooting-categories"></a>Fel söknings kategorier

Du kan felsöka problem under kategorier. Vanliga problem som är relaterade till API-prestanda, Gateway, API-principer och tjänst nivå kan analyseras i varje kategori. Varje kategori ger också mer exakta diagnostiska kontroller. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="skärm bild av kategori översikt.":::


### <a name="availability-and-performance"></a>Tillgänglighet och prestanda

Kontrol lera API-tillgänglighet och prestanda problem under den här kategorin. När du har valt den här kategori panelen visas några vanliga kontroller i ett interaktivt gränssnitt. Klicka på varje kontroll för att gå djupare till de olika problemen. Kontrollen ger också en graf som visar dina API-prestanda och en sammanfattning av prestanda problemen. Din API-tjänst kan till exempel ha haft ett 5xx-fel och en timeout under den senaste timmen på Server delen. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Skärm bild 1 av interaktiv gränssnitts kontroll.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Skärm bild 2 av interaktiv gränssnitts kontroll.":::

### <a name="api-policies"></a>API-principer

Den här kategorin identifierar fel och meddelar dig om princip problem. 

Ett liknande interaktivt gränssnitt vägleder dig till data måtten som hjälper dig att felsöka konfigurationen av API-principer.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="skärm bild av kategori panelen för API-principer.":::

### <a name="gateway-performance"></a>Gatewayprestanda 

Använd den här kategorin för att övervaka och felsöka för gateway-förfrågningar eller svar eller eventuella 4xx-eller 5xx-fel på din gateway. På samma sätt kan du använda det interaktiva gränssnittet för att gå djup på det angivna utrymmet som du vill kontrol lera för API-gatewayens prestanda. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="skärm bild av en kategori panel för gateway-prestanda.":::

### <a name="service-upgrade"></a>Tjänst uppgradering

Den här kategorin kontrollerar vilken Service Tier (SKU) som du använder för närvarande och påminner dig om att uppgradera för att undvika eventuella problem som kan vara relaterade till den nivån. Samma interaktiva gränssnitt hjälper dig att gå djup med mer grafik och ett resultat av en sammanfattnings kontroll. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="skärm bild av kategori panel för tjänste uppgradering.":::

## <a name="search-documentation"></a>Sök i dokumentationen

I ytterligare verktyg för att diagnostisera och lösa problem kan du söka efter fel söknings dokumentation som är relaterad till ditt problem. När du har kört diagnostiken på tjänsten väljer du **Sök efter dokumentation** i det interaktiva gränssnittet. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="skärm bild 1 av hur du använder Sök dokumentations funktionen.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="skärm bild 2 av hur du använder Sök dokumentation.":::


## <a name="next-steps"></a>Nästa steg

* Använd också [API Analytics](howto-use-analytics.md) för att analysera användning och prestanda för API: erna. 
* Vill du felsöka Web Apps problem med diagnostik? Läs det [här](../app-service/overview-diagnostics.md)
* Utnyttja diagnostik för att kontrol lera problem med Azure Kubernetes Services. Se [diagnostik på AKS](../aks/concepts-diagnostics.md)
* Publicera dina frågor eller feedback på [UserVoice](https://feedback.azure.com/forums/248703-api-management) genom att lägga till [diag] i rubriken.