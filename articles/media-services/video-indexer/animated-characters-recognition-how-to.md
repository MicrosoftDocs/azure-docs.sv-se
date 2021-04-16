---
title: Identifiering av animerade tecken med Video Indexer hur man
titleSuffix: Azure Media Services
description: Det här visar hur du använder identifiering av animerade tecken med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: e80699a043d4c18a1bc7ba75ce58c6972fae0fad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531560"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Använda identifiering av animerade tecken (förhandsversion) med portalen och API 

Azure Media Services Video Indexer har stöd för identifiering, gruppering och igenkänning av tecken i animerat innehåll är den här funktionen tillgänglig via Azure Portal och via API. Läs [det här översiktsavsnittet.](animated-characters-recognition.md)

Den här artikeln visar hur du använder identifiering av animerade tecken med Azure Portal och Video Indexer-API:et.

## <a name="use-the-animated-character-detection-with-portal"></a>Använda identifiering av animerade tecken med portalen 

I utvärderingskontona hanteras Custom Vision av en Video Indexer kan du börja skapa och använda modellen med animerade tecken. Om du använder utvärderingskontot kan du hoppa över följande avsnitt ("Anslut Custom Vision ditt konto").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Anslut ditt Custom Vision konto (endast betalkonton)

Om du äger Video Indexer ett betalt konto måste du först ansluta Custom Vision konto. Om du inte redan har ett Custom Vision skapar du ett. Mer information finns i [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Båda kontona måste finnas i samma region. Den Custom Vision integreringen stöds för närvarande inte i Japan-regionen.

Betalkonton som har åtkomst till sina Custom Vision kan se modeller och taggade bilder där. Läs mer om [att förbättra klassificeraren i Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). 

Observera att träningen av modellen endast ska göras via Video Indexer och inte via Custom Vision webbplats. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Ansluta ett Custom Vision konto med API 

Följ dessa steg om du Custom Vision ett konto till Video Indexer eller ändra det Custom Vision-konto som för närvarande är anslutet till Video Indexer:

1. Bläddra till [www.customvision.ai](https://www.customvision.ai) och logga in.
1. Kopiera nycklarna för resurserna Träning och Förutsägelse:

    > [!NOTE]
    > Om du vill ange alla nycklar måste du ha två separata resurser i Custom Vision, en för träning och en för förutsägelse.
1. Ange annan information:

    * Slutpunkt 
    * Resurs-ID för förutsägelse
1. Bläddra och logga in på [Video Indexer](https://vi.microsoft.com/).
1. Klicka på frågetecknet i det övre högra hörnet på sidan och välj **API-referens.**
1. Se till att du prenumererar på API Management genom att klicka **på fliken** Produkter. Om du har ett API anslutet kan du fortsätta till nästa steg, annars prenumererar du. 
1. I utvecklarportalen klickar du på Complete **API Reference (Fullständig API-referens)** och bläddrar till **Åtgärder**.  
1. Välj **Anslut Custom Vision konto (FÖRHANDSVERSION) och** klicka på **Prova.**
1. Fyll i de obligatoriska fälten samt åtkomsttoken och klicka på **Skicka**. 

    Mer information om hur du hämtar Video Indexer åtkomsttoken finns i [utvecklarportalen](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)och i [relevant dokumentation.](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)  
1. När anropet returnerar svaret 200 OK är ditt konto anslutet.
1. Så här verifierar du anslutningen genom att gå [till Video Indexer](https://vi.microsoft.com/)) portal:
1. Klicka på knappen **Anpassning av innehållsmodell** i det övre högra hörnet.
1. Gå till fliken **Animerade** tecken.
1. När du klickar på Hantera modeller i Custom Vision överförs du till det Custom Vision-konto som du precis har anslutit.

> [!NOTE]
> För närvarande stöds endast modeller som har skapats Video Indexer enheter. Modeller som skapas via Custom Vision kommer inte att vara tillgängliga. Dessutom är det bästa sättet att redigera modeller som har skapats via Video Indexer endast via Video Indexer-plattformen, eftersom ändringar som görs via Custom Vision kan orsaka oönskade resultat.

### <a name="create-an-animated-characters-model"></a>Skapa en modell med animerade tecken

1. Gå till [Video Indexer](https://vi.microsoft.com/)-webbplatsen och logga in.
1. Om du vill anpassa en modell i ditt konto väljer **du knappen Anpassning av** innehållsmodell till vänster på sidan.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Anpassa innehållsmodellen i Video Indexer":::
1. Gå till fliken **Animerade tecken** i modellanpassningsavsnittet.
1. Klicka på **Lägg till modell.**
1. Namnge din modell och klicka på Retur för att spara namnet.

> [!NOTE]
> Det bästa sättet är att ha en Custom Vision-modell för varje animerad serie. 

### <a name="index-a-video-with-an-animated-model"></a>Indexera en video med en animerad modell

Ladda upp minst två videor under den första träningen. Varje bör helst vara längre än 15 minuter innan du förväntar dig en bra igenkänningsmodell. Om du har kortare avsnitt rekommenderar vi att du laddar upp minst 30 minuters videoinnehåll före träningen. På så sätt kan du sammanslå grupper som tillhör samma tecken från olika scener och bakgrunder och därmed öka chansen att det identifierar tecknet i följande avsnitt som du indexerar. Om du vill träna en modell på flera videor (avsnitt) måste du indexera dem alla med samma animeringsmodell. 

1. Klicka på knappen **Ladda** upp.
1. Välj en video att ladda upp (från en fil eller en URL).
1. Klicka på **Avancerade alternativ.**
1. Under **Personer/Animerade tecken väljer** du **Animeringsmodeller.**
1. Om du har en modell väljs den automatiskt, och om du har flera modeller kan du välja den relevanta från den nedrullningsbara menyn.
1. Klicka på ladda upp.
1. När videon har indexerats visas identifierade tecken i avsnittet **Animerade** tecken i **fönstret** Insikter.

Innan du taggar och tränar modellen får alla animerade tecken namnet "Unknown #X". När du har tränat modellen identifieras de också.

### <a name="customize-the-animated-characters-models"></a>Anpassa modeller med animerade tecken

1. Namnge tecknen i Video Indexer.

    1. När modellen har skapat teckengruppen rekommenderar vi att du granskar dessa grupper i Custom Vision. 
    1. Om du vill tagga ett animerat tecken i videon går du till fliken **Insikter** och klickar på knappen Redigera i   det övre högra hörnet av ****   fönstret. 
    1. I fönstret **Insikter** klickar du på något av de identifierade animerade tecknen och ändrar namnen från "Okänd #X" till ett tillfälligt namn (eller namnet som tidigare tilldelades   till tecknet). 
    1. När du har skrivit det nya namnet klickar du på kryssikonen bredvid det nya namnet. Det nya namnet sparas i modellen i Video Indexer. 
1. Endast betalkonton: Granska grupperna i Custom Vision 

    > [!NOTE]
    > Betalkonton som har åtkomst till sina Custom Vision kan se modeller och taggade bilder där. Läs mer om [att förbättra klassificeraren i Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). Det är viktigt att observera att träning av modellen endast ska göras via Video Indexer (enligt beskrivningen i denna topid) och inte via Custom Vision webbplats. 

    1. Gå till sidan **Anpassade modeller** i Video Indexer och välj fliken **Animerade** tecken. 
    1. Klicka på knappen Redigera för den modell som du arbetar med för att hantera den i Custom Vision. 
    1. Granska varje teckengrupp: 

        * Om gruppen innehåller orelaterade bilder rekommenderar vi att du tar bort dem på Custom Vision webbplats. 
        * Om det finns bilder som tillhör ett annat tecken ändrar du taggen för dessa bilder genom att klicka på bilden, lägga till rätt tagg och ta bort fel tagg. 
        * Om gruppen inte är korrekt, vilket innebär att den främst innehåller bilder som inte är tecken eller bilder från flera tecken, kan du ta bort i på Custom Vision webbplats eller i Video Indexer insikter. 
        * Grupperingsalgoritmen delar ibland upp dina tecken i olika grupper. Därför rekommenderar vi att du ger alla grupper som tillhör samma tecken samma namn (i Video Indexer Insights), vilket omedelbart gör att alla dessa grupper visas som på Custom Vision webbplats. 
    1. När gruppen har förfinats kontrollerar du att det ursprungliga namnet som du taggade den med återspeglar tecknet i gruppen. 
1. Träna modellen 

    1. När du har redigerat alla namn du vill ha måste du träna modellen. 
    1. När ett tecken har tränats in i modellen identifieras det nästa video som indexeras med den modellen. 
    1. Öppna anpassningssidan och klicka på fliken **Animerade tecken** och klicka sedan på knappen   Träna **för** att träna din modell. För att behålla anslutningen mellan video 
    
Indexeraren och modellen tränar inte modellen på Custom Vision webbplats (betalkonton har åtkomst till Custom Vision webbplats), endast i Video Indexer. När den har tränats identifierar alla videor som indexeras eller indexeras om med den modellen de tränade tecknen. 

## <a name="delete-an-animated-character-and-the-model"></a>Ta bort ett animerat tecken och modellen

1. Ta bort ett animerat tecken.

    1. Om du vill ta bort ett animerat tecken i  dina videoinsikter går du till fliken **Insikter** och klickar på knappen Redigera i det övre högra hörnet av fönstret.
    1. Välj det animerade tecknet och klicka sedan på knappen **Ta bort** under namnet.

    > [!NOTE]
    > Då tas insikterna bort från den här videon, men modellen påverkas inte.
1. Ta bort en modell.

    1. Klicka på knappen **Anpassning av innehållsmodell** på den översta menyn och gå till **fliken Animerade** tecken.
    1. Klicka på ellipsikonen till höger om den modell som du vill ta bort och klicka sedan på borttagningsknappen.
    
    * Betalt konto: Modellen kopplas bort från Video Indexer och du kommer inte att kunna återansluta den.
    * Utvärderingskonto: Modellen tas även bort från Vision för synen. 
    
        > [!NOTE]
        > I ett utvärderingskonto har du bara en modell som du kan använda. När du har tagit bort den kan du inte träna andra modeller.

## <a name="use-the-animated-character-detection-with-api"></a>Använda identifiering av animerade tecken med API 

1. Anslut ett Custom Vision konto.

    Om du äger ett Video Indexer betalkonto måste du först ansluta ett Custom Vision konto. <br/>
    Om du inte redan har ett Custom Vision skapar du ett. Mer information finns i [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

    [Anslut ditt Custom Vision-konto med hjälp av API: et](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account).
1. Skapa en modell med animerade tecken.

    Använd [API:et för att skapa animeringsmodell.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model)
1. Indexera eller indexera om en video.

    Använd [API:et för omindexering.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) 
1. Anpassa modeller med animerade tecken.

    Använd [API:et för att träna animeringsmodellen.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model)

### <a name="view-the-output"></a>Visa utdata

Se animerade tecken i den genererade JSON-filen.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Begränsningar

* För närvarande stöds inte funktionen "animeringsidentifiering" i East-Asia region.
* Tecken som verkar vara små eller långt i videon kanske inte identifieras korrekt om videons kvalitet är dålig.
* Rekommendationen är att använda en modell per uppsättning animerade tecken (till exempel per animerad serie).

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)