---
title: Media Services terminologi och koncept
description: Lär dig mer om terminologi och begrepp för Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 2d388cf6c776e6d30a487ce9e14ba1601dce1d23
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491630"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services terminologi och koncept

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Det här avsnittet ger en kort översikt över Azure Media Services terminologi och begrepp. Artikeln innehåller också länkar till artiklar med en djupgående förklaring av Media Services v3-koncept och-funktioner.

De grundläggande begreppen som beskrivs i dessa avsnitt bör granskas innan du börjar utveckla.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3-terminologi

|Period|Beskrivning|
|---|---|
|Live-händelse|En **Live-händelse** representerar en pipeline för inmatning, kodning (valfritt) och paketering av direktsända video-, ljud-och real tids metadata.<br/><br/>För kunder som migrerar från Media Services v2-API: er ersätter **Live-händelsen** **kanal** enheten i v2. Mer information finns i [Migrera från v2 till v3](migrate-v-2-v-3-migration-introduction.md).|
|Slut punkt för direkt uppspelning/paketering/ursprung|En **slut punkt för direkt uppspelning** representerar en dynamisk (just-in-Time)-paketering och ursprungs tjänst som kan leverera Live och på begäran-innehåll direkt till ett klient Player-program. Den använder ett av de vanliga protokollen för strömmande media (HLS eller tank streck). Dessutom tillhandahåller streaming- **slutpunkten** dynamisk (just-in-Time) kryptering till branschledande Digital Rights Management system (DRM: er).<br/><br/>I medie direkt uppspelnings branschen kallas den här tjänsten ofta en **paketerare** eller ett **ursprung**.  Andra vanliga termer i branschen för den här funktionen är JITP (just-in-Time-packageer) eller JITE (just-in-Time-Encryption).

## <a name="media-services-v3-concepts"></a>Media Services v3-begrepp

|Begrepp|Beskrivning|Länkar|
|---|---|---|
|Till gångar och överför innehåll|För att börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services konto och ladda upp dina digitala filer till **till gångar**.|[Uppladdning till och lagring i molnet](storage-account-concept.md)<br/><br/>[Till gångs koncept](assets-concept.md)|
|Koda innehåll|När du har överfört digitala mediefiler med hög kvalitet till till gångar kan du koda dem till-format som kan spelas upp i en mängd olika webbläsare och enheter. <br/><br/>Om du vill koda med Media Services v3 måste du skapa **transformeringar** och **jobb**.|[Transformeringar och jobb](transforms-jobs-concept.md)<br/><br/>[Kodning med Media Services](encoding-concept.md)|
|Analysera innehåll (Video Indexer)|Med Media Services v3 kan du extrahera insikter från dina video-och ljudfiler med hjälp av Media Services v3-för inställningar. Om du vill analysera ditt innehåll med Media Services v3-för inställningar måste du skapa **transformeringar** och **jobb**.<br/><br/>Om du vill ha mer detaljerade insikter använder du [video Indexer](../video-indexer/index.yml) direkt.|[Analysera video-och ljudfiler](analyzing-video-audio-files-concept.md)|
|Paketering och leverans|När ditt innehåll är kodat kan du dra nytta av **dynamisk paketering**. I Media Services är en **slut punkt för direkt uppspelning** den dynamiska packnings tjänsten som används för att leverera medie innehåll till klient spelarna. Om du vill göra videor i utmatnings till gången tillgängliga för klienter för uppspelning, måste du skapa en **strömmande lokaliserare** och sedan skapa strömmande URL: er. <br/><br/>När du skapar **streaming Locator**, förutom till gångens namn, måste du ange en **strömmande princip**. Med **direkt uppspelnings principer** kan du definiera strömmande protokoll och krypterings alternativ (om det finns några) för dina **strömmande positionerare**. Dynamisk paketering används oavsett om du strömmar ditt innehåll Live eller på begäran. <br/><br/>Du kan använda Media Services **dynamiska manifest** för att endast strömma en speciell åter givning eller under klipp av videon.|[Dynamisk paketering](dynamic-packaging-overview.md)<br/><br/>[Slut punkter för direkt uppspelning](streaming-endpoint-concept.md)<br/><br/>[Strömmande positionerare](streaming-locators-concept.md)<br/><br/>[Strömmande principer](streaming-policy-concept.md)<br/><br/>[Dynamiska manifest](filters-dynamic-manifest-overview.md)<br/><br/>[Filter](filters-concept.md)|
|Innehållsskydd|Med Media Services kan du leverera Live och innehåll på begäran som krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller/och något av de tre stora DRM-systemen: Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. <br/><br/>Om du anger krypterings alternativ för data strömmen skapar du **innehålls nyckel principen** och associerar den med din **strömmande positionerare**. Med **princip för innehålls nyckel** kan du konfigurera hur innehålls nyckeln levereras till slut klienter.<br/><br/> Försök att återanvända principer när samma alternativ behövs.| [Principer för innehålls nyckel](content-key-policy-concept.md)<br/><br/>[Innehålls skydd](content-protection-overview.md)|
|Liveuppspelning|Med Media Services kan du leverera Live-händelser till dina kunder i Azure-molnet. **Livehändelser** ansvarar för att mata in och bearbeta direktsända videofeeds. När du skapar en **Live-händelse** skapas en ingångs slut punkt som du kan använda för att skicka en Live-signal från en fjär kodare. När strömmen flödar in i Live- **evenemanget** kan du starta den strömmande händelsen genom att skapa en **till gång**, en **Live-utgång** och en **strömmande positionerare**. **Live-utdata** kommer att arkivera strömmen till **till gången** och göra den tillgänglig för användare via **slut punkten för direkt uppspelning**. En Live-händelse kan ställas in till antingen en *direkt* uppspelning (en lokal Live-kodare som skickar en data ström med flera bit hastigheter) eller *direktsänd kodning* (en lokal Live-kodare skickar en data ström med en bit hastighet). |[Översikt över direkt uppspelning](live-streaming-overview.md)<br/><br/>[Livehändelser och liveutdata](live-events-outputs-concept.md)|
|Övervakning med Event Grid|Om du vill se förloppet för jobbet använder du **Event Grid**. Media Services utskriver också direkt händelse typer. Med Event Grid kan dina appar lyssna efter och reagera på händelser från i princip alla Azure-tjänster, samt även från anpassade källor. |[Hantera Event Grid-händelser](reacting-to-media-services-events.md)<br/><br/>[Scheman](media-services-event-schemas.md)|
|Övervakning med Azure Monitor|Övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina appar presterar med Azure Monitor.|[Mått och diagnostikloggar](monitoring/monitor-media-services-data-reference.md)<br/><br/>[Scheman för diagnostikloggar](monitoring/monitor-media-services-data-reference.md)|
|Player-klienter|Du kan använda Azure Media Player för att spela upp medie innehåll som strömmas av Media Services på en rad olika webbläsare och enheter. Azure Media Player använder bransch standarder, till exempel HTML5, media source Extensions (MSE) och EME (Encrypted Media Extensions) för att tillhandahålla en omfattande anpassningsbar strömmande upplevelse. |[Översikt över Azure Media Player](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* [Koda fjärrfiler och strömma video – REST](stream-files-tutorial-with-rest.md)
* [Koda uppladdade filer och strömma video – NET](stream-files-tutorial-with-api.md)
* [Strömma live – .NET](stream-live-tutorial-with-api.md)
* [Analysera din video – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128, dynamisk kryptering – .NET](protect-with-aes128.md)
* [Kryptera dynamiskt med multi-DRM-.NET](protect-with-drm.md)