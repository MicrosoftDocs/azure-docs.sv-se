---
title: Använda portalen för att ladda upp, koda och strömma innehåll
description: Den här snabb starten visar hur du använder portalen för att ladda upp, koda och strömma innehåll med Azure Media Services.
ms.topic: quickstart
ms.date: 08/31/2020
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.openlocfilehash: 3831892b6cd241643b3e5753689f768d59e27704
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955978"
---
# <a name="quickstart-upload-encode-and-stream-content-with-portal"></a>Snabb start: Ladda upp, koda och strömma innehåll med portalen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här snabb starten visar hur du använder Azure Portal för att ladda upp, koda och strömma innehåll med Azure Media Services.

> [!NOTE]
> Se till att granska: [Azure Portal begränsningar för Media Services v3](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3).
  
## <a name="overview"></a>Översikt

* Om du vill börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services-konto och ladda upp den digitala medie filen med hög kvalitet till en **till gång**. 
    
    > [!NOTE]
    > Om din video tidigare har laddats upp till Media Services kontot med hjälp av Media Services v3 API eller om innehållet genererades baserat på Live-utdata, visas inte knapparna **koda**, **analysera** eller **kryptera** i Azure Portal. Använd Media Services v3-API: erna för att utföra dessa uppgifter.

    Granska följande: 

  * [Uppladdning till och lagring i molnet](storage-account-concept.md)
  * [Till gångs koncept](assets-concept.md)
* När du har överfört den digitala medie filen med hög kvalitet till en till gång (en indata till gång) kan du bearbeta den (koda eller analysera). Det bearbetade innehållet går till en annan till gång (utmatnings till gång). 
    * [Koda](encoding-concept.md) den överförda filen till format som kan spelas upp i en mängd olika webbläsare och enheter.
    * [Analysera](analyzing-video-audio-files-concept.md) den överförda filen. 

        När du använder Azure Portal kan du för närvarande göra följande: generera en TTML och WebVTT-filer med dold textning. Filer i dessa format kan användas för att göra ljud-och videofiler tillgängliga för personer med hörsel funktions hinder. Du kan också extrahera nyckelord från ditt innehåll.

        Om du vill ha en omfattande upplevelse som gör det möjligt att extrahera insikter från dina video-och ljudfiler använder du Media Services v3-för inställningar (enligt beskrivningen i [Självstudier: analysera videor med Media Services v3](analyze-videos-tutorial-with-api.md)). <br/>Om du vill ha mer detaljerade insikter använder du [video Indexer](../video-indexer/index.yml) direkt.    
* När innehållet har bearbetats kan du leverera medie innehåll till klient spelarna. Om du vill göra videor i utmatnings till gången tillgängliga för klienter för uppspelning måste du skapa en **strömmande positionerare**. När du skapar en **strömmande positionerare** måste du ange en **strömmande princip**. Med **direkt uppspelnings principer** kan du definiera strömmande protokoll och krypterings alternativ (om det finns några) för dina **strömmande positionerare**.
    
    Beakta

    * [Positionerare för direktuppspelning](streaming-locators-concept.md)
    * [Principer för direktuppspelning](streaming-policy-concept.md)
    * [Paketering och leverans](dynamic-packaging-overview.md)
    * [Filter](filters-concept.md)
* Du kan skydda ditt innehåll genom att kryptera det med Advanced Encryption Standard (AES-128) eller/och något av de tre stora DRM-systemen: Microsoft PlayReady, Google Widevine och Apple FairPlay. [Kryptera innehåll med Azure Portal](encrypt-content-quickstart.md) snabb start visar hur du konfigurerar innehålls skydd.
        
## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[Skapa ett Media Services-konto](create-account-howto.md)

## <a name="upload"></a>Ladda upp

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Leta upp och klicka på ditt Media Services-konto.
1. Välj **till gångar (ny)**.
1. Tryck på **överför** överst i fönstret. 
1. Dra och släpp eller bläddra till en fil som du vill ladda upp.

Om du navigerar till fönstret till gångar, ser du att en ny till gång har lagts till i listan:

![Skärm bild av Azure Portal visar fönstret till gångar som öppnats genom att välja till gångar (nytt) och en ny till gång genom att välja knappen Överför.](./media/manage-assets-quickstart/upload.png)

## <a name="encode"></a>Koda

1. Välj **till gångar (ny)**.
1. Välj din nya till gång (lades till i det sista steget).
1. Klicka på **koda** överst i fönstret.

    Om du trycker på den här knappen startas kodnings jobbet. När den har slutförts genererar en utgående till gång som innehåller det kodade innehållet.

Om du navigerar till fönstret till gångar kommer du att se att den utgående till gången har lagts till i listan:

![Skärm bild av fönstret till gångar i Azure Portal visar den till gång ignite.mp4 Media-kodad standard som har kodats till listan över till gångar.](./media/manage-assets-quickstart/encode.png)

## <a name="monitor-the-job-progress"></a>Övervaka jobb förloppet

Om du vill visa jobb status går du till **jobb**. Jobbet går vanligt vis igenom följande tillstånd: schemalagda, köade, bearbetar, slutförda (slutligt tillstånd). Om jobbet har påträffat ett fel visas tillståndet Fel.

![Status](./media/manage-assets-quickstart/job-status.png)

## <a name="publish-and-stream"></a>Publicera och strömma

Om du vill publicera en till gång måste du nu lägga till en strömmande lokaliserare till din till gång.

### <a name="streaming-locator"></a>Strömmande positionerare 

1. I avsnittet **streaming Locator** trycker du på **+ Lägg till en strömmande positionerare**.
    Detta publicerar till gången och genererar strömmande URL: er.

    > [!NOTE]
    > Om du vill att data strömmen ska krypteras måste du skapa en princip för innehålls nyckel och ange den på den strömmande lokaliseraren. Mer information finns i [Kryptera innehåll med Azure Portal](encrypt-content-quickstart.md).
1. I fönstret **Lägg till streaming Locator** väljer du en av de fördefinierade strömmande principerna. Detaljerad information finns i [strömmande principer](streaming-policy-concept.md)

    ![Strömmande positionerare](./media/manage-assets-quickstart/streaming-locator.png)

När till gången har publicerats kan du strömma den direkt i portalen. 

![Spela](./media/manage-assets-quickstart/publish.png)

Du kan också kopiera strömnings-URL: en och använda den i klient spelaren.

> [!NOTE]
> Kontrol lera att [slut punkten för direkt uppspelning](streaming-endpoint-concept.md) körs. När du först skapar ett media service-konto skapas standard slut punkten för direkt uppspelning och den är i stoppat läge, så du måste starta den innan du kan strömma ditt innehåll.<br/>Du faktureras bara när slut punkten för direkt uppspelning är i körnings läge.

## <a name="cleanup-resources"></a>Rensa resurser

Om du avser att testa de andra snabb starterna ska du hålla på de resurser som skapats. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp under vilken du körde den här snabb starten och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

[Använda portalen för att kryptera innehåll](encrypt-content-quickstart.md)
