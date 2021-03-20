---
title: Exempel på användar scenarier för API för textanalys
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln för att se några vanliga scenarier för att integrera API för textanalys i dina tjänster och processer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: a4c551a6e9b8d79fdeb7b5219f311c7c7c969757
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97505366"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Exempel på användar scenarier för API för textanalys

API för textanalys är en molnbaserad tjänst som tillhandahåller avancerad bearbetning av naturligt språk över text. I den här artikeln beskrivs några exempel på användnings fall för integrering av API i dina affärs lösningar och processer. 

## <a name="analyze-survey-results"></a>Analysera undersöknings resultat

Skapa insikter från resultat från kund-och personal undersökningar genom att bearbeta de obehandlade text svaren med Attitydanalys. Sammanställ resultaten för analys, uppföljning och drivande engagemang.

![En bild som beskriver hur du utför sentiment analys av enkäter för kunder och anställda.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analysera registrerade inkommande kund samtal

Extrahera insikter från kund service samtal med tal-till-text, Attitydanalys och Extrahering av diskussionsämne. Visa resultaten på Power BI instrument panel eller på en portal för att bättre förstå kunder, markera kund tjänst trender och driva kund engagemang. Skicka API-begäranden som en batch för rapportering eller i real tid för ingripande. Se exempel koden [på GitHub](https://github.com/rlagh2/callcenteranalytics).

![En bild som beskriver hur du automatiserar insikter från kund service samtal med sentiment-analys](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Bearbeta och kategorisera support ärenden

Använd Extrahering av diskussionsämne och enhets igenkänning för att bearbeta support förfrågningar som skickats i ostrukturerat text format. Använd de extraherade fraserna och entiteterna för att kategorisera förfrågningarna för resurs planering och trend analys.

![En bild som beskriver hur du använder extrahering av nyckel fraser och enhets igenkänning för att kategorisera incident rapporter och trender](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Övervaka din produkts sociala media-flöden

Övervaka feedback från användarnas produkter på din produkts Twitter-eller Facebook-sida. Använd data för att analysera kund sentiment mot nya produkter, extrahera viktiga fraser om funktioner och funktions förfrågningar, eller ta itu med kund klagomål när de sker. Se exempel [mal len Microsoft Power automatisera mall](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![En bild som beskriver hur du övervakar din produkt och företagets feedback på sociala medier med extrahering av nyckel fraser](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Klassificera och bortredigering-dokument med känslig information

Använd namngiven enhets igenkänning för att identifiera personlig och känslig information i dokument. Använd data för att klassificera dokument eller redigera dem så att de kan delas på ett säkert sätt.

![En bild som beskriver hur du använder NER för att identifiera personlig information och klassificera och bortredigering dokument](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>Följ utåsikts utvinning

Gruppera åsikter som rör specifika aspekter av en produkt eller tjänst i undersökningar, kundfeedback eller där text innehåller ett yttrande om en aspekt. Använd den för att hjälpa dig att hjälpa dig att hjälpa dig att få hjälp med produkt lansering och förbättringar, marknadsförings åtgärder eller att markera hur produkten eller tjänsten fungerar 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="Exempel på ett hotell.":::

## <a name="next-steps"></a>Nästa steg

* [Vad är API för textanalys?](overview.md)
* [Skicka en begäran till API för textanalys med hjälp av klient biblioteket](quickstarts/client-libraries-rest-api.md)
