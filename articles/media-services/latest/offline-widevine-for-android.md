---
title: Strömma Widevine Android offline
description: Det här avsnittet visar hur du konfigurerar ditt Azure Media Services v3-konto för offline-strömning av Widevine-skyddat innehåll.
services: media-services
keywords: BINDESTRECK, DRM, Widevine offline-läge, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c7adab9c14075e12cf5656f9e798e78ff26ff0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955215"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Offline-Widevine strömning för Android med Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Förutom att skydda innehåll för online-direktuppspelning, erbjuder medie innehålls prenumeration och hyres tjänster nedladdnings Bart innehåll som fungerar när du inte är ansluten till Internet. Du kan behöva ladda ned innehåll till din telefon eller surfplatta för uppspelning i flyg Plans läge när det är frånkopplat från nätverket. Ytterligare scenarier där du kanske vill ladda ned innehåll:

- En del innehålls leverantörer får inte tillåta leverans av DRM-licenser utöver ett land/regions gräns. Om en användare vill titta på innehåll medan du reser utomlands krävs offline-nedladdning.
- I vissa länder/regioner är Internet tillgänglighet och/eller bandbredd begränsad. Användarna kan välja att ladda ned innehåll för att kunna se det med hög nog hög upplösning för tillfredsställande visnings upplevelse.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Den här artikeln beskriver hur du implementerar uppspelning offline för streck innehåll som skyddas av Widevine på Android-enheter. Med offline-DRM kan du tillhandahålla prenumerations-, hyres-och inköps modeller för ditt innehåll, vilket gör det möjligt för kunder av tjänsterna att enkelt ta innehåll med dem när de är frånkopplade från Internet.

För att skapa Android Player-appar kan vi disponera tre alternativ:

> [!div class="checklist"]
> * Bygg en spelare med Java API för ExoPlayer SDK
> * Bygg en spelare med Xamarin-bindningen för ExoPlayer SDK
> * Bygg en spelare med hjälp av EME (Encrypted Media Extension) och media source Extension (MSE) i V62 för mobila webbläsare eller senare

Artikeln har också svar på några vanliga frågor om offline-strömning av Widevine-skyddat innehåll.

> [!NOTE]
> Offline-DRM debiteras bara för att skapa en enskild begäran om en licens när du laddar ned innehållet. Eventuella fel faktureras inte.

## <a name="prerequisites"></a>Förutsättningar 

Innan du implementerar offline DRM för Widevine på Android-enheter bör du först:

- Bekanta dig med de begrepp som introducerades för innehålls skydd online med Widevine DRM. Detta beskrivs i detalj i följande dokument/exempel:
    - [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
    - [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
- Klona https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git .

    Du måste ändra koden i [kryptera med DRM med .net](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) för att lägga till Widevine-konfigurationer.  
- Bekanta dig med Google ExoPlayer SDK för Android, en Video Player SDK med öppen källkod som stöder offline-uppspelning av Widevine. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Guide för ExoPlayer-utvecklare](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer-blogg](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurera innehålls skydd i Azure Media Services

I [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) -metoden finns följande nödvändiga steg:

1. Ange hur innehålls nyckel leverans tillåts i licens leverans tjänsten: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Konfigurera Widevine-licens mal len:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Skapa ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Aktivera offline-läge

Om du vill aktivera **offline** -läge för Widevine-licenser måste du konfigurera [licens mal len för Widevine](widevine-license-template-overview.md). I **policy_overrides** -objektet anger du egenskapen **can_persist** till **True** (standard är falskt), som du ser i [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurera Android Player för offline-uppspelning

Det enklaste sättet att utveckla en inbyggd Player-app för Android-enheter är att använda [Google EXOPLAYER SDK](https://github.com/google/ExoPlayer), en Video Player SDK med öppen källkod. ExoPlayer stöder funktioner som för närvarande inte stöds av Android: s inbyggda Media Player-API, inklusive MPEG-streck och Microsoft Smooth Streaming Delivery Protocols.

ExoPlayer version 2,6 och senare innehåller många klasser som stöder offline-uppspelning av DRM-Widevine. I synnerhet tillhandahåller OfflineLicenseHelper-klassen verktyg för att under lätta användningen av DefaultDrmSessionManager för att ladda ned, förnya och frigöra offline-licenser. De klasser som anges i SDK-mappen "Library/Core/src/main/Java/com/Google/Android/exoplayer2/offline/" stöder nedladdning av video innehåll offline.

Följande lista över klasser underlättar offline-läget i ExoPlayer SDK för Android:

- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/OfflineLicenseHelper. java  
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSession. java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager. java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/DrmSession. java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/ErrorStateDrmSession. java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/DRM/ExoMediaDrm. java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/offline/SegmentDownloader. java
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper. java 
- Library/Core/src/main/Java/com/Google/Android/exoplayer2/offline/hämtare. java
- Library/bindestreck/src/main/Java/com/Google/Android/exoplayer2/source/streck/offline/DashDownloader. java 

Utvecklare bör referera till [ExoPlayer Developer-guiden](https://google.github.io/ExoPlayer/guide.html) och motsvarande [utvecklare för utvecklare](https://medium.com/google-exoplayer) när de utvecklar ett program. Google har inte publicerat en fullständigt dokumenterad referens implementering eller exempel kod för ExoPlayer-appen som stöder Widevine offline just nu, så informationen är begränsad till utvecklarens guide och blogg. 

### <a name="working-with-older-android-devices"></a>Arbeta med äldre Android-enheter

För vissa äldre Android-enheter måste du ange värden för följande **policy_overrides** egenskaper (definieras i [Widevine-licens mal len](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** och **license_duration_seconds**. Du kan också ställa in dem på noll, vilket innebär oändlig/obegränsad varaktighet.  

Värdena måste anges för att undvika en heltals spill bugg. Mer information om problemet finns i https://github.com/google/ExoPlayer/issues/3150 och https://github.com/google/ExoPlayer/issues/3112 . <br/>Om du inte anger värdena explicit, tilldelas mycket stora värden för  **PlaybackDurationRemaining** och **LicenseDurationRemaining** (till exempel 9223372036854775807, vilket är det högsta positiva värdet för ett 64-bitars heltal). Det innebär att Widevine-licensen har upphört att gälla och därför att dekrypteringen inte sker. 

Det här problemet uppstår inte på Android 5,0 Lollipop eller senare eftersom Android 5,0 är den första Android-versionen, som har utformats för att fullständigt stödja ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) och 64-bitarsplattformar, medan Android 4,4 KitKat ursprungligen har utformats för att stödja ARMv7-och 32-bitarsplattformar som med andra äldre Android-versioner.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Använda Xamarin för att bygga en Android-uppspelnings-app

Du kan hitta Xamarin-bindningar för ExoPlayer med hjälp av följande länkar:

- [Bibliotek för Xamarin-bindningar för Google ExoPlayer-biblioteket](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin-bindningar för ExoPlayer-NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Se även följande tråd: Xamarin- [bindning](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome Player-appar för Android

Från och med lanseringen av [Chrome för Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)stöds beständig licens i EME. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) stöds nu också i Chrome för Android. På så sätt kan du skapa offline-uppspelnings program i Chrome om slutanvändarna har den här (eller högre) versionen av Chrome. 

Dessutom har Google skapat ett PWA-exempel (PWA) med öppen källkod: 

- [Käll kod](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google-värdbaserad version](https://biograf-155113.appspot.com/ttt/episode-2/) (fungerar bara i Chrome v 62 och högre på Android-enheter)

Om du uppgraderar din mobila Chrome-webbläsare till V62 (eller högre) på en Android-telefon och testar den ovan värdbaserade exempel appen, kommer du att se att både online-direktuppspelning och offline-uppspelning fungerar.

Den här PWA-appen med öppen källkod har skapats i Node.js. Om du vill vara värd för din egen version på en Ubuntu-Server bör du tänka på följande vanliga påträffade problem som kan förhindra uppspelning:

1. CORS-problem: exempel videon i exempel appen finns i https://storage.googleapis.com/biograf-video-files/videos/ . Google har konfigurerat CORS för alla test exempel som finns i Google Cloud Storage Bucket. De hanteras med CORS-rubriker och anger explicit CORS-posten: `https://biograf-155113.appspot.com` (den domän där Google-värdarna i sitt exempel) förhindrar åtkomst av andra platser. Om du försöker visas följande HTTP-fel: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Certifikat problem: från och med Chrome v 58 krävs HTTPS för EME för Widevine. Därför måste du vara värd för exempel appen via HTTPS med ett X509-certifikat. Ett vanligt test certifikat fungerar inte på grund av följande krav: du måste skaffa ett certifikat som uppfyller följande minimi krav:
    - Chrome och Firefox kräver SAN-Subject alternativ namns inställning som finns i certifikatet
    - Certifikatet måste ha en betrodd certifikat utfärdare och ett självsignerat utvecklings certifikat fungerar inte
    - Certifikatet måste ha ett CN-namn som matchar DNS-namnet på webb servern eller gatewayen

## <a name="faqs"></a>Vanliga frågor och svar

Mer information finns i [vanliga frågor och svar om Widevine](frequently-asked-questions.md#widevine-streaming-for-android).

## <a name="additional-notes"></a>Ytterligare information

Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="summary"></a>Sammanfattning

Den här artikeln beskrivs hur du implementerar uppspelning i offlineläge för streck innehåll som skyddas av Widevine på Android-enheter.  Det besvarade även några vanliga frågor om offline-strömning av Widevine-skyddat innehåll.
