---
title: Anpassa en varumärke modell med Video Indexer webbplats
titleSuffix: Azure Media Services
description: Lär dig hur du anpassar en-modell med Video Indexer webbplats.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: a2de9dbb479f43d6b646cd9f6cf604d6a08c8b6a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586119"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Anpassa en varumärke modell med Video Indexer webbplats

Video Indexer stöder varumärkes identifiering från tal-och visuell text vid indexering och Omindexering av video-och ljud innehåll. Funktionen varumärkes identifiering identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bing-databasens varumärken. Om Microsoft till exempel nämns i video-eller ljud innehåll, eller om det visas i visuell text i en video, identifierar Video Indexer det som ett varumärke i innehållet.

Med en anpassad varumärke modell kan du:

- Välj om du vill att Video Indexer ska identifiera varumärken från Bing varumärkes-databasen.
- Välj om du vill Video Indexer undanta vissa varumärken från att identifieras (i princip skapar du en nekad lista över varumärken).
- Välj om du vill att Video Indexer ska ta med varumärken som ska ingå i din modell som kanske inte finns i Bing-databasens varumärken (i grunden skapa en lista över varumärken).

En detaljerad översikt finns i den här [översikten](customize-brands-model-overview.md).

Du kan använda Video Indexer webbplats för att skapa, använda och redigera anpassade varumärke modeller som identifieras i en video, enligt beskrivningen i det här avsnittet. Du kan också använda API: et enligt beskrivningen i [Anpassa varumärkes-modellen med hjälp av API: er](customize-brands-model-with-api.md).

> [!NOTE]
> Om videon var indexerad innan du lade till ett varumärke måste du indexera om den. Du hittar objektet **Indexera** om på den nedrullningsbara menyn som är kopplad till videon. Välj **Avancerade alternativ**  ->  **varumärkes kategorier** och markera **Alla varumärken**.

## <a name="edit-brands-model-settings"></a>Redigera inställningar för varumärkes modell

Du har möjlighet att ange om du vill att märken från Bing-databasen ska identifieras eller inte. Om du vill ange det här alternativet måste du redigera inställningarna för din varumärkes-modell. Gör så här:

1. Gå till [video Indexer](https://www.videoindexer.ai/) webbplats och logga in.
1. Om du vill anpassa en modell i ditt konto väljer du knappen **anpassning av innehålls modell** till vänster på sidan.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Anpassa innehålls modell i Video Indexer":::
1. Om du vill redigera varumärken väljer du fliken **varumärken** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="Skärm bild som visar fliken varumärken i dialog rutan anpassning av innehålls modell":::
1. Markera alternativet **Visa varumärken som föreslås av Bing** om du vill att video Indexer ska identifiera varumärken som föreslås av Bing – lämna alternativet avmarkerat om du inte vill det.

## <a name="include-brands-in-the-model"></a>Inkludera varumärken i modellen

Avsnittet **Inkludera varumärken** representerar anpassade varumärken som du vill video Indexer identifiera, även om de inte föreslås av Bing.  

### <a name="add-a-brand-to-include-list"></a>Lägg till ett märke att ta med i listan

1. Välj **+ Skapa nytt varumärke**.

    Ange ett namn (obligatoriskt), kategori (valfritt), beskrivning (valfritt) och referens-URL (valfritt).
    Kategori fältet är avsett att hjälpa dig att tagga dina varumärken. Det här fältet visas som märkes *taggar* när du använder video Indexer-API: er. Till exempel kan märket "Azure" märkas eller kategoriseras som "moln".

    Fältet referens-URL kan vara en referens webbplats för varumärket (som en länk till dess wikipedia-sida).

2. Välj **Spara** så ser du att varumärket har lagts till i listan **Inkludera märken** .

### <a name="edit-a-brand-on-the-include-list"></a>Redigera ett varumärke på listan ta med

1. Välj Penn ikonen bredvid det märke som du vill redigera.

    Du kan uppdatera kategori, beskrivning eller referens-URL för ett varumärke. Du kan inte ändra namnet på ett varumärke eftersom namn på varumärken är unika. Om du behöver ändra märkes namnet tar du bort hela varumärket (se nästa avsnitt) och skapar ett nytt varumärke med det nya namnet.

2. Välj knappen **Uppdatera** för att uppdatera varumärket med den nya informationen.

### <a name="delete-a-brand-on-the-include-list"></a>Ta bort ett varumärke i listan ta med

1. Välj pappers korgs ikonen bredvid det märke som du vill ta bort.
2. Välj **ta bort** och varumärket visas inte längre i listan *med inkluderade märken* .

## <a name="exclude-brands-from-the-model"></a>Uteslut varumärken från modellen

Avsnittet **exkludera varumärken** representerar de varumärken som du inte vill att video Indexer identifiera.

### <a name="add-a-brand-to-exclude-list"></a>Lägg till ett varumärke i uteslutnings listan

1. Välj **+ Skapa nytt varumärke.**

    Ange ett namn (obligatoriskt), kategori (valfritt).

2. Välj **Spara** så ser du att varumärket har lagts till i listan *Uteslut varumärken* .

### <a name="edit-a-brand-on-the-exclude-list"></a>Redigera ett varumärke i uteslutnings listan

1. Välj Penn ikonen bredvid det märke som du vill redigera.

    Du kan bara uppdatera kategorin för ett varumärke. Du kan inte ändra namnet på ett varumärke eftersom namn på varumärken är unika. Om du behöver ändra märkes namnet tar du bort hela varumärket (se nästa avsnitt) och skapar ett nytt varumärke med det nya namnet.

2. Välj knappen **Uppdatera** för att uppdatera varumärket med den nya informationen.

### <a name="delete-a-brand-on-the-exclude-list"></a>Ta bort ett varumärke i uteslutnings listan

1. Välj pappers korgs ikonen bredvid det märke som du vill ta bort.
2. Välj **ta bort** och varumärket visas inte längre i listan med *undantags märken* .

## <a name="next-steps"></a>Nästa steg

[Anpassa varumärkes-modellen med API: er](customize-brands-model-with-api.md)
