---
title: 'Vad är API: t för avvikelse detektor Multivarierad?'
titleSuffix: Azure Cognitive Services
description: 'Översikt över nya avvikelser för multivarierad-API: er i offentlig för hands version.'
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: avvikelse identifiering, maskin inlärning, algoritmer
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315669"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Multivarierad Time Series-avvikelse identifiering (offentlig för hands version)

Den första versionen av Azure Cognitive Services avvikelse detektorn gör att du kan bygga mått övervaknings lösningar med hjälp av de lättanvända [API: erna för univariate Time Series-avvikelser](overview.md). Genom att tillåta analys av tids serier individuellt ger avvikelser detektor univariate enkelhet och skalbarhet.

Med de nya API: erna för **avvikelse identifiering i multivarierad** kan utvecklare enkelt integrera avancerad AI för att identifiera avvikelser från grupper av mått, utan att det behövs några kunskaper om maskin inlärning eller märkta data. Beroenden och Inter-korrelationer mellan upp till 300 olika signaler räknas nu automatiskt som viktiga faktorer. Den här nya funktionen hjälper dig att proaktivt skydda dina komplexa system, till exempel program vara, servrar, fabriks datorer, rymd farkoster eller till och med ditt företag, från haverier.

Föreställ dig 20 sensorer från en automatisk motor som genererar 20 olika signaler som vibration, temperatur, bränsle tryck osv. Avläsningarna av dessa signaler individuellt kanske inte meddelar dig mycket om system nivå problem, men tillsammans kan de representera motorns hälso tillstånd. När interaktionen mellan dessa signaler avviker utanför det vanliga intervallet, kan funktionen för avvikelse identifiering i multivarierad upptäcka avvikelsen som en förfallen expert. De underliggande AI-modellerna tränas och anpassas med hjälp av dina data så att de förstår företagets unika behov. Med de nya API: erna i avvikelse detektor kan utvecklare nu enkelt integrera multivarierad Time Series-identifierings funktioner i förebyggande underhålls lösningar, AIOps övervaknings lösningar för komplex företags program vara eller Business Intelligence verktyg.

## <a name="when-to-use-multivariate-versus-univariate"></a>När du ska använda **multivarierad** respektive **univariate**

Använd API: er för avvikelse identifiering i univariate om målet är att identifiera avvikelser från ett normalt mönster i varje enskild tids serie, baserat på sina egna historiska data. Exempel: du vill kunna identifiera dagliga intäkts avvikelser baserat på själva intäkts data, eller om du vill identifiera CPU-insamling rent baserat på CPU-data.
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Tids serie linje diagram med en enda variabel som fluktuerar värden som har registrerats av en blå linje med avvikelser som identifieras av orange cirklar](./media/anomaly_detection2.png)

Använd API: er för multivarierad avvikelse identifiering nedan om målet är att identifiera avvikelser på system nivå från en grupp tids serie data. I synnerhet när en enskild tids serie inte meddelar dig mycket, och du måste titta på alla signaler (en grupp tids serier) holistiskt för att fastställa ett problem på system nivå. Exempel: du har en dyr fysisk till gång, t. ex. flyg plan, utrustning på en olje anläggning eller satellit. Var och en av dessa till gångar har flera olika typer av sensorer. Du skulle behöva titta på alla dessa tids serie signaler från dessa sensorer för att avgöra om det finns problem på system nivå.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Linje diagram med flera tids serier för variabler av: vibration, temperatur, tryck, hastighet, rotations hastighet med avvikelser markerade i orange](./media/multivariate-graph.png)

## <a name="region-support"></a>Stöd för regioner

Den offentliga för hands versionen av multivarierad för avvikelse detektor är för närvarande tillgänglig i tre regioner: västra 2; USA, östra 2; USA och Västeuropa.

## <a name="algorithms"></a>Algoritmer

- [Multivarierad Time Series-avvikelse identifiering via diagram Attention-nätverk](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Delta i communityn för avvikelseidentifiering

- Gå med i [gruppen avvikelser detektorer i Microsoft Teams](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Nästa steg

- [Snabb starter](./quickstarts/client-libraries-multivariate.md).
- [Bästa praxis](./concepts/best-practices-multivariate.md): den här artikeln innehåller rekommenderade mönster som används med Multivarierad-API: er.
