---
title: Anpassa en varumärke modell i Video Indexer – Azure
titleSuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad som är en modell av en varumärkes-modell i Video Indexer och hur du anpassar den.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586082"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Anpassa en modell med ett varumärke i Video Indexer

Video Indexer stöder varumärkes identifiering från tal-och visuell text vid indexering och Omindexering av video-och ljud innehåll. Funktionen varumärkes identifiering identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bing-databasens varumärken. Om Microsoft till exempel nämns i ett video-eller ljud innehåll eller om det visas i visuell text i en video identifierar Video Indexer det som ett varumärke i innehållet. Varumärken är disambiguated från andra termer som använder kontext.

Varumärkes identifiering är användbart i en mängd olika affärs scenarier, till exempel innehålls Arkiv och identifiering, sammanhangsbaserad annonsering, analys av sociala medier, analys av detalj handeln och många fler. Med Video Indexer varumärkes identifiering kan du indexera varumärkes omnämnanden i tal-och visuell text med hjälp av Bing-databaser och med anpassning genom att skapa en anpassad varumärke modell för varje Video Indexer-konto. Med funktionen för anpassade varumärke modeller kan du välja om Video Indexer ska identifiera varumärken från Bing-databaserna, undanta vissa märken från att identifieras (i princip skapar vi en lista över ej godkända varumärken) och inkluderar varumärken som ska ingå i din modell som kanske inte finns i Bing-databasen (i grunden skapa en lista över godkända varumärken). Den anpassade varumärke modell som du skapar kommer bara att vara tillgänglig i det konto som du skapade modellen i.

## <a name="out-of-the-box-detection-example"></a>Exempel på Box-identifiering

I presentationen "Microsoft build 2017 Day 2" visas varumärket "Microsoft Windows" flera gånger. Ibland i avskriften, ibland som visuell text och aldrig som orda Grant. Video Indexer identifierar med hög precision som en term är helt beroende av sammanhanget, som täcker över 90k varumärken och ständigt uppdaterar. Vid 02:25 identifierar Video Indexer varumärket från tal och sedan igen vid 02:40 från visuell text, som är en del av Windows-logotypen.

![Översikt över varumärken](./media/content-model-customization/brands-overview.png)

Om du pratar om Windows i samband med bygge identifieras inte ordet "Windows" som ett varumärke, samma för Box, Apple, Fox osv., baserat på avancerade Machine Learning algoritmer som vet hur man disambiguate från kontexten. Varumärkes identifiering fungerar för alla våra språk som stöds.  

## <a name="next-steps"></a>Nästa steg

Ta en titt på följande avsnitt för att ta med dina egna varumärken:

[Anpassa varumärkes-modellen med API: er](customize-brands-model-with-api.md)

[Anpassa varumärkes-modellen med hjälp av webbplatsen](customize-brands-model-with-website.md)
