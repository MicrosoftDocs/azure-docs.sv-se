---
title: Uppdatering av klassisk avisering & övervakning i Azure Monitor
description: Beskrivning av indragningen av klassiska övervaknings tjänster och funktioner, tidigare visas i Azure Portal under aviseringar (klassisk).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.openlocfilehash: bfa92a2fc58d479edd328dba9bf02d57ec66c0c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041101"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Enhetlig avisering & övervakning i Azure Monitor ersätter klassisk avisering & övervakning

Azure Monitor är en enhetlig övervaknings stack som stöder "ett mått" och "en avisering" mellan Azure-resurser. Mer information finns i det här [blogg inlägget](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). De nya plattformarna för Azure-övervakning och-avisering har utformats för att vara snabbare, smartare och utöknings Bar – hålla jämna steg med den växande expanse av molnbaserad data behandling och online med Microsoft Intelligent Cloud-filosofi.

Med den nya plattformen för Azure-övervakning och avisering på plats kommer de klassiska aviseringarna i Azure Monitor att dras tillbaka för offentliga moln användare, men fortfarande i begränsad användning till och med **31 maj 2021**. Klassiska aviseringar för Azure Government molnet och Azure Kina 21Vianet kommer att dra tillbaka den **29 februari 2024**.

 ![Klassisk avisering i Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Vi rekommenderar att du kommer igång och återskapar dina aviseringar på den nya plattformen.

> [!IMPORTANT]
> Klassiska varnings regler som skapats i aktivitets loggen är inte föråldrade eller migreras inte. Alla klassiska varnings regler som skapats i aktivitets loggen kan nås och används som de är från nya Azure Monitor-aviseringar. Mer information finns i [skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](./alerts-activity-log.md). På samma sätt kan aviseringar på Service Health kommas åt och användas som de är från avsnittet ny Service Health. Mer information finns i [aviseringar om meddelanden om tjänst hälsa](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Enhetliga mått och aviseringar för Azure-resurser

I mars 2018 lanserade vi nästa generations avisering för Azure-resurser. Den nya mått plattformen och aviseringen är snabbare och ger mer detaljerad information med hjälp av dimensioner. Med dimensionerna kan du segmentera och filtrera till en speciell värde kombination, villkor eller åtgärd. De nyare mått aviseringarna använder åtgärds grupper för att tillåta fler meddelanden och automatiserings åtgärder. Se mer information om hur du [hanterar mått aviseringar med hjälp av Azure Monitor](./alerts-metric.md).

Nya mått för Azure-resurser är tillgängliga som:

- **Azure Monitor standard plattforms mått** – som ger populära förifyllda mått från olika Azure-tjänster och-produkter. Mer information finns i den här artikeln om [mått som stöds på Azure Monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) och [stöd för mått varningar på Azure Monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor anpassade mått** – som tillhandahåller mått från användar drivna källor, inklusive Azure-diagnostik-agenten. Mer information finns i den här artikeln om [anpassade mått i Azure Monitor](../essentials/metrics-custom-overview.md). Med anpassade mått kan du också publicera mått som samlats in av [Windows Azure-diagnostik-agenten](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) och [InfluxDatain ympkvistar-agenten](../essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Dra tillbaka klassisk övervaknings-och varnings plattform

Som tidigare nämnts är äldre klassisk övervakning och avisering dras tillbaka för offentliga moln användare. Det omfattar avslutning av relaterade API: er, Azure Portal gränssnitt och tjänster, men fortfarande i begränsad användning till och med **31 maj 2021**. Klassiska aviseringar för Azure Government molnet och Azure Kina 21Vianet kommer att dra tillbaka den **29 februari 2024**.

Mer specifikt är omfånget för de klassiska mått som för närvarande är tillgängligt i [avsnittet om aviseringar (klassisk)](./alerts-classic.overview.md) i Azure Portal och är tillgängliga som [Microsoft. Insights/alertrules-](/rest/api/monitor/alertrules) resurser.

Det innebär att du måste:

- Den klassiska övervaknings-och aviserings tjänsten dras tillbaka och kan inte längre användas för att skapa nya varnings regler.
- Eventuella varnings regler som fortsätter att finnas i aviseringar (klassisk) fortsätter att köras och utlösa aviseringar.
- De flesta klassiska varnings regler kommer att migreras. Processen kommer att vara sömlös utan drift avbrott och kunder har ingen förlust av övervaknings täckning.
- Utlösta meddelanden innehåller ny nytto Last struktur. Målet måste anpassas för att fungera med den nya strukturen.
- Vissa [klassiska varnings regler som inte kan migreras automatiskt och som](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) kräver manuell åtgärd från användare att fortsätta att köras.

> [!IMPORTANT]
> Azure Monitor har distribuerat ett [verktyg för att frivilligt migrera](alerts-using-migration-tool.md) sina klassiska varnings regler till den nya plattformen. Återstående regler migreras automatiskt när tjänsten har dragits tillbaka. Kunderna måste se till att den klassiska varnings regel nytto lasten är anpassad för att hantera den nya nytto lasten från [enhetliga mått och aviseringar för andra Azure-resurser](#unified-metrics-and-alerts-for-azure-resources), efter migrering av de klassiska varnings reglerna. Mer information finns i [förbereda för klassisk migrering av aviserings regler](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Priser för migrerade varnings regler

Vi har lanserat ett Migreringsverktyg för att hjälpa dig att migrera dina Azure Monitor [klassiska aviseringar](./alerts-classic.overview.md) till den nya aviserings upplevelsen. De migrerade varnings reglerna och motsvarande migrerade åtgärds grupper (e-post, webhook eller LogicApp) är kostnads fria. De funktioner som du har haft med klassiska aviseringar, inklusive möjligheten att redigera tröskeln, sammansättnings typ och agg regerings kornig het, fortsätter att vara tillgängliga kostnads fritt med den migrerade varnings regeln. Men om du redigerar den migrerade varnings regeln för att använda någon av de nya varnings plattforms funktionerna, meddelanden eller åtgärds typer, kommer en motsvarande avgift att gälla. Mer information om priser för varnings regler och aviseringar finns i [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

Följande är exempel på fall där du debiteras för aviserings regeln:

- Alla nya aviserings regler (ej migrerade) som skapats utanför kostnads fria enheter, på den nya Azure Monitors plattformen
- Alla data som samlas in och behålls utöver de kostnads fria enheterna som ingår i Azure Monitor
- Alla webbtester med flera test som körs av Application Insights
- Alla anpassade mått som lagras bortom kostnads fria enheter som ingår i Azure Monitor
- Eventuella migrerade varnings regler som redige ras för att använda nya mått, t. ex. frekvens, flera resurser/dimensioner, [dynamiska tröskelvärden](../alerts/alerts-dynamic-thresholds.md), ändring av resurs/signal och så vidare.
- Alla migrerade åtgärds grupper som redige ras för att använda nyare meddelanden eller åtgärds typer som SMS, röst samtal och/eller ITSM-integrering.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den [nya enhetliga Azure Monitor](../overview.md).
* Läs mer om de nya [Azure-aviseringarna](./alerts-overview.md).