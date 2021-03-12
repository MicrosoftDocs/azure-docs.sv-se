---
title: Konfigurera ditt konto för offline-strömning av Widevine-skyddat innehåll – Azure
description: Det här avsnittet visar hur du konfigurerar ditt Azure Media Services-konto för offline-strömning av Widevine-skyddat innehåll.
services: media-services
keywords: BINDESTRECK, DRM, Widevine offline-läge, ExoPlayer, Android
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: b13feeda162f9babe1958402a694b728312ea4e5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015411"
---
# <a name="offline-widevine-streaming-for-android"></a>Widevine-direktuppspelning offline för Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 3](../latest/offline-widevine-for-android.md)
> * [Version 2](offline-widevine-for-android.md)

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Förutom att skydda innehåll för online-direktuppspelning, erbjuder medie innehålls prenumeration och hyres tjänster nedladdnings Bart innehåll som fungerar när du inte är ansluten till Internet. Du kan behöva ladda ned innehåll till din telefon eller surfplatta för uppspelning i flyg Plans läge när det är frånkopplat från nätverket. Ytterligare scenarier där du kanske vill ladda ned innehåll:

- En del innehålls leverantörer får inte tillåta leverans av DRM-licenser utöver ett land/regions gräns. Om en användare vill titta på innehåll medan du reser utomlands krävs offline-nedladdning.
- I vissa länder/regioner är Internet tillgänglighet och/eller bandbredd begränsad. Användarna kan välja att ladda ned innehåll för att kunna se det med hög nog hög upplösning för tillfredsställande visnings upplevelse.

Den här artikeln beskriver hur du implementerar uppspelning offline för streck innehåll som skyddas av Widevine på Android-enheter. Med offline-DRM kan du tillhandahålla prenumerations-, hyres-och inköps modeller för ditt innehåll, vilket gör det möjligt för kunder av tjänsterna att enkelt ta innehåll med dem när de är frånkopplade från Internet.

För att skapa Android Player-appar kan vi disponera tre alternativ:

> [!div class="checklist"]
> * Bygg en spelare med Java API för ExoPlayer SDK
> * Bygg en spelare med Xamarin-bindningen för ExoPlayer SDK
> * Bygg en spelare med hjälp av EME (Encrypted Media Extension) och media source Extension (MSE) i V62 för mobila webbläsare eller senare

Artikeln har också svar på några vanliga frågor om offline-strömning av Widevine-skyddat innehåll.

## <a name="requirements"></a>Krav 

Innan du implementerar offline DRM för Widevine på Android-enheter bör du först:

- Bekanta dig med de begrepp som introducerades för innehålls skydd online med Widevine DRM. Detta beskrivs i detalj i följande dokument/exempel:
    - [Använda Azure Media Services för att leverera DRM-licenser eller AES-nycklar](media-services-deliver-keys-and-licenses.md)
    - [CENC med Multi-DRM och åtkomstkontroll: En referensdesign och implementering i Azure och Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Använda PlayReady och/eller Widevine Dynamic Common Encryption med .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Använd Azure Media Services för att leverera PlayReady-och/eller Widevine-licenser med .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Bekanta dig med Google ExoPlayer SDK för Android, en Video Player SDK med öppen källkod som stöder offline-uppspelning av Widevine. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Guide för ExoPlayer-utvecklare](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer-blogg](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfiguration av innehålls skydd i Azure Media Services

När du konfigurerar Widevine-skydd för en till gång i Media Services måste du skapa ContentKeyAuthorizationPolicyOption som har angett följande tre saker:

1. DRM-system (Widevine)
2. ContentKeyAuthorizationPolicyRestriction som anger hur innehålls nyckel leverans tillåts i licens leverans tjänsten (öppen eller token Authorization)
3. DRM (Widevine)-licens mal len

Om du vill aktivera **offline** -läge för Widevine-licenser måste du konfigurera [licens mal len för Widevine](media-services-widevine-license-template-overview.md). I **policy_overrides** -objektet anger du egenskapen **can_persist** till **Sant** (Standardvärdet är falskt). 

I följande kod exempel används .NET för att aktivera **offline** -läge för Widevine-licenser. Koden baseras på [ med hjälp av PlayReady och/eller Widevine Dynamic common Encryption med .net](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) -exempel. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurera Android Player för offline-uppspelning

Det enklaste sättet att utveckla en inbyggd Player-app för Android-enheter är att använda [Google EXOPLAYER SDK](https://github.com/google/ExoPlayer), en Video Player SDK med öppen källkod. ExoPlayer stöder funktioner som för närvarande inte stöds av Android: s inbyggda Media Player-API, inklusive MPEG-streck och Microsoft Smooth Streaming Delivery Protocols.

ExoPlayer version 2,6 och senare innehåller många klasser som stöder offline-uppspelning av DRM-Widevine. I synnerhet tillhandahåller OfflineLicenseHelper-klassen verktyg för att under lätta användningen av DefaultDrmSessionManager för att ladda ned, förnya och frigöra offline-licenser. De klasser som anges i SDK-mappen "Library/Core/src/main/Java/com/Google/Android/exoplayer2/offline/" stöder nedladdning av video innehåll offline.

Följande lista över klasser underlättar offline-läge i ExoPlayer SDK för Android:

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

För vissa äldre Android-enheter måste du ange värden för följande **policy_overrides** egenskaper (definieras i [Widevine-licens mal len](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** och **license_duration_seconds**. Du kan också ställa in dem på noll, vilket innebär oändlig/obegränsad varaktighet.  

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

- [Källkod](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google-värdbaserad version](https://biograf-155113.appspot.com/ttt/episode-2/) (fungerar bara i Chrome v 62 och högre på Android-enheter)

Om du uppgraderar din mobila Chrome-webbläsare till V62 (eller högre) på en Android-telefon och testar den ovan värdbaserade exempel appen, kommer du att se att både online-direktuppspelning och offline-uppspelning fungerar.

Den här PWA-appen med öppen källkod har skapats i Node.js. Om du vill vara värd för din egen version på en Ubuntu-Server bör du tänka på följande vanliga påträffade problem som kan förhindra uppspelning:

1. CORS-problem: exempel videon i exempel appen finns i https://storage.googleapis.com/biograf-video-files/videos/ . Google har konfigurerat CORS för alla test exempel som finns i Google Cloud Storage Bucket. De hanteras med CORS-rubriker och anger explicit CORS-posten: `https://biograf-155113.appspot.com` (den domän där Google-värdarna i sitt exempel) förhindrar åtkomst av andra platser. Om du försöker visas följande HTTP-fel: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Certifikat problem: från och med Chrome v 58 krävs HTTPS för EME för Widevine. Därför måste du vara värd för exempel appen via HTTPS med ett X509-certifikat. Ett vanligt test certifikat fungerar inte på grund av följande krav: du måste skaffa ett certifikat som uppfyller följande minimi krav:
    - Chrome och Firefox kräver SAN-Subject alternativ namns inställning som finns i certifikatet
    - Certifikatet måste ha en betrodd certifikat utfärdare och ett självsignerat utvecklings certifikat fungerar inte
    - Certifikatet måste ha ett CN-namn som matchar DNS-namnet på webb servern eller gatewayen

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="question"></a>Fråga

Hur kan jag leverera beständiga licenser (offline-aktiverade) för vissa klienter/användare och icke-beständiga licenser (offline-inaktive rad) för andra? Måste jag duplicera innehållet och använda en separat innehålls nyckel?

### <a name="answer"></a>Svar
Du behöver inte duplicera innehållet. Du kan bara använda en enda kopia av innehållet och en enskild ContentKeyAuthorizationPolicy, men två separata ContentKeyAuthorizationPolicyOption: s:

1. IContentKeyAuthorizationPolicyOption 1: använder beständiga licenser och ContentKeyAuthorizationPolicyRestriction 1 som innehåller ett anspråk som license_type = "persistent"
2. IContentKeyAuthorizationPolicyOption 2: använder icke-beständig licens och ContentKeyAuthorizationPolicyRestriction 2 som innehåller ett anspråk som license_type = "icke-beständig"

Det innebär att när en licens förfrågan kommer från klient appen, är det ingen skillnad mellan licens förfrågningar. För en annan slutanvändare/enhet bör STS dock ha affärs logiken för att utfärda olika JWT-token som innehåller olika anspråk (ett av de två license_type). Anspråks värdet i JWT-token kommer att användas för att bestämma vilken typ av licens som ska utfärdas av licens tjänsten: beständigt eller icke-permanent.

Det innebär att STS (Secure token service) måste ha information om affärs logiken och klienten/enheten för att lägga till motsvarande anspråks värde i en token.

### <a name="question"></a>Fråga

För Widevine säkerhets nivåer, i Googles [Widevine översikt över dokument](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentation för DRM-arkitekturen, definieras tre olika säkerhets nivåer. Men i [Azure Media Services dokumentation om Widevine licens mal len](./media-services-widevine-license-template-overview.md)beskrivs fem olika säkerhets nivåer. Vad är relationen eller mappningen mellan de två olika uppsättningarna med säkerhets nivåer?

### <a name="answer"></a>Svar

I Googles [Översikt över WIDEVINE DRM-arkitektur](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)definieras följande tre säkerhets nivåer:

1.  Säkerhets nivå 1: all innehålls bearbetning, kryptografi och kontroll utförs i TEE (Trusted Execution Environment). I vissa implementerings modeller kan säkerhets bearbetningen utföras i olika kretsar.
2.  Säkerhets nivå 2: Utför kryptografi (men inte video bearbetning) i TEE: dekrypterade buffertar returneras till program domänen och bearbetas via separat video maskin vara eller program vara. På nivå 2 bearbetas dock kryptografisk information fortfarande endast inom TEE.
3.  Säkerhets nivå 3 har inte någon TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografisk information och dekrypterat innehåll på värd operativ system. En nivå 3-implementering kan också innehålla en kryptografisk motor för maskin vara, men den förbättrar bara prestanda, inte säkerhet.

På samma tid kan security_level egenskapen för content_key_specs ha följande fem olika värden (krav för Widevine för uppspelning) i [Azure Media Services dokumentation om licens mal len](./media-services-widevine-license-template-overview.md)för:

1.  Programvarubaserad-baserad kryptografisk kryptering krävs.
2.  Program varu kryptering och en fördunklade-avkodare måste anges.
3.  Nyckel material-och krypterings åtgärder måste utföras inom en maskinvarubaserad TEE.
4.  Kryptering och avkodning av innehåll måste utföras inom en maskinvarubaserad TEE.
5.  Krypteringen, avkodningen och all hantering av mediet (komprimerade och okomprimerade) måste hanteras inom en maskinvarubaserad TEE.

Båda säkerhets nivåerna definieras av Google Widevine. Skillnaden är i användnings nivån: arkitektur nivå eller API-nivå. De fem säkerhets nivåerna används i Widevine-API: et. Content_key_specs-objektet som innehåller security_level deserialiseras och skickas till den globala Widevine-leverans tjänsten av Azure Media Services Widevine licens service. Tabellen nedan visar mappningen mellan de två uppsättningarna med säkerhets nivåer.

| **Säkerhets nivåer som definieras i Widevine-arkitekturen** |**Säkerhets nivåer som används i Widevine-API**|
|---|---| 
| **Säkerhets nivå 1**: all innehålls bearbetning, kryptografi och kontroll utförs i tee (Trusted Execution Environment). I vissa implementerings modeller kan säkerhets bearbetningen utföras i olika kretsar.|**security_level = 5**: kryptering, avkodning och all hantering av mediet (komprimerade och okomprimerade) måste hanteras inom en maskinvarubaserad tee.<br/><br/>**security_level = 4**: kryptering och avkodning av innehåll måste utföras inom en maskinvarubaserad tee.|
**Säkerhets nivå 2**: Utför kryptografi (men inte video bearbetning) i tee: dekrypterade buffertar returneras till program domänen och bearbetas via separat video maskin vara eller program vara. På nivå 2 bearbetas dock kryptografisk information fortfarande endast inom TEE.| **security_level = 3**: nyckel material-och krypterings åtgärder måste utföras inom en maskinvarubaserad tee. |
| **Säkerhets nivå 3**: har inte någon tee på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografisk information och dekrypterat innehåll på värd operativ system. En nivå 3-implementering kan också innehålla en kryptografisk motor för maskin vara, men den förbättrar bara prestanda, inte säkerhet. | **security_level = 2**: program varu kryptering och en fördunklade-avkodare krävs.<br/><br/>**security_level = 1**: programvarubaserad kryptografiskt kryptografiskt krypterings utrymme krävs.|

### <a name="question"></a>Fråga

Varför tar det lång tid för innehålls hämtningen?

### <a name="answer"></a>Svar

Det finns två sätt att förbättra nedladdnings hastigheten:

1.  Aktivera CDN så att slutanvändare förmodligen kan trycka på CDN i stället för slut punkts-/direkt uppspelnings slut punkt för innehålls hämtning. Om slut punkter för direkt uppspelning av användare träffar, paketeras och krypteras varje HLS segment eller ett streck fragment. Även om den här svars tiden är i millisekunder för varje segment/fragment, kan den ackumulerade svars tiden vara en stor del av den ackumulerade fördröjningen som kan hämtas längre.
2.  Ge slutanvändarna möjlighet att selektivt Hämta video kvalitets lager och ljud spår i stället för allt innehåll. För offline-läge finns det ingen punkt för att ladda ned alla kvalitets lager. Det finns två sätt att åstadkomma detta:
    1.  Kontrollerad av klienten: antingen väljer Player-appen automatiskt eller så kan användaren välja video kvalitets lager och ljud spår för att ladda ned.
    2.  Tjänsten kontrollerad: en kan använda funktionen dynamisk manifest i Azure Media Services för att skapa ett (Globalt) filter, vilket begränsar HLS-spelnings lista eller streck-MPD till ett enda video kvalitets lager och valda ljud spår. Hämtnings-URL: en som visas för slutanvändarna inkluderar det här filtret.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="summary"></a>Sammanfattning

Den här artikeln beskrivs hur du implementerar uppspelning i offlineläge för streck innehåll som skyddas av Widevine på Android-enheter.  Det besvarade även några vanliga frågor om offline-strömning av Widevine-skyddat innehåll.
