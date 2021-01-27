---
title: Skydda ditt innehåll med Media Services v3 dynamisk kryptering
description: Lär dig mer om innehålls skydd med dynamisk kryptering, direkt uppspelnings protokoll och krypterings typer i Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: edea46e86475f1731ecfae465f969ca45d63f0a2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897636"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Skydda ditt innehåll med Media Services dynamisk kryptering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Använd Azure Media Services för att skydda dina medier från den tid det lämnar datorn hela vägen genom lagring, bearbetning och leverans. Med Media Services kan du leverera direktsänd och innehåll på begäran som krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre större Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. Om innehållet krypteras med en AES-klar nyckel och skickas via HTTPS, är det inte i klartext förrän klienten når klienten.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

I Media Services v3 är en innehålls nyckel kopplad till streaming Locator (se [det här exemplet](protect-with-aes128.md)). Om du använder Media Services Key Delivery Service kan du låta Azure Media Services generera innehålls nyckeln åt dig. Innehålls nyckeln bör genereras själv om du använder din egen nyckel leverans tjänst eller om du behöver hantera ett scenario med hög tillgänglighet där du behöver samma innehålls nyckel i två Data Center.

När en data ström begärs av en spelare, använder Media Services den angivna nyckeln för att dynamiskt Kryptera ditt innehåll med hjälp av AES Clear Key eller DRM-kryptering. För att dekryptera data strömmen begär spelaren nyckeln från Media Services Key Delivery Service eller den nyckel leverans tjänst som du har angett. För att avgöra om användaren har behörighet att hämta nyckeln, utvärderar tjänsten den innehålls nyckel princip som du har angett för nyckeln.

Du kan använda REST API eller ett Media Services klient bibliotek för att konfigurera principer för auktorisering och autentisering för dina licenser och nycklar.

Följande bild illustrerar arbets flödet för Media Services innehålls skydd:

![Arbets flöde för Media Services innehålls skydd](./media/content-protection/content-protection.svg)
  
&#42; *dynamisk kryptering stöder AES-128 Clear Key, CBCS och Cenc. Mer information finns i [support mat ris](#streaming-protocols-and-encryption-types).*

Den här artikeln förklarar begrepp och terminologi som hjälper dig att förstå innehålls skydd med Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Huvud komponenter i ett innehålls skydds system

För att kunna slutföra ditt innehålls skydds system måste du fullständigt förstå omfattningen av ansträngningen. I följande avsnitt får du en översikt över tre delar som du måste implementera.

> [!NOTE]
> Vi rekommenderar starkt att du fokuserar och testar varje del i följande avsnitt innan du går vidare till nästa del. Om du vill testa ditt innehålls skydds system använder du de verktyg som anges i avsnitten.

### <a name="media-services-code"></a>Media Services kod
  
[DRM-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) visar hur du implementerar ett multi-DRM-system med Media Services v3 med hjälp av .net. Det visar också hur du använder tjänsten för Media Services licens/nyckel-leverans.
  
Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). För att se vad som är meningsfullt att kombinera, se [strömmande protokoll och krypterings typer](#streaming-protocols-and-encryption-types).

Exemplet visar hur du:

1. Skapa och konfigurera en [princip för innehålls nyckel](content-key-policy-concept.md).

   Du skapar en innehålls nyckel princip för att konfigurera hur innehålls nyckeln (som ger säker åtkomst till till gångar) levereras till slut klienter:  

   * Definiera auktorisering av licens leverans. Ange logiken för verifierings kontrollen baserat på anspråk i JSON Web Token (JWT).
   * Konfigurera [PlayReady](playready-license-template-overview.md)-, [Widevine](widevine-license-template-overview.md)-och/eller [Fairplay](fairplay-license-overview.md) -licenser. Med mallarna kan du konfigurera rättigheter och behörigheter för var och en av DRM: er.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Skapa en [strömmande lokalisering](streaming-locators-concept.md) som är konfigurerad för att strömma den krypterade till gången.
  
   Den strömmande lokaliseraren måste kopplas till en [strömmande princip](streaming-policy-concept.md). I det här exemplet har vi angett `StreamingLocator.StreamingPolicyName` till principen "Predefined_MultiDrmCencStreaming".

   PlayReady-och Widevine-krypteringarna tillämpas och nyckeln levereras till uppspelnings klienten baserat på de konfigurerade DRM-licenserna. Om du även vill kryptera din ström med CBCS (FairPlay) använder du principen "Predefined_MultiDrmStreaming".

   Den strömmande lokaliseraren är också kopplad till den innehålls nyckel princip som du har definierat.

3. Skapa en testtoken.

   `GetTokenAsync`Metoden visar hur du skapar en testtoken.
4. Bygg direkt uppspelnings-URL: en.

   `GetDASHStreamingUrlAsync`Metoden visar hur du skapar strömnings-URL: en. I det här fallet strömmar URL-innehållet.

### <a name="player-with-an-aes-or-drm-client"></a>Spelare med en AES-eller DRM-klient

En Video Player-app som baseras på en Player SDK (antingen ursprunglig eller webbläsarbaserad) måste uppfylla följande krav:

* Media Player SDK stöder de DRM-klienter som behövs.
* Media Player SDK stöder de protokoll som krävs för strömning: utjämna, streck och/eller HTTP Live Streaming (HLS).
* Player SDK kan hantera överföring av en JWT-token i en begäran om licens hämtning.

Du kan skapa en spelare med hjälp av [Azure Media Player-API: et](https://amp.azure.net/libs/amp/latest/docs/). Använd [Azure Media Player ProtectionInfo-API](https://amp.azure.net/libs/amp/latest/docs/) för att ange vilken DRM-teknik som ska användas på olika DRM-plattformar.

För att testa AES eller CENC (Widevine och/eller PlayReady) krypterat innehåll kan du använda [Azure Media Player](https://aka.ms/azuremediaplayer). Se till att du väljer **Avancerade alternativ** och kontrol lera krypterings alternativen.

Om du vill testa FairPlay-krypterat innehåll använder du [den här test spelaren](https://aka.ms/amtest). Spelaren stöder Widevine-, PlayReady-och FairPlay-DRM: er, tillsammans med AES-128-kryptering med tydliga nycklar.

Välj rätt webbläsare för att testa olika DRM: er:

* Chrome, Opera eller Firefox för Widevine.
* Microsoft Edge eller Internet Explorer 11 för PlayReady.
* Safari på macOS för FairPlay.

### <a name="security-token-service"></a>Security Token Service

En säkerhetstokentjänst (STS) utfärdar JWT som åtkomsttoken för backend-resursens åtkomst. Du kan använda Azure Media Services licens-/nyckel leverans tjänst som backend-resurs. En STS måste definiera följande saker:

* Utfärdare och mål grupp (eller omfång).
* Anspråk, som är beroende av affärs kraven i innehålls skyddet.
* Symmetrisk eller asymmetrisk verifiering för signaturverifiering.
* Stöd för nyckel förnyelse (vid behov).

Du kan använda [det här STS-verktyget](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) för att testa STS. Den har stöd för alla tre typerna av verifierings nycklar: symmetrisk, asymmetrisk eller Azure Active Directory (Azure AD) med nyckel förnyelse.

## <a name="streaming-protocols-and-encryption-types"></a>Protokoll och krypteringstyper för direktuppspelning

Du kan använda Media Services för att leverera innehåll som krypterats dynamiskt med AES-rensning eller DRM-kryptering med hjälp av PlayReady, Widevine eller FairPlay. För närvarande kan du kryptera HLS, MPEG-streck och Smooth Streaming format. Varje protokoll stöder följande krypterings metoder.

### <a name="hls"></a>HLS

HLS-protokollet stöder följande behållar format och krypterings scheman:

|Behållar format|Krypterings schema|URL-exempel|
|---|---|---|
|Alla|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2 – TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2 – TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (inklusive HEVC/H. 265) stöds på följande enheter:

* iOS 11 eller senare.
* iPhone 8 eller senare.
* macOS hög Sierra med Intels sjunde generations processor.

### <a name="mpeg-dash"></a>MPEG-STRECK

MPEG-streck-protokollet stöder följande behållar format och krypterings scheman:

|Behållar format|Krypterings schema|URL-exempel
|---|---|---|
|Alla|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF (fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Smooth Streaming-protokollet stöder följande behållar format och krypterings scheman.

|Protokoll|Behållar format|Krypterings schema|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|
|fMP4 | PIFF 1,1 (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1,1-support tillhandahålls som en bakåtkompatibla lösning för smart TV (Samsung, LG) som implementerade den tidiga "Silverlight-versionen av Common Encryption. Vi rekommenderar att du bara använder det PIFF-format som krävs för att stöda legacey Samsung eller LG Smart TV levereras mellan 2009-2015 som stöder PIFF 1,1-versionen av PlayReady-kryptering. 

### <a name="browsers"></a>Webbläsare

Vanliga webbläsare stöder följande DRM-klienter:

|Webbläsare|Kryptering|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Styra åtkomst till innehåll

Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera innehålls nyckel principen. Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Klienten (spelaren) måste uppfylla principen innan nyckeln kan levereras till klienten. Principen för innehålls nyckeln kan ha begränsningen *Open* eller *token* .

En princip med öppen begränsad innehålls nyckel kan användas när du vill utfärda licens till någon utan auktorisering. Till exempel, om din intäkt är AD-baserad och inte för prenumerations-baserad.  

Med en princip för en token-begränsad innehålls nyckel skickas innehålls nyckeln endast till en klient som presenterar en giltig JWT-token eller en enkel webbtoken (SWT) i licens-/Key-begäran. Denna token måste utfärdas av en STS.

Du kan använda Azure AD som STS eller distribuera en [anpassad STS](#using-a-custom-sts). STS måste konfigureras för att skapa en token som signerats med den angivna nyckeln och utfärda anspråk som du angav i konfigurationen för token-begränsning. Media Services licens-/nyckel leverans tjänst returnerar den begärda licensen eller nyckeln till klienten om båda dessa villkor föreligger:

* Token är giltig.
* Anspråk i token matchar de som har kon figurer ATS för licensen eller nyckeln.

När du konfigurerar den token-begränsade principen måste du ange primär verifierings nyckel, utfärdare och mål grupps parametrar. Den primära verifierings nyckeln innehåller den nyckel som token har signerats med. Utfärdaren är den STS som utfärdar token. Mål gruppen, som ibland kallas omfång, beskriver syftet med den token eller resurs som token tillåter åtkomst till. Media Services licens-/nyckel leverans tjänst validerar att dessa värden i token matchar värdena i mallen.

### <a name="token-replay-prevention"></a>Förhindra repetition av token

Funktionen för att *förhindra repetition av token* tillåter Media Services kunder att ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Kunden kan lägga till ett anspråk av typen `urn:microsoft:azure:mediaservices:maxuses` i token, där värdet är antalet gånger som token kan användas för att hämta en licens eller nyckel. Alla efterföljande förfrågningar med samma token till nyckel leverans returnerar ett obehörigt svar. Se hur du lägger till anspråk i [DRM-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Överväganden

* Kunder måste ha kontroll över genereringen av token. Anspråket måste placeras i själva token.
* När du använder den här funktionen avvisas begär Anden med token vars utgångs tid är mer än en timme från den tidpunkt då begäran tas emot avvisas med ett obehörigt svar.
* Tokens identifieras unikt av signaturen. Alla ändringar av nytto lasten (till exempel uppdatering till förfallo tiden eller anspråket) ändrar signaturen för token och den räknas som en ny token som nyckel leveransen inte tar emot över innan.
* Uppspelningen Miss lyckas om token har överskridit `maxuses` värdet som anges av kunden.
* Den här funktionen kan användas för allt befintligt skyddat innehåll (bara token som utfärdats måste ändras).
* Den här funktionen fungerar med både JWT och SWT.

## <a name="using-a-custom-sts"></a>Använda en anpassad STS

En kund kan välja att använda en anpassad STS för att tillhandahålla tokens. Orsaker är:

* Identitets leverantören (IDP) som används av kunden har inte stöd för STS. I det här fallet kan en anpassad STS vara ett alternativ.
* Kunden kan behöva mer flexibel eller tätare kontroll för att integrera STS med kundens fakturerings system.

   En [ott](https://en.wikipedia.org/wiki/Over-the-top_media_services) tjänst operatör kan till exempel erbjuda flera prenumerations paket, till exempel Premium, Basic och idrotts. Operatören kanske vill matcha anspråk i en token med ett prenumerations paket så att endast innehållet i ett särskilt paket blir tillgängligt. I det här fallet ger en anpassad STS nödvändig flexibilitet och kontroll.

* Om du vill inkludera anpassade anspråk i token för att välja mellan olika ContentKeyPolicyOptions med olika parametrar för DRM-licenser (en prenumerations licens jämfört med en hyres licens).
* Ta med ett anspråk som representerar nyckel identifieraren för nyckeln som token beviljar åtkomst till.

När du använder en anpassad STS måste du göra två ändringar:

* När du konfigurerar licens leverans tjänsten för en till gång måste du ange den säkerhets nyckel som ska användas för verifiering av den anpassade STS i stället för den aktuella nyckeln från Azure AD.
* När en JTW-token skapas, anges en säkerhets nyckel i stället för den privata nyckeln för det aktuella X509-certifikatet i Azure AD.

Det finns två typer av säkerhets nycklar:

* Symmetrisk nyckel: samma nyckel används för att generera och verifiera en JWT.
* Asymmetrisk nyckel: ett offentligt privat privat nyckel par i ett X509-certifikat används med en privat nyckel för att kryptera/generera en JWT och med den offentliga nyckeln för att verifiera token.

Om du använder .NET Framework/C# som utvecklings plattform måste X509-certifikatet som används för en asymmetrisk säkerhets nyckel ha en nyckel längd på minst 2048. Den här nyckel längden är ett krav för klassen system. IdentityModel. tokens. X509AsymmetricSecurityKey i .NET Framework. Annars genereras följande undantag: IDX10630: system. IdentityModel. tokens. X509AsymmetricSecurityKey för signering får inte vara mindre än 2048 bitar.

## <a name="custom-key-and-license-acquisition-url"></a>URL för anpassad nyckel och licens hämtning

Använd följande mallar om du vill ange en annan licens-/nyckel leverans tjänst (inte Media Services). Det finns två utbytbara fält i mallarna så att du kan dela din strömmande princip över många till gångar i stället för att skapa en strömmande princip per till gång. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: Mall för URL: en för den anpassade tjänsten som levererar nycklar till slut användar spelarna. Det krävs inte när du använder Azure Media Services för att utfärda nycklar. 

   Mallen stöder utbytbara token som tjänsten kommer att uppdatera vid körning med värdet som är kopplat till begäran.  De token-värden som stöds för närvarande är:
   * `{AlternativeMediaId}`, som ersätts med värdet för StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, som ersätts med värdet för identifieraren för den begärda nyckeln.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Mall för URL: en för den anpassade tjänsten som levererar licenser till slut användar spelarna. Det krävs inte när du använder Azure Media Services för utfärdande av licenser.

   Mallen stöder utbytbara token som tjänsten kommer att uppdatera vid körning med värdet som är kopplat till begäran. De token-värden som stöds för närvarande är:  
   * `{AlternativeMediaId}`, som ersätts med värdet för StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, som ersätts med värdet för identifieraren för den begärda nyckeln. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Samma som föregående mall, endast för Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Samma som föregående mall, endast för FairPlay.  

Exempel:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` har ett värde för den begärda nyckeln. Du kan använda `AlternativeMediaId` om du vill mappa begäran till en entitet på din sida. Kan till exempel `AlternativeMediaId` användas för att söka efter behörigheter.

För REST-exempel som använder anpassade URL: er för licens-/nyckel hämtning, se [strömmande principer-skapa](/rest/api/media/streamingpolicies/create).

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="troubleshoot"></a>Felsöka

Om du får `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` felet kontrollerar du att du anger lämplig strömmande princip.

Om du får fel som slutar med ser `_NOT_SPECIFIED_IN_URL` du till att du anger krypterings formatet i URL: en. Ett exempel är `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Se [strömmande protokoll och krypterings typer](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* [Skydda med AES-kryptering](protect-with-aes128.md)
* [Skydda med DRM](protect-with-drm.md)
* [Designa multi-DRM innehålls skydds system med åtkomst kontroll](design-multi-drm-system-with-access-control.md)
* [Kryptering på lagrings Sidan](storage-account-concept.md#storage-side-encryption)
* [Vanliga frågor och svar](frequently-asked-questions.md)
* [JSON Web Token hanterare](/dotnet/framework/security/json-web-token-handler)
