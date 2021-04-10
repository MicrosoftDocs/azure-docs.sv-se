---
title: Vad är tjänsten Metrics Advisor?
titleSuffix: Azure Cognitive Services
description: Vad är Metrics Advisor?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: dfdd7286013bbb6462fb8e5b1bdf52e6ed738029
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384688"
---
# <a name="what-is-metrics-advisor-preview"></a>Vad är Metrics Advisor (för hands version)? 

Mått Advisor är en del av Azure Cognitive Services som använder AI för att utföra data övervakning och avvikelse identifiering i Time Series-data. Tjänsten automatiserar processen att tillämpa modeller på dina data och tillhandahåller en uppsättning API: er och en webbaserad arbets yta för data inmatning, avvikelse identifiering och diagnostik – utan att behöva känna till Machine Learning. Utvecklare kan bygga AIOps-, predicative-underhålls-och Business Monitor-program ovanpå tjänsten. Använd Metrics Advisor för att:

* Analysera flerdimensionella data från flera data källor
* Identifiera och korrelera avvikelser
* Konfigurera och finjustera den avvikelse identifierings modell som används på dina data
* Diagnostisera avvikelser och hjälp med rotor Saks analys

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Översikt över Metrics Advisor":::

Den här dokumentationen innehåller följande typer av artiklar:
* [Snabb starterna](./Quickstarts/web-portal.md) är steg-för-steg-instruktioner som gör att du kan anropa tjänsten och få resultat under en kort tids period. 
* [Instruktions guiderna](./how-tos/onboard-your-data.md) innehåller instruktioner för att använda tjänsten på mer exakta eller anpassade sätt.
* De [konceptuella artiklarna](glossary.md) innehåller djupgående förklaringar av tjänstens funktioner och funktioner.

## <a name="connect-to-a-variety-of-data-sources"></a>Anslut till en mängd olika data källor

Metrics Advisor kan ansluta till och [mata in flerdimensionella mått](how-tos/onboard-your-data.md) data från många data lager, inklusive: SQL Server, Azure Blob Storage, MongoDB och mycket annat.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Lättanvänd och anpassningsbar avvikelse identifiering

* Mått Advisor väljer automatiskt den bästa modellen för dina data utan att behöva känna till någon maskin inlärning.
* Övervaka automatiskt varje tids serie i [flerdimensionella mått](glossary.md#multi-dimensional-metric).
* Använd [parameter justering](how-tos/configure-metrics.md) och [interaktiv feedback](how-tos/anomaly-feedback.md) för att anpassa modellen som används på dina data och framtida avvikelse identifierings resultat.

## <a name="real-time-alerts-through-multiple-channels"></a>Real tids aviseringar via flera kanaler

När avvikelser upptäcks kan du [Skicka real tids aviseringar](how-tos/alerts.md) via flera kanaler med hjälp av hookar, till exempel: e-posthooker, Webhooks och Azure DevOps-hookar. Med flexibla aviserings regler kan du anpassa vilka aviseringar som ska skickas och deras mål.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Smart Diagnostic Insights genom att analysera avvikelser

Analysera avvikelser som har identifierats på flerdimensionella mått och generera [Smart Diagnostic Insights](how-tos/diagnose-incident.md) inklusive de flesta sannolika rotor saken, diagnostiska träd, mått borrning och mycket annat. Genom att konfigurera [mått diagram](how-tos/metrics-graph.md)kan du aktivera kors mått analys som hjälper dig att visualisera incidenter.


## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbets flödet är enkelt: när du har registrerat dina data kan du finjustera avvikelse identifieringen och skapa konfigurationer som passar ditt scenario.

1. [Skapa en Azure-resurs](https://go.microsoft.com/fwlink/?linkid=2142156) för Metrics Advisor. 
2. Bygg din första övervakare med hjälp av webb portalen.
    1. Registrera dina data
    2. Finjustera avvikelse identifiering
    3. Prenumerera på aviseringar
    4. Visa diagnostiska insikter
3. Använd REST API för att anpassa din instans.

## <a name="next-steps"></a>Nästa steg

* Utforska en snabb start: [övervaka ditt första mått på webben](quickstarts/web-portal.md).
* Utforska en snabb start: [Använd REST-API: er för att anpassa din lösning](./quickstarts/rest-api-and-client-library.md).
