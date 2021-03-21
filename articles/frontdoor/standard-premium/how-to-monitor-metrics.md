---
title: Övervaknings mått för Azures front dörr standard/Premium
description: Den här artikeln beskriver måtten för övervaknings mått för Azures front dörr standard/Premium.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: bb10fb337972db2696960b530f2d7538bd36a2fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101099385"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Real tids övervakning i Azures främre dörr standard/Premium

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Azure frontend standard/Premium är integrerat med Azure Monitor och har 11 mått som hjälper dig att övervaka Azures främre dörr standard/Premium i real tid för att spåra, felsöka och felsöka problem.  

Azure frontend-standard/Premium-mått och skickar sina mått i intervall om 60 sekunder. Måtten kan ta upp till 3 minuter innan de visas i portalen. Mått kan visas i diagram eller rutnät som du väljer och är tillgängliga via portal, PowerShell, CLI och API. Mer information finns i [Azure Monitor mått](../../azure-monitor/platform/data-platform-metrics.md).  

Standard måtten är kostnads fria. Du kan aktivera ytterligare mått för en extra kostnad. 

Du kan konfigurera aviseringar för varje mått, till exempel ett tröskelvärde för 4XXErrorRate eller 5XXErrorRate. När fel frekvensen överskrider tröskelvärdet utlöses en avisering som har kon figurer ATS. Mer information finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md). 

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Mått som stöds i Azures främre dörr standard/Premium

| Mått  | Beskrivning | Dimensioner |
| ------------- | ------------- | ------------- |
| Träff grad för byte | Procent andelen utgående från AFD-cache, beräknad mot total utgående. </br> **Förhållandet mellan byte träff** = (utgångs punkt från Edge-utgång från ursprung)/egress från gräns. </br> **Scenarier som exkluderas i beräkningen av byte-träffar**:</br> 1. du konfigurerar inget cacheminne antingen via regel motor eller cachelagring av frågesträngar. </br> 2. du konfigurerar inte Cache-Control-direktivet utan lagring eller privat cache. </br>3. byte träff-förhållandet kan vara lågt om större delen av trafiken vidarebefordras till ursprung i stället för att hanteras från cachen baserat på dina konfigurationer eller scenarier. | Slutpunkt |
| RequestCount | Antalet klient begär Anden som hanteras av CDN. | Slut punkt, klient land, klient region, HTTP-status, HTTP-status grupp |
| ResponseSize | Antalet klient begär Anden som hanteras av AFD. |Slut punkt, klient land, klient region, HTTP-status, HTTP-status grupp |
| TotalLatency | Den totala tiden från klient förfrågan som mottagits av CDN **tills den sista svars bytet skickades från CDN till klienten**. |Slut punkt, klient land, klient region, HTTP-status, HTTP-status grupp |
| RequestSize | Antalet byte som har skickats som begär Anden från klienter till AFD. | Slut punkt, klient land, klient region, HTTP-status, HTTP-status grupp |
| 4XX% ErrorRate | Procent andelen av alla klient begär Anden som svars status koden är 4XX för. | Slut punkt, klient land, klient region |
| 5XX% ErrorRate | Procent andelen av alla klient begär Anden som svars status koden är 5XX för. | Slut punkt, klient land, klient region |
| OriginRequestCount  | Antal begär Anden som skickats från AFD till ursprung | Slut punkt, ursprung, HTTP-status, HTTP-status grupp |
| OriginLatency | Tiden räknat från när begäran skickades av AFD Edge till Server delen tills AFD fick den senaste svars byten från Server delen. | Slut punkt, ursprung |
| OriginHealth% | Procent andelen lyckade hälso avsökningar från AFD till ursprung.| Ursprung, ursprungs grupp |
| Antal WAF begär Anden | Matchad WAF-begäran. | Åtgärd, regel namn, princip namn |

## <a name="access-metrics-in-azure-portal"></a>Åtkomst mått i Azure Portal

1. Välj **alla resurser** på Azure Portal-menyn  >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. Under **övervakning** väljer du **mått**:

3. I **mått** väljer du det mått som ska läggas till:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Skärm bild av sidan mått." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Välj **Lägg till filter** för att lägga till ett filter:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Skärm bild som visar hur du lägger till filter till mått." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Välj **Använd delning** för att dela data efter olika dimensioner:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Skärm bild av mått för att lägga till dimensioner." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Välj **nytt diagram** för att lägga till ett nytt diagram:

## <a name="configure-alerts-in-azure-portal"></a>Konfigurera aviseringar i Azure Portal

1. Konfigurera aviseringar i Azure frontend standard/Premium (för hands version) genom att välja **övervaknings**  >>  **aviseringar**.

1. Välj **ny varnings regel** för mått som anges i avsnittet mått.

Aviseringen kommer att debiteras baserat på Azure Monitor. Mer information om aviseringar finns i [Azure Monitor aviseringar](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Standard-/Premium-rapporter i Azures front dörr](how-to-reports.md).
- Lär dig mer om [Standard-/Premium loggar för Azure-dörren](how-to-logs.md).
