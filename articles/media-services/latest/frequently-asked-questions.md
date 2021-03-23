---
title: Vanliga frågor och svar om Azure Media Services v3
description: Den här artikeln innehåller svar på vanliga frågor om Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: d1c2fd2ac7f809b4a70a0edffbddfb6e733fed57
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871409"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Vanliga frågor och svar om Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln innehåller svar på vanliga frågor om Azure Media Services v3.

## <a name="general"></a>Allmänt

### <a name="does-media-services-store-any-customer-data-outside-of-the-service-region"></a>Lagrar Media Services alla kund uppgifter utanför tjänst regionen?

- Kunder ansluter sina egna lagrings konton till sina Azure Media Services-konton.  Alla till gångs data lagras i dessa associerade lagrings konton och kunden styr platsen och replikeringens typ av lagringen.
- Ytterligare data som är associerade med Media Services konto (inklusive innehålls krypterings nycklar, verifierings nycklar för token, JobInputHttp-URL: er och andra entitets-metadata) lagras i Microsofts ägda lagring inom den region som valts för det Media Services kontot.
    - På grund av [placering-krav för data](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) i brasilien och Sydostasien, lagras ytterligare konto data i zoner som är redundanta och finns i en enda region. För Sydostasien lagras alla ytterligare konto data i Singapore och för södra Brasilien lagras data i Brasilien.
    - I andra regioner än Brasilien, södra och Sydostasien, kan ytterligare konto data också lagras i Microsoft-ägda lagrings utrymmen i den [kopplade regionen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).
- Azure Media Services är en regional tjänst och ger inte [hög tillgänglighet](media-services-high-availability-encoding.md) eller datareplikering. Kunder som behöver dessa funktioner rekommenderas att bygga en lösning med hjälp av Media Services konton i flera regioner.  Ett exempel som visar hur du skapar en lösning för hög tillgänglighet med Media Services video på begäran finns i en guide.

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Vilka Azure Portal begränsningar för Media Services v3?

Du kan använda [Azure Portal](https://portal.azure.com/) för att hantera v3 Live-händelser, Visa v3-till gångar och jobb, hämta information om åtkomst till API: er, Kryptera innehåll. <br/>För alla andra hanterings aktiviteter (till exempel hantering av transformeringar och jobb eller analys av v3-innehåll) använder du [REST API](/rest/api/media/accountfilters), [CLI](/cli/azure/ams)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.

Om din video tidigare har laddats upp till Media Services kontot med hjälp av Media Services v3 API eller om innehållet genererades baserat på Live-utdata, visas inte knapparna **koda**, **analysera** eller **kryptera** i Azure Portal. Använd Media Services v3-API: erna för att utföra dessa uppgifter.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Vilka Azure-roller kan utföra åtgärder på Azure Media Services resurser? 

Se [Azure-rollbaserad åtkomst kontroll (Azure RBAC) för Media Services-konton](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Vill du Hur gör jag för att Stream till Apple iOS-enheter?

Kontrol lera att du har **(format = M3U8-AAPL)** i slutet av din sökväg (efter **/manifest** -delen av URL: en) för att meddela att den strömmande ursprungs servern returnerar HTTP Live Streaming (HLS) innehåll för användning på Apple iOS-enheter. Mer information finns i avsnittet om att [leverera innehåll](dynamic-packaging-overview.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Vad är den rekommenderade metoden för att bearbeta videor?

Använd [transformeringar](/rest/api/media/transforms) för att konfigurera vanliga aktiviteter för kodning eller analys av videor. Varje transformering beskriver ett recept, eller ett arbets flöde för uppgifter för bearbetning av video-eller ljudfiler. Ett [jobb](/rest/api/media/jobs) är den faktiska begäran om Media Services att tillämpa transformeringen på ett indata-eller ljud innehåll. När transformeringen har skapats kan du skicka jobb genom att använda Media Services API: er eller någon av de publicerade SDK: erna. Mer information finns i [Transformeringar och jobb](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Jag har laddat upp, kodat och publicerat en video. Varför spelas inte videon upp när jag försöker strömma den?

En av de vanligaste orsakerna är att du inte har den slut punkt för direkt uppspelning som du försöker spela upp i körnings läge.

### <a name="how-does-pagination-work"></a>Hur fungerar sid brytning?

När du använder sid brytning bör du alltid använda nästa länk för att räkna upp samlingen och inte beroende av en viss sid storlek. Mer information och exempel finns i [filtrering, sortering, växling](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Vilka funktioner är inte tillgängliga än i Azure Media Services v3?

Mer information finns i [migreringsguiden](migrate-v-2-v-3-migration-introduction.md).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Vad är processen att flytta ett Media Services konto mellan prenumerationer?  

Mer information finns i [Flytta ett Media Services konto mellan prenumerationer](media-services-account-concept.md).

## <a name="live-streaming"></a>Liveuppspelning 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Hur gör jag för att stoppar du den aktiva strömmen när sändningen är färdig?

Du kan närma dig detta från klient sidan eller Server sidan.

#### <a name="client-side"></a>Klient Sidan

Ditt webb program bör fråga användaren om de vill avsluta sändningen när webbläsaren stängs. Detta är en webb läsar händelse som ditt webb program kan hantera.

#### <a name="server-side"></a>Server sida

Du kan övervaka Live-händelser genom att prenumerera på Azure Event Grid händelser. Mer information finns i [händelse schema för EventGrid](monitoring/media-services-event-schemas.md#live-event-types).

Du kan antingen:

* [Prenumerera](monitoring/reacting-to-media-services-events.md) på data strömmens [Microsoft. Media. LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md#liveeventencoderdisconnected) -händelser och övervaka att det inte finns några åter anslutningar i ett tag för att stoppa och ta bort din Live-händelse.
* [Prenumerera](monitoring/reacting-to-media-services-events.md) på händelser på spår nivåns [pulsslag](monitoring/media-services-event-schemas.md#liveeventingestheartbeat) . Om alla spår har en inkommande bit hastighet som släpps till 0 eller om den sista tidsstämpeln inte längre ökar, kan du stänga av direkt sändningen på ett säkert sätt. Pulsslags händelserna kommer in var 20: e sekund för varje spår, så det kan vara en bit utförlig.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Hur gör jag för att infoga brytningar/videor och bildfärgare under en Live-ström?

Media Services v3 Live encoding stöder ännu inte infogning av video-eller bildskärmar under Live Stream. 

Du kan använda en [aktiv lokal kodare](recommended-on-premises-live-encoders.md) för att växla mellan käll videon. Många appar ger möjlighet att byta källor, inklusive Wirecast för multistream, växlaren Studio (på iOS) och ONLINEBANKSYSTEM Studio (gratis app).

## <a name="content-protection"></a>Innehållsskydd

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Bör jag använda AES-128-kryptering av nyckel eller DRM-system?

Kunderna undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den största skillnaden mellan de två systemen är att med AES-kryptering överförs innehålls nyckeln till klienten via TLS så att nyckeln krypteras under överföringen men utan ytterligare kryptering ("i klartext"). Det innebär att den nyckel som används för att dekryptera innehållet är tillgänglig för klient spelaren och kan visas i ett nätverks spår på klienten i klartext. AES-128-kryptering är lämplig för användnings fall där visnings programmet är en betrodd part (till exempel kryptering av företags videor som distribueras i ett företag som kan ses av anställda).

DRM-system som PlayReady, Widevine och FairPlay ger alla ytterligare krypterings nivåer på den nyckel som används för att dekryptera innehållet, jämfört med en AES-128-klar nyckel. Innehålls nyckeln krypteras till en nyckel som skyddas av DRM-körningen utöver all kryptering på transport nivå som tillhandahålls av TLS. Dekrypteringen hanteras dessutom i en säker miljö på nivån operativ system, där det är svårare för en angripare att attackera angrepp. Vi rekommenderar DRM för användnings fall där visnings programmet kanske inte är en betrodd part och du behöver den högsta säkerhets nivån.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hur gör jag för att bara visa en video till användare som har en särskild behörighet utan att använda Azure AD?

Du behöver inte använda någon speciell token-provider som Azure Active Directory (Azure AD). Du kan skapa en egen [JWT](https://jwt.io/) -Provider (so-kallas Secure token service) eller STS genom att använda asymmetrisk nyckel kryptering. I din anpassade STS kan du lägga till anspråk baserat på din affärs logik.

Se till att utfärdaren, mål gruppen och alla anspråk matchar exakt mellan vad som är i JWT och värdet som `ContentKeyPolicyRestriction` används i `ContentKeyPolicy` .

Mer information finns i [skydda ditt innehåll med hjälp av Media Services dynamisk kryptering](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Hur och var fick jag en JWT-token innan jag använder den för att begära en licens eller nyckel?

För produktion måste du ha en säker token-tjänst (det vill säga en webb tjänst), som utfärdar en JWT-token vid en HTTPS-begäran. För test kan du använda koden som visas i den `GetTokenAsync` metod som definierats i [programmet. cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

Spelaren gör en begäran, efter att en användare har autentiserats, till STS för en sådan token och tilldelar den som token-värde. Du kan använda [Azure Media Player-API: et](https://amp.azure.net/libs/amp/latest/docs/).

Ett exempel på att köra STS med antingen en symmetrisk nyckel eller en asymmetrisk nyckel finns i [JWT-verktyget](https://aka.ms/jwt). Ett exempel på en spelare som bygger på Azure Media Player som använder en sådan JWT-token finns i [Azure Media Test Tool](https://aka.ms/amtest). (Expandera **player_settings** -länken för att Visa token-indatamängden.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hur gör jag för att tillåta förfrågningar om att strömma videor med AES-kryptering?

Den korrekta metoden är att använda Secure token service. I STS, beroende på användar profilen, lägger du till olika anspråk (till exempel "Premium användare", "grundläggande användare," "kostnads fri utvärderings användare"). Med olika anspråk i ett JWT kan användaren se olika innehåll. För olika innehåll och till gångar får `ContentKeyPolicyRestriction` motsvarande `RequiredClaims` värde.

Använd Azure Media Services-API: er för att konfigurera licens-/nyckel leverans och kryptera dina till gångar (som visas i [det här exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Mer information finns i:

- [Översikt över innehållsskydd](content-protection-overview.md)
- [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Ska jag använda HTTP eller HTTPS?
ASP.NET MVC Player-programmet måste ha stöd för följande:

* Användarautentisering via Azure AD, som är under HTTPS.
* JWT Exchange mellan klienten och Azure AD, som är under HTTPS.
* Hämtning av DRM-licenser av klienten, som måste vara under HTTPS om licens leverans tillhandahålls av Media Services. PlayReady-produktsortimentet mäter inte HTTPS för licens leverans. Om din PlayReady-licensserver är utanför Media Services kan du använda antingen HTTP eller HTTPS.

ASP.NET Player-programmet använder HTTPS som bästa praxis, så Media Player finns på en sida under HTTPS. HTTP rekommenderas dock för strömning, så du måste tänka på de här problemen med blandat innehåll:

* Webbläsaren tillåter inte blandat innehåll. Men plugin-program som Silverlight och OSMF-plugin-programmet för smidig och streck tillåter det. Blandat innehåll är ett säkerhets problem på grund av hotet om möjligheten att mata in skadlig Java Script, vilket kan ge kund information till risk. Webbläsare blockerar den här funktionen som standard. Det enda sättet att undvika det finns på servern (ursprungs sidan) genom att tillåta alla domäner (oavsett HTTPS eller HTTP). Detta är förmodligen ingen bra idé.
* Undvik blandat innehåll. Både Player-programmet och Media Player ska använda HTTP eller HTTPS. När du spelar blandat innehåll kräver Silverlight-Tech att du avmarkerar en varning med blandat innehåll. Den blixt tekniska tekniken hanterar blandat innehåll utan varnings meddelande med blandat innehåll.
* Om slut punkten för direkt uppspelning skapades före 2014 augusti stöder den inte HTTPS. I det här fallet skapar och använder du en ny slut punkt för direkt uppspelning för HTTPS.

### <a name="what-about-live-streaming"></a>Vad händer om Direktsänd strömning?

Du kan använda exakt samma design och implementering för att skydda Direktsänd strömning i Media Services genom att behandla till gången som är kopplad till ett program som en VOD till gång. Om du vill tillhandahålla ett multi-DRM-skydd för Live-innehållet använder du samma installation/bearbetning till till gången som om det vore en VOD till gång innan du kopplar till gången till Live-utdata.

### <a name="what-about-license-servers-outside-media-services"></a>Vad gäller för licens servrar utanför Media Services?

Kunder har ofta investerat i en licens Server grupp, antingen i ett eget Data Center eller i en värd av DRM-tjänst leverantörer. Med Media Services innehålls skydd kan du arbeta i hybrid läge. Innehåll kan vara värd för och dynamiskt skyddas i Media Services, medan DRM-licenser levereras av servrar utanför Media Services. I det här fallet bör du tänka på följande ändringar:

* STS måste utfärda token som är acceptabla och kan verifieras av licens Server gruppen. Till exempel kräver Widevine-licensservern som tillhandahålls av Axinom ett speciellt JWT som innehåller ett rättighets meddelande. Du måste ha en STS för att kunna utfärda ett JWT. 
* Du behöver inte längre konfigurera licens leverans tjänsten i Media Services. Du måste ange URL: er för licens hämtning (för PlayReady, Widevine och FairPlay) när du konfigurerar `ContentKeyPolicy` .

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google och omfattas av villkoren i tjänste-och sekretess policyn för Google.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan jag använda Azure Portal för att hantera v3-resurser?

För närvarande kan du använda [Azure Portal](https://portal.azure.com/) för att:

* Hantera [Live-händelser](live-events-outputs-concept.md) i Media Services v3. 
* Visa (inte hantera) v3- [till gångar](assets-concept.md). 
* [Hämta information om att komma åt API: er](./access-api-howto.md). 

För alla andra hanterings uppgifter (t. ex. [transformationer och jobb](transforms-jobs-concept.md) och [innehålls skydd](content-protection-overview.md)) använder du [REST API](/rest/api/media/), [Azure CLI](/cli/azure/ams)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Finns det ett AssetFile-koncept i v3?

`AssetFile`Konceptet togs bort från Media Services API för att separera Media Services från Storage SDK-beroendet. Nu Azure Storage, inte Media Services, behåller den information som ingår i Storage SDK: n. 

Mer information finns i [migrera till Media Services v3](migrate-v-2-v-3-migration-introduction.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Var finns lagrings kryptering på klient Sidan?

Vi rekommenderar nu att du använder lagrings kryptering på Server sidan (som är aktiverat som standard). Mer information finns i [Azure Storage tjänst kryptering för vilande data](../../storage/common/storage-service-encryption.md).

## <a name="offline-streaming"></a>Offline-direktuppspelning

### <a name="fairplay-streaming-for-ios"></a>FairPlay-strömning för iOS

Följande vanliga frågor ger hjälp med fel sökning av offline-FairPlay strömning för iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Varför spelas bara ljud upp men inte video i offlineläge?

Det här beteendet verkar vara avsiktligt av exempel appen. När ett alternativt ljud spår finns (vilket är fallet för HLS) under offlineläge, är både iOS 10 och iOS 11 standardvärdet för det alternativa ljud spåret. För att kompensera det här beteendet för offline-läge för FPS tar du bort det alternativa ljud spåret från data strömmen. Om du vill göra detta på Media Services lägger du till det dynamiska manifest filtret **endast ljud-Only = false**. Med andra ord slutar en HLS-URL med **. ISM/manifest (format = M3U8-AAPL, endast ljud = falskt)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Varför spelar det fortfarande bara ljud utan video i offlineläge efter att jag lagt till endast ljud = falskt?

Beroende på den cachelagrade nyckel designen för Content Delivery Network kan innehållet vara cachelagrat. Rensa cacheminnet.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Stöds offline-läge för FPS i iOS 11 förutom iOS 10?

Ja. Offline-läge för FPS stöds för iOS 10 och iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Varför kan jag inte hitta dokumentet "offline-uppspelning med FairPlay streaming och HTTP Live Streaming" i serverns SDK för FPS?

Eftersom FPS Server SDK version 4 har det här dokumentet slagits samman i programmerings guiden "FairPlay streaming".

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Vad är den nedladdade/offline-filstrukturen på iOS-enheter?

Den nedladdade fil strukturen på en iOS-enhet ser ut som på följande skärm bild. `_keys`Mappen lagrar hämtade fps-licenser med en lagrings fil för varje licens tjänst värd. `.movpkg`Mappen lagrar ljud-och video innehåll. 

Den första mappen med ett namn som slutar med ett streck följt av ett tal innehåller video innehåll. Det numeriska värdet är video åter givningarnas högsta bandbredd. Den andra mappen med ett namn som slutar med ett bindestreck följt av 0 innehåller ljud innehåll. Den tredje mappen `Data` innehåller huvud spelnings listan för innehållet i FPS. Slutligen boot.xml ger en fullständig beskrivning av innehållet i `.movpkg` mappen. 

![Fil struktur offline för FairPlay iOS-exempelprogrammet](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Här är ett exempel på en boot.xml fil:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Widevine-strömning för Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Hur kan jag leverera beständiga licenser (offline aktiverat) för vissa klienter/användare och icke-beständiga licenser (offline-inaktive rad) för andra? Måste jag duplicera innehållet och använda separata innehålls nycklar?

Eftersom Media Services v3 tillåter att en till gång har flera `StreamingLocator` instanser kan du ha:

* En `ContentKeyPolicy` instans med `license_type = "persistent"` , `ContentKeyPolicyRestriction` med anspråk på `"persistent"` och dess `StreamingLocator` .
* En annan `ContentKeyPolicy` instans med `license_type="nonpersistent"` , `ContentKeyPolicyRestriction` med anspråk på `"nonpersistent` "och dess `StreamingLocator` .
* Två `StreamingLocator` instanser som har olika `ContentKey` värden.

Beroende på affärs logik för anpassad STS utfärdas olika anspråk i JWT-token. Med token kan endast motsvarande licens hämtas, och endast motsvarande URL kan spelas upp.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Vad är mappningen mellan Widevine och Media Services säkerhets nivåer för DRM?

Översikt över Googles "Widevine DRM-arkitektur" definierar tre säkerhets nivåer. [Azure Media Services-dokumentationen i Widevine-licensservern](widevine-license-template-overview.md) beskriver dock fem säkerhets nivåer (krav för klient stabilitet för uppspelning). I det här avsnittet beskrivs hur säkerhets nivåer kartan.

Båda uppsättningarna med säkerhets nivåer definieras av Google Widevine. Skillnaden är i användnings nivå: arkitektur eller API. De fem säkerhets nivåerna används i Widevine-API: et. `content_key_specs`Objektet som innehåller `security_level` deserialiseras och skickas till den globala Widevine-leverans tjänsten av tjänsten Azure Media Services Widevine License. I följande tabell visas mappningen mellan de två uppsättningarna med säkerhets nivåer.

| **Säkerhets nivåer som definieras i Widevine-arkitekturen** |**Säkerhets nivåer som används i Widevine-API**|
|---|---| 
| **Säkerhets nivå 1**: all innehålls bearbetning, kryptografi och kontroll utförs i tee (Trusted Execution Environment). I vissa implementerings modeller kan säkerhets bearbetningen utföras i olika kretsar.|**security_level = 5**: kryptering, avkodning och all hantering av mediet (komprimerade och okomprimerade) måste hanteras inom en maskinvarubaserad tee.<br/><br/>**security_level = 4**: kryptering och avkodning av innehåll måste utföras inom en maskinvarubaserad tee.|
**Säkerhets nivå 2**: kryptografi (men inte video bearbetning) utförs i tee. Dekrypterade buffertar returneras till program domänen och bearbetas via separat video maskin vara eller program vara. På nivå 2 bearbetas dock kryptografisk information fortfarande endast inom TEE.| **security_level = 3**: nyckel material-och krypterings åtgärder måste utföras inom en maskinvarubaserad tee. |
| **Säkerhets nivå 3**: det finns ingen tee på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografisk information och dekrypterat innehåll på värd operativ system. En nivå 3-implementering kan också innehålla en kryptografisk motor för maskin vara, men den förbättrar bara prestanda, inte säkerhet. | **security_level = 2**: program varu kryptering och en fördunklade-avkodare krävs.<br/><br/>**security_level = 1**: programvarubaserade kryptografiska krypteringar krävs.|

#### <a name="why-does-content-download-take-so-long"></a>Varför tar det lång tid för innehålls hämtningen?

Det finns två sätt att förbättra nedladdnings hastigheten:

* Aktivera ett nätverk för innehålls leverans så att användarna är mer sannolika i stället för slut punkten för slut punkten/direkt uppspelningen för innehålls hämtning. Om en användare träffar en slut punkt för direkt uppspelning paketeras och krypteras varje HLS segment eller ett streck fragment. Även om den här svars tiden är i millisekunder för varje segment eller fragment, kan den ackumulerade svars tiden vara stor och göra en längre nedladdning om du har en timmes lång video.
* Ge användarna möjlighet att selektivt Hämta video kvalitets lager och ljud spår i stället för allt innehåll. För offline-läge finns det ingen punkt där du kan hämta alla kvalitets lager. Det finns två sätt att åstadkomma detta:

  * Kontrollerad klient: Player-appen väljs automatiskt, eller så väljer användaren video kvalitet lagret och ljud spåren som ska laddas ned.
  * Tjänsten kontrollerad: du kan använda funktionen för dynamiskt manifest i Azure Media Services för att skapa ett (Globalt) filter, vilket begränsar HLS-spelnings lista eller streck-MPD till ett enda video kvalitets lager och valda ljud spår. Hämtnings-URL: en som visas för användarna kommer att inkludera det här filtret.

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)