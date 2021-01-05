---
title: Animerat teckensnitts identifiering med Video Indexer How to
titleSuffix: Azure Media Services
description: Det här visar hur du använder animerad identifiering av bokstäver med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 1ee179efbe936c742f1eb51b998c10f9349c14fb
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763395"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Använda animerad Character Detection (för hands version) med Portal och API 

Azure Media Services Video Indexer stöder identifiering, gruppering och igenkänning av tecken i animerat innehåll är den här funktionen tillgänglig via Azure Portal och via API. Läs [det här översikts](animated-characters-recognition.md) avsnittet.

Den här artikeln visar hur du använder det animerade Character-identifieringen med Azure Portal och Video Indexer-API: et.

## <a name="use-the-animated-character-detection-with-portal"></a>Använda animerat teckensnitts identifiering med portalen 

I utvärderings kontoren hanteras Custom Vision-integrering av Video Indexer, och du kan börja skapa och använda den animerade tecken modellen. Om du använder ett utvärderings konto kan du hoppa över följande avsnitt ("Anslut ditt Custom Vision konto").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Anslut ditt Custom Vision konto (endast betalda konton)

Om du äger ett Video Indexer betalt konto måste du först ansluta ett Custom Vision-konto. Om du inte redan har ett Custom Vision konto kan du skapa ett. Mer information finns i [Custom vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Båda kontona måste finnas i samma region. Custom Vision-integration stöds för närvarande inte i regionen Japan.

Betalda konton som har åtkomst till sitt Custom Vision konto kan se modeller och taggade bilder där. Lär dig mer om [att förbättra din klassificerare i Custom vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier). 

Observera att inlärningen av modellen endast bör göras via Video Indexer och inte via Custom Vision webbplats. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Ansluta ett Custom Vision-konto med API 

Följ dessa steg för att ansluta dig Custom Vision konto till Video Indexer eller ändra det Custom Vision-konto som är kopplat till Video Indexer:

1. Bläddra till [www.customvision.AI](https://www.customvision.ai) och logga in.
1. Kopiera nycklarna för utbildnings-och förutsägelse resurserna:

    > [!NOTE]
    > För att tillhandahålla alla nycklar måste du ha två separata resurser i Custom Vision, en för utbildning och en för förutsägelse.
1. Ange annan information:

    * Slutpunkt 
    * Resurs-ID för förutsägelse
1. Bläddra och logga in på [video Indexer](https://vi.microsoft.com/).
1. Klicka på frågetecknet i det övre högra hörnet på sidan och välj **API-referens**.
1. Se till att du prenumererar på API Management genom att klicka på fliken **produkter** . Om du har ett API anslutet kan du fortsätta till nästa steg, annars prenumerera. 
1. Klicka på den **fullständiga API-referensen** på Developer-portalen och gå till **åtgärder**.  
1. Välj **anslut Custom vision konto (FÖRhands granskning)** och klicka på **prova**.
1. Fyll i obligatoriska fält och åtkomsttoken och klicka på **Skicka**. 

    Om du vill ha mer information om hur du hämtar Video Indexer-åtkomsttoken går du till [Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)och läser [relevant dokumentation](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Ditt konto är anslutet när anropet returnerade svaret 200 OK.
1. Så här verifierar du din anslutning genom att bläddra till [video Indexer](https://vi.microsoft.com/)-portalen:
1. Klicka på **anpassnings knappen för innehålls modell** i det övre högra hörnet.
1. Gå till fliken **animerade tecken** .
1. När du klickar på Hantera modeller i Custom Vision överförs du till det Custom Vision-konto som du precis anslöt.

> [!NOTE]
> För närvarande stöds endast modeller som skapats via Video Indexer. Modeller som skapas via Custom Vision är inte tillgängliga. Vi rekommenderar dessutom att du redigerar modeller som har skapats via Video Indexer bara via Video Indexers plattformen, eftersom ändringar som görs via Custom Vision kan orsaka oönskade resultat.

### <a name="create-an-animated-characters-model"></a>Skapa en animerad tecken modell

1. Gå till [Video Indexer](https://vi.microsoft.com/)-webbplatsen och logga in.
1. Om du vill anpassa en modell i ditt konto väljer du knappen **anpassning av innehålls modell** till vänster på sidan.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Anpassa innehålls modell i Video Indexer":::
1. Gå till fliken **animerade tecken** i avsnittet modell anpassning.
1. Klicka på **Lägg till modell**.
1. Namn du modell och klicka på RETUR för att spara namnet.

> [!NOTE]
> Den bästa metoden är att ha en anpassad vision modell för varje animerad serie. 

### <a name="index-a-video-with-an-animated-model"></a>Indexera en video med en animerad modell

Ladda upp minst två videor för den inledande utbildningen. Vart och ett bör vara företräde längre än 15 minuter innan den förväntade igenkännings modellen förväntas. Om du har kortare avsnitt rekommenderar vi att du överför minst 30 minuters video innehåll innan du tränar. På så sätt kan du slå samman grupper som tillhör samma Character från olika scener och bakgrunder, och därmed öka chansen att identifiera tecknen i följande avsnitt som du indexerar. Om du vill träna en modell på flera videor (avsnitt) måste du indexera dem med samma animerings modell. 

1. Klicka på knappen **överför** .
1. Välj en video att ladda upp (från en fil eller en URL).
1. Klicka på **Avancerade alternativ**.
1. Välj **animerings modeller** under **personer/animerade tecken** .
1. Om du har en modell väljs den automatiskt, och om du har flera modeller kan du välja den som är relevant för den aktuella List menyn.
1. Klicka på överför.
1. När videon har indexerats visas identifierade tecken i avsnittet **animerade tecken** i fönstret **insikter** .

Innan du taggar och tränar modellen med varandra får alla animerade tecken namnet "okänd #X". När du har tränat modellen kommer de också att identifieras.

### <a name="customize-the-animated-characters-models"></a>Anpassa modeller för animerade tecken

1. Namnge tecknen i Video Indexer.

    1. När du har skapat en modell av typen modell, rekommenderar vi att du granskar dessa grupper i Custom Vision. 
    1. Om du vill tagga ett animerat kort i videon går du till fliken **insikter**   och klickar på knappen **Redigera**   i det övre högra hörnet i fönstret. 
    1. I fönstret **insikter**   klickar du på något av de synliga animerade tecknen och ändrar deras namn från "okänd #X" till ett tillfälligt namn (eller det namn som tidigare har tilldelats till tecknet). 
    1. När du har skrivit det nya namnet klickar du på kryss ikonen bredvid det nya namnet. Detta sparar det nya namnet i modellen i Video Indexer. 
1. Endast betalda konton: granska grupperna i Custom Vision 

    > [!NOTE]
    > Betalda konton som har åtkomst till sitt Custom Vision konto kan se modeller och taggade bilder där. Lär dig mer om [att förbättra din klassificerare i Custom vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier). Det är viktigt att notera att inlärningen av modellen endast bör göras via Video Indexer (enligt beskrivningen i den här topid) och inte via webbplatsen Custom Vision. 

    1. Gå till sidan **anpassade modeller** i video Indexer och välj fliken **animerade tecken** . 
    1. Klicka på knappen Redigera för den modell du arbetar med för att hantera den i Custom Vision. 
    1. Granska varje Character-grupp: 

        * Om gruppen innehåller orelaterade bilder rekommenderar vi att du tar bort dem på Custom Vision webbplats. 
        * Om det finns bilder som tillhör ett annat, ändrar du taggen för dessa bilder genom att klicka på bilden, lägga till den högra taggen och ta bort fel tag gen. 
        * Om gruppen inte är korrekt, vilket innebär att den innehåller huvudsakligen icke-tecken bilder eller bilder från flera tecken, kan du ta bort dem i Custom Vision webbplats eller i Video Indexer insikter. 
        * En grupperingstyp delar ibland dina tecken till olika grupper. Vi rekommenderar därför att du ger alla grupper som tillhör samma namn samma namn (i Video Indexer insikter) som omedelbart kommer att se till att alla dessa grupper visas som på Custom Vision webbplats. 
    1. När gruppen är raffinerad kontrollerar du att det ursprungliga namnet du taggade den med motsvarar det i gruppen. 
1. Träna modellen 

    1. När du har redigerat alla namn som du vill ha måste du träna modellen. 
    1. När ett kort har tränats in i modellen identifieras det nästa video som indexeras med den modellen. 
    1. Öppna anpassnings sidan och klicka på fliken **animerade tecken**   och klicka sedan på knappen **träna** för att träna din modell. För att hålla anslutningen mellan videon 
    
Indexerare och modell, träna inte modellen på Custom Vision webbplats (betalda konton har åtkomst till Custom Vision webbplats), bara i Video Indexer. När de har tränats in kommer alla videor som ska indexeras eller indexeras med den modellen att identifiera de intränaa tecknen. 

## <a name="delete-an-animated-character-and-the-model"></a>Ta bort ett animerat teckensnitt och modellen

1. Ta bort ett animerat teckensnitt.

    1. Om du vill ta bort ett animerat teckensnitt i dina video insikter går du till fliken **insikter** och klickar på knappen **Redigera** i det övre högra hörnet i fönstret.
    1. Välj det animerade specialtecknet och klicka sedan på knappen **ta bort** under namn.

    > [!NOTE]
    > Detta tar bort insikter från den här videon men påverkar inte modellen.
1. Ta bort en modell.

    1. Klicka på knappen **anpassning av innehålls modell** på den översta menyn och gå till fliken **animerade tecken** .
    1. Klicka på ikonen med tre punkter till höger om den modell som du vill ta bort och klicka sedan på knappen Ta bort.
    
    * Betalt konto: modellen kommer att kopplas från Video Indexer och du kommer inte att kunna ansluta den igen.
    * Utvärderings konto: modellen tas även bort från insikten. 
    
        > [!NOTE]
        > I ett utvärderings konto har du bara en modell som du kan använda. När du har tagit bort det kan du inte träna andra modeller.

## <a name="use-the-animated-character-detection-with-api"></a>Använda animerad Character avkänning med API 

1. Anslut ett Custom Vision-konto.

    Om du äger ett Video Indexer betalt konto måste du först ansluta ett Custom Vision-konto. <br/>
    Om du inte redan har ett Custom Vision konto kan du skapa ett. Mer information finns i [Custom vision](../../cognitive-services/custom-vision-service/overview.md).

    [Anslut ditt Custom vision-konto med hjälp av API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Skapa en animerad tecken modell.

    Använd API för att [skapa animerings modell](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) .
1. Indexera eller indexera om en video.

    Använd [Omindexering](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) av API. 
1. Anpassa modeller för animerade tecken.

    Använd modell-API: t för [träna animering](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) .

### <a name="view-the-output"></a>Visa utdata

Se de animerade tecknen i den genererade JSON-filen.

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

* För närvarande stöds inte funktionen "animation Identification" i East-Asia region.
* Tecken som verkar vara små eller långt i videon kanske inte identifieras korrekt om videons kvalitet är låg.
* Rekommendationen är att använda en modell per uppsättning animerade tecken (till exempel per animerad serie).

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)
