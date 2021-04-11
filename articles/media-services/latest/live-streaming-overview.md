---
title: Översikt över direkt uppspelning
description: Den här artikeln ger en översikt över direkt uppspelning med Azure Media Services v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: f207cefaf333fc21eb56dc15b1e4d191bf8e5fae
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108310"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Direktsänd strömning med Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Azure Media Services kan du leverera Live-händelser till dina kunder i Azure-molnet. Om du vill strömma dina Live-händelser med Media Services behöver du följande:  

- En kamera som används för att avbilda direktsänd händelse.<br/>För installations tips kan du läsa mer i installations [programmet för enkel och bärbar händelse]( https://link.medium.com/KNTtiN6IeT).

    Om du inte har åtkomst till en kamera kan du använda verktyg som t. ex. [Wirecast för multistream](https://www.telestream.net/wirecast/overview.htm) för att generera en live-feed från en videofil.
- En direkt video kodare som konverterar signaler från en kamera (eller en annan enhet, till exempel en bärbar dator) till en bidrags matning som skickas till Media Services. Bidrags flödet kan innehålla signaler som rör annonsering, till exempel SCTE-35-markörer.<br/>En lista över rekommenderade kodare för direkt uppspelning finns i [direkt uppspelnings kodare](recommended-on-premises-live-encoders.md). Ta också en titt på den här bloggen: [produktion av Direktsänd strömning med onlinebanksystem](https://link.medium.com/ttuwHpaJeT).
- Komponenter i Media Services som gör att du kan mata in, förhandsgranska, paketera, registrera, kryptera och skicka Live-evenemanget till dina kunder eller till ett CDN för ytterligare distribution.

För kunder som vill leverera innehåll till stora Internet-åhörare rekommenderar vi att du aktiverar CDN på [slut punkten för direkt uppspelning](streaming-endpoint-concept.md).

Den här artikeln ger en översikt och vägledning för Live streaming med Media Services och länkar till andra relevanta artiklar.
 
> [!NOTE]
> Du kan använda [Azure Portal](https://portal.azure.com/) för att hantera v3 [Live-händelser](live-events-outputs-concept.md), Visa v3- [till gångar](assets-concept.md), hämta information om åtkomst till API: er. För alla andra hanterings uppgifter (t. ex. transformationer och jobb) använder du [REST API](/rest/api/media/), [CLI](/cli/azure/ams)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.

## <a name="dynamic-packaging-and-delivery"></a>Dynamisk paketering och leverans

Med Media Services kan du dra nytta av [dynamisk paketering](encode-dynamic-packaging-concept.md)som gör att du kan förhandsgranska och skicka in dina Live-strömmar i [MPEG-streck, HLS och Smooth Streaming Format](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) från den bidrags feed som skickas till tjänsten. Dina användare kan spela upp direktsänd ström med alla HLS-, tank-eller Smooth Streaming-kompatibla spelare. Du kan använda [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) i dina webb-eller mobil program för att leverera data strömmen i något av dessa protokoll.

## <a name="dynamic-encryption"></a>Dynamisk kryptering

Med dynamisk kryptering kan du dynamiskt kryptera din direktsända eller på begäran-innehåll med AES-128 eller någon av de tre större Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. Mer information finns i [dynamisk kryptering](drm-content-protection-concept.md).

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="dynamic-filtering"></a>Dynamisk filtrering

Dynamisk filtrering används för att styra antalet spår, format, bit hastigheter och presentations tids fönster som skickas ut till spelarna. Mer information finns i [filter och dynamiska manifest](filters-dynamic-manifest-concept.md).

## <a name="live-event-types"></a>Direktsända händelse typer

[Live-händelser](/rest/api/media/liveevents) ansvarar för att mata in och bearbeta direktsända video flöden. En Live-händelse kan ställas in till antingen en *direkt* uppspelning (en lokal Live-kodare som skickar en data ström med flera bit hastigheter) eller *direktsänd kodning* (en lokal Live-kodare skickar en data ström med en bit hastighet). Mer information om Direktsänd strömning i Media Services v3 finns i [Live-händelser och Live-utdata](live-events-outputs-concept.md).

### <a name="pass-through"></a>Direkt

![Diagram som visar hur video-och ljud strömmar från en direkt sändnings händelse matas in och bearbetas.](./media/live-streaming/pass-through.svg)

När du använder direkt **sändnings evenemang** förlitar du dig på din lokala Live-kodare för att generera en video ström med flera bit hastigheter och skicka den som bidrags flödet till Live-händelsen (med hjälp av RTMP eller fragmenterad MP4-inmatnings protokoll). Live-händelsen bevarar sedan genom inkommande video strömmar till den dynamiska Paketeraren (slut punkt för direkt uppspelning) utan ytterligare kodning. En sådan direkt sändnings händelse är optimerad för långvariga Live-händelser eller linjär direkt uppspelning med 24x365. 

### <a name="live-encoding"></a>Live Encoding  

![livekodning](./media/live-streaming/live-encoding.svg)

När du använder Cloud encoding med Media Services kan du konfigurera den lokala Live-kodaren för att skicka en video med en bit hastighet som bidrags flödet (upp till 32Mbps agg regering) till Live-händelsen (med hjälp av RTMP eller fragmenterad MP4-inmatnings protokoll). Direkt sändningen kodar den inkommande enkla bit hastigheten till [video strömmar med flera bit](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) hastigheter i varierande lösningar för att förbättra leveransen och gör den tillgänglig för leverans till uppspelning av enheter via bransch standard protokoll som MPEG-streck, Apple http Live Streaming (HLS) och Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Direkt avskrift (för hands version)

Direkt avskrift är en funktion som du kan använda med Live-händelser som antingen är direkt eller direktsänd kodning. Mer information finns i [direkt avskriftering](live-transcription.md). När den här funktionen är aktive rad använder tjänsten funktionen [tal-till-text](../../cognitive-services/speech-service/speech-to-text.md) i Cognitive Services för att skriva över talade ord i inkommande ljud till text. Den här texten görs sedan tillgänglig för leverans tillsammans med video och ljud i MPEG-streck och HLS-protokoll.

> [!NOTE]
> För närvarande är direkt avskrift tillgänglig som en förhands gransknings funktion i USA, västra 2.

## <a name="live-streaming-workflow"></a>Live streaming-arbetsflöde

För att förstå Live streaming-arbetsflödet i Media Services v3 måste du först granska och förstå följande begrepp: 

- [Slutpunkter för direktuppspelning](streaming-endpoint-concept.md)
- [Livehändelser och liveutdata](live-events-outputs-concept.md)
- [Positionerare för direktuppspelning](streaming-locators-concept.md)

### <a name="general-steps"></a>Allmänna steg

1. I ditt Media Services konto kontrollerar du att **slut punkten för direkt uppspelning** körs. 
2. Skapa en [Live-händelse](live-events-outputs-concept.md). <br/>När du skapar händelsen kan du ange att den ska autostartas. Du kan också starta händelsen när du är redo att börja strömma.<br/> När Autostart har angetts till True startas direkt sändningen direkt efter att den har skapats. Faktureringen börjar så snart direkt händelsen börjar köras. Du måste uttryckligen anropa Stop Event-resursen för att stoppa ytterligare fakturering. Mer information finns i [händelse tillstånd och fakturering](live-event-states-billing.md).
3. Hämta hämtnings-URL: er och konfigurera din lokala kodare för att använda URL: en för att skicka bidrags flödet.<br/>Se [rekommenderade Live-kodare](recommended-on-premises-live-encoders.md).
4. Hämta för hands versionen av URL: en och Använd den för att kontrol lera att inmatarna faktiskt tas emot.
5. Skapa ett nytt **till gångs** objekt. 

    Varje Live-utdata är kopplad till en till gång, som används för att spela in videon i den associerade Azure Blob storage-behållaren. 
6. Skapa en **Live-utdata** och Använd namnet på den till gång som du skapade så att data strömmen kan arkiveras i till gången.

    Liveutdata startar när de skapas och avbryts när de tas bort. När du tar bort Live-utdata tar du inte bort den underliggande till gången och innehållet i till gången.
7. Skapa en **strömmande lokaliserare** med de [inbyggda typer av strömmande principer](streaming-policy-concept.md).

    Om du vill publicera Live-utdata måste du skapa en strömmande lokaliserare för den associerade till gången. 
8. Visa en lista över Sök vägarna för den **strömmande lokaliseraren** för att få tillbaka de webb adresser som ska användas (dessa är deterministiska).
9. Hämta värd namnet för den **strömmande slut punkten** (ursprung) som du vill strömma från.
10. Kombinera URL: en från steg 8 med värd namnet i steg 9 för att få den fullständiga URL: en.
11. Om du inte vill att din **Live-händelse** ska visas måste du stoppa strömningen och ta bort den **strömmande lokaliseraren**.
12. Följ stegen nedan om du är klar med strömningen av händelser och vill rensa de resurser som etablerades tidigare.

    * Stoppa sändningen av dataströmmen från kodaren.
    * Stoppa Live-händelsen. När Live-händelsen har stoppats debiteras inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
    * Du kan stoppa slut punkten för direkt uppspelningen om du inte vill fortsätta att ange en Live-händelse som Arkiv lag ring. Om Live-händelsen är i stoppat tillstånd debiteras inga avgifter.

Till gången som Live-utdata arkiveras till blir automatiskt en till gång på begäran när Live-utdata tas bort. Du måste ta bort alla Live-utdata innan en Live-händelse kan stoppas. Du kan använda en valfri flagga [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) för att automatiskt ta bort Live-utdata vid stopp. 

> [!TIP]
> I [självstudien om direkt uppspelning](stream-live-tutorial-with-api.md), artikeln undersöker den kod som implementerar stegen som beskrivs ovan.

## <a name="other-important-articles"></a>Andra viktiga artiklar

- [Rekommenderade livekodare](recommended-on-premises-live-encoders.md)
- [Använda en molnbaserad DVR-spelare](live-event-cloud-dvr.md)
- [Funktions jämförelse för live event types](live-event-types-comparison.md)
- [Tillstånd och fakturering](live-event-states-billing.md)
- [Svarstid](live-event-latency.md)

## <a name="live-streaming-questions"></a>Frågor om Live-direktuppspelning

Se artikeln om [direkt uppspelnings frågor](questions-collection.md#live-streaming) .
