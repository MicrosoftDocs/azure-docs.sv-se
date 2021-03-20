---
title: Sök efter exakt moment i videor med Video Indexer
titleSuffix: Azure Media Services
description: Lär dig hur du söker efter exakta moment i videor med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96030526"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Sök efter exakt moment i videor med Video Indexer

Det här avsnittet visar hur du använder Video Indexer webbplats för att söka efter exakta moment i videor.

1. Gå till [video Indexer](https://www.videoindexer.ai/) webbplats och logga in.
1. Ange sökorden och sökningen kommer att utföras bland alla videor i ditt konto bibliotek. 

    Du kan filtrera sökningen genom att välja **filter**. I följande exempel söker vi efter "Microsoft" som endast visas som en text på skärmen (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filter, endast text":::
1. Tryck på **Sök** för att visa resultatet.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Video Sök Resultat":::

    Om du väljer ett av resultaten ger spelaren dig det exakta tillfället i videon.
1. Visa och Sök i de sammanfattande insikterna av videon genom att klicka på **spela upp** på videon eller välja ett av dina ursprungliga Sök resultat. 

    Du kan visa, söka och redigera **insikter**. När du väljer en av insikterna ger spelaren dig den exakta tidpunkten i videon.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Visa, Sök och redigera insikter för videon":::

    Om du bäddar in videon via Video Indexer widgetar kan du nå Player/Insights-vyn och synkroniseringen i din app. Mer information finns i [bädda in video Indexer-widgetar i din app](video-indexer-embed-widgets.md).
1. Du kan visa, söka och redigera avskrifterna genom att klicka på fliken **tids linje** . 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Visa, Sök och redigera avskrifterna av videon":::

    Redigera texten genom att välja **Redigera** i det övre högra hörnet och ändra texten efter behov. 

    Du kan också översätta och hämta avskrifterna genom att välja lämpligt alternativ i det övre högra hörnet. 

## <a name="embed-download-create-projects"></a>Bädda in, Hämta, skapa projekt

Du kan bädda in videon genom att välja **</>bädda in** under videon. Mer information finns i [bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md).

Du kan ladda ned käll videon, insikter om videon, avskrifter genom att klicka på **Ladda ned** under videon.

Du kan skapa ett klipp baserat på din video av vissa rader och en stund genom att klicka på **Öppna i redigeraren**. Redigera sedan videon och spara projektet. Mer information finns i [använda dina videors djup insikter](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Bädda in, hämta och skapa projekt för videon":::

## <a name="next-steps"></a>Nästa steg

[Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
