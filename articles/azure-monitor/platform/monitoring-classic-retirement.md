---
title: Uppdatering av klassisk avisering & övervakning i Azure Monitor
description: Beskrivning av indragningen av klassiska övervaknings tjänster och funktioner, tidigare visas i Azure Portal under aviseringar (klassisk).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 368ab1bc6a1fc13c3001b437c3c2a8be2bbb9c04
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525999"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Enhetlig avisering & övervakning i Azure Monitor ersätter klassisk avisering & övervakning

Azure Monitor har nu blivit en enhetlig tjänst för fullständig stack övervakning som nu stöder "ett mått" och "en avisering" mellan resurser. Mer information finns i vårt [blogg inlägg på nya Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). De nya plattformarna för Azure-övervakning och-avisering har utformats för att vara snabbare, smartare och utöknings Bar – hålla jämna steg med den växande expanse av molnbaserad data behandling och online med Microsoft Intelligent Cloud-filosofi.

Med den nya plattformen för Azure-övervakning och avisering är de klassiska aviseringarna i Azure Monitor pensionerade för offentliga moln användare, men fortfarande i begränsad användning för resurser som ännu inte stöder de nya aviseringarna. Datum för indragningen för dessa aviseringar har ännu förlängts. Ett nytt datum meddelas snart för migrering av återstående aviseringar, [Azure Government molnet](../../azure-government/documentation-government-welcome.md)och [Azure Kina 21Vianet](https://docs.azure.cn/).

 ![Klassisk avisering i Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Vi rekommenderar att du kommer igång och återskapar dina aviseringar på den nya plattformen.

> [!IMPORTANT]
> Klassiska varnings regler som skapats i aktivitets loggen är inte föråldrade eller migreras inte. Alla klassiska varnings regler som skapats i aktivitets loggen kan nås och används som de är från nya Azure Monitor-aviseringar. Mer information finns i [skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](./alerts-activity-log.md). På samma sätt kan aviseringar på Service Health kommas åt och användas som de är från avsnittet ny Service Health. Mer information finns i [aviseringar om meddelanden om tjänst hälsa](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Enhetliga mått och aviseringar i Application Insights

Azure Monitors nya mått plattform kommer nu att komma från Application Insights. Den här flytten innebär att Application Insights kopplar till åtgärds grupper, vilket ger mycket fler alternativ än bara de tidigare e-post-och webhook-anropen. Aviseringar kan nu utlösa röst samtal, Azure Functions, Logic Apps, SMS och ITSM-verktyg som ServiceNow och Automation-runbooks. Med den nya plattformen för övervakning och avisering i nära real tid kan Application Insights användare använda samma teknik för att övervaka andra Azure-resurser och underfästa övervakning av Microsoft-produkter.

Den nya enhetliga övervakningen och aviseringen för Application Insights kommer att omfatta:

- **Application Insights plattforms mått** – som ger populära förbyggda mått från Application Insights produkt. Mer information finns i den här artikeln om hur du använder [plattforms mått för Application Insights på nya Azure Monitor](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights tillgänglighet och webbtest** – vilket ger dig möjlighet att utvärdera svars tider och tillgänglighet för din webbapp eller server. Mer information finns i den här artikeln om [att använda tillgänglighets test och aviseringar för Application Insights på nya Azure Monitor](../app/monitor-web-app-availability.md).
- **Application Insights anpassade mått** – som gör att du kan definiera och generera egna mått för övervakning och aviseringar. Mer information finns i den här artikeln om hur du använder [anpassad mått för Application Insights på nya Azure Monitor](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Application Insights fel avvikelser (del av Smart identifiering)** – som automatiskt meddelar dig i nära real tid om din webbapp upplever en onormal ökning av antalet misslyckade HTTP-förfrågningar eller beroende anrop. Mer information finns i den här artikeln om att använda [Smart identifiering – fel avvikelser](../app/proactive-failure-diagnostics.md).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Enhetliga mått och aviseringar för andra Azure-resurser

Sedan mars 2018 har nästa generations avisering och flerdimensionell övervakning för Azure-resurser varit i tillgänglighet. Nu är den nya mått plattformen och aviseringar snabbare med funktioner i nästan real tid. Det är viktigt att de nyare mått plattforms aviseringarna ger mer detaljerad information, eftersom den nya plattformen innehåller alternativ för dimensioner, vilket gör att du kan segmentera och filtrera efter en speciell värde kombination, villkor eller åtgärd. Precis som med alla aviseringar i nya Azure Monitor, är de nyare mått aviseringarna mer utöknings bara med användningen av ActionGroups – vilket gör att meddelanden kan utökas utöver e-post eller webhook till SMS, röst, Azure Function, Automation Runbook med mera. Mer information finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](./alerts-metric.md).
Nya mått för Azure-resurser är tillgängliga som:

- **Azure Monitor standard plattforms mått** – som ger populära förifyllda mått från olika Azure-tjänster och-produkter. Mer information finns i den här artikeln om [mått som stöds på Azure Monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) och [stöd för mått varningar på Azure Monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor anpassade mått** – som tillhandahåller mått från användar drivna källor, inklusive Azure-diagnostik-agenten. Mer information finns i den här artikeln om [anpassade mått i Azure Monitor](./metrics-custom-overview.md). Med anpassade mått kan du också publicera mått som samlats in av [Windows Azure-diagnostik-agenten](./collect-custom-metrics-guestos-resource-manager-vm.md) och [InfluxDatain ympkvistar-agenten](./collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Dra tillbaka klassisk övervaknings-och varnings plattform

Som tidigare nämnts har äldre klassisk övervakning och avisering dragits tillbaka för offentliga moln användare. inklusive stängning av relaterade API: er, Azure Portal gränssnitt och tjänster, men fortfarande i begränsad användning för resurser som ännu inte stöder de nya aviseringarna. Mer specifikt är dessa funktioner inaktuella:

- Äldre (klassiska) mått och aviseringar för Azure-resurser som för närvarande är tillgängliga via [aviseringar (klassisk)](./alerts-classic.overview.md) i Azure Portal; tillgänglig som [Microsoft. Insights/alertrules-](/rest/api/monitor/alertrules) resurs
- Äldre (klassisk) plattform och anpassade mått för Application Insights samt avisering om dem som för närvarande är tillgängliga via [aviseringar (klassisk)](./alerts-classic.overview.md) i Azure Portal och som är tillgängliga som [Microsoft. Insights/alertrules-](/rest/api/monitor/alertrules) resurs
- Varning om äldre (klassisk) fel avvikelser är för närvarande tillgängliga som [Smart identifiering i Application Insights](../app/proactive-diagnostics.md) i Azure Portal; med aviseringar som kon figurer ATS i [avsnittet aviseringar (klassisk)](./alerts-classic.overview.md) i Azure Portal

Det innebär att du måste:

- Den klassiska övervaknings-och aviserings tjänsten dras tillbaka och kan inte längre användas för att skapa nya varnings regler.
- Eventuella varnings regler som fortsätter att finnas i aviseringar (klassisk) fortsätter att köras och utlösa aviseringar.
- Varnings regler i klassisk övervakning & aviseringar som kan migreras, kommer automatiskt att flyttas av Microsoft till motsvarande i den nya Azure Monitor-plattformen i faser som sträcker sig över några veckor. Processen kommer att vara sömlös utan drift avbrott och kunder har ingen förlust av övervaknings täckning.
- Aviserings regler som har flyttats till den nya aviserings plattformen tillhandahåller övervaknings täckning som tidigare men kommer att utlösa aviseringar med nya nytto laster. Alla e-postadresser, webhook-slutpunkter eller Logic app-länkar som är kopplade till den klassiska varnings regeln överförs vid migrering, men kanske inte fungerar korrekt eftersom aviserings nytto lasten är annorlunda i den nya plattformen.
- Vissa [klassiska varnings regler som inte kan migreras automatiskt](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) och kräver manuell åtgärd från användarna kommer att fortsätta att köras.

> [!IMPORTANT]
> Microsoft Azure övervakare har distribuerats i faser- [verktyget för att frivilligt migrera](alerts-using-migration-tool.md) sina klassiska varnings regler till den nya plattformen snart. Och kör det genom att tvinga alla klassiska aviserings regler som fortfarande finns och som kan migreras. Kunder måste se till att den klassiska varnings regel nytto lasten är anpassad för att hantera den nya nytto lasten från [enhetliga mått och aviseringar i Application Insights](#unified-metrics-and-alerts-in-application-insights) eller [enhetliga mått och aviseringar för andra Azure-resurser](#unified-metrics-and-alerts-for-other-azure-resources), efter migrering av de klassiska varnings reglerna. Mer information finns i [förbereda för klassisk migrering av aviserings regel](alerts-prepare-migration.md)

Den här artikeln uppdateras kontinuerligt med länkar & information om de nya funktionerna för Azure Monitoring-& avisering, samt tillgänglighet för verktyg för att hjälpa användarna att införa den nya Azure Monitor-plattformen.

## <a name="pricing-for-migrated-alert-rules"></a>Priser för migrerade varnings regler

Vi distribuerar ett Migreringsverktyg för att hjälpa dig att migrera dina Azure Monitor [klassiska aviseringar](./alerts-classic.overview.md) till den nya aviserings upplevelsen. De migrerade varnings reglerna och motsvarande migrerade åtgärds grupper (e-post, webhook eller LogicApp) är kostnads fria. De funktioner som du har haft med klassiska aviseringar, inklusive möjligheten att redigera tröskeln, sammansättnings typ och agg regerings kornig het, fortsätter att vara tillgängliga kostnads fritt med den migrerade varnings regeln. Men om du redigerar den migrerade varnings regeln för att använda någon av de nya varnings plattforms funktionerna, meddelanden eller åtgärds typer, kommer en motsvarande avgift att gälla. Mer information om priser för varnings regler och aviseringar finns i [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

Följande är exempel på fall där du får en avgift för aviserings regeln:

- Alla nya aviserings regler (ej migrerade) som skapats utanför kostnads fria enheter, på den nya Azure Monitors plattformen
- Alla data som samlas in och behålls utöver de kostnads fria enheterna som ingår i Azure Monitor
- Alla webbtester med flera test som körs av Application Insights
- Alla anpassade mått som lagras bortom kostnads fria enheter som ingår i Azure Monitor
- Eventuella migrerade varnings regler som redige ras för att använda nya mått, t. ex. frekvens, flera resurser/dimensioner, [dynamiska tröskelvärden](alerts-dynamic-thresholds.md), ändring av resurs/signal och så vidare.
- Alla migrerade åtgärds grupper som redige ras för att använda nyare meddelanden eller åtgärds typer som SMS, röst samtal och/eller ITSM-integrering.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den [nya enhetliga Azure Monitor](../overview.md).
* Läs mer om de nya [Azure-aviseringarna](./alerts-overview.md).

