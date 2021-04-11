---
title: Konfigurera en auktoriseringsprincip för innehålls nycklar med hjälp av Azure Portal | Microsoft Docs
description: Den här artikeln visar hur du konfigurerar en auktoriseringsprincip för en innehålls nyckel.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 68bd4bd2472e80a663294745368fb505767a230a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008322"
---
# <a name="configure-a-content-key-authorization-policy"></a>Konfigurera en auktoriseringsprincip för innehålls nycklar

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Översikt
 Du kan använda Azure Media Services för att leverera MPEG-streck, Smooth Streaming och HTTP Live Streaming (HLS) som skyddas med Advanced Encryption Standard (AES) genom att använda 128-bitars krypterings nycklar eller [PlayReady Digital Rights Management (DRM)](https://www.microsoft.com/playready/overview/). Med Media Services kan du också leverera streck strömmar krypterade med Widevine DRM. Både PlayReady och Widevine krypteras enligt den gemensamma krypteringsspecifikationen (ISO/IEC 23001 7 CENC).

Media Services tillhandahåller också en nyckel-/licens leverans tjänst från vilken klienter kan hämta AES-nycklar eller PlayReady/Widevine-licenser för att spela upp det krypterade innehållet.

Den här artikeln visar hur du använder Azure Portal för att konfigurera auktoriseringsprincipen för innehålls nycklar. Nyckeln kan senare användas för att dynamiskt Kryptera ditt innehåll. För närvarande kan du kryptera HLS, MPEG-streck och Smooth Streaming format. Du kan inte kryptera Progressive hämtningar.

När en spelare begär en data ström som är inställd på dynamisk kryptering, använder Media Services den konfigurerade nyckeln för att dynamiskt kryptera innehållet med hjälp av AES eller DRM-kryptering. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de Auktoriseringsprinciper som du har angett för nyckeln.

Om du planerar att ha flera innehålls nycklar eller vill ange en annan URL än den Media Services nyckel leverans tjänsten använder du Media Services .NET SDK eller REST-API: er. Mer information finns i:

* [Konfigurera en auktoriseringsprincip för innehålls nycklar med hjälp av Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)
* [Konfigurera en auktoriseringsprincip för innehålls nycklar med hjälp av Media Services REST API](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Vissa överväganden gäller
* När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill börja strömma ditt innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slut punkten för direkt uppspelning vara i läget "körs". 
* Till gången måste innehålla en uppsättning anpassad bit hastighet hastigheter eller anpassad bit hastighet Smooth Streaming filer. Mer information finns i [koda en till gång](media-services-encode-asset.md).
* Nyckel leverans tjänsten cachelagrar ContentKeyAuthorizationPolicy och dess relaterade objekt (princip alternativ och begränsningar) i 15 minuter. Du kan skapa en ContentKeyAuthorizationPolicy och ange om du vill använda en begränsning för token, testa den och sedan uppdatera principen till den öppna begränsningen. Den här processen tar ungefär 15 minuter innan principen växlar till den öppna versionen.
* En Media Services strömnings slut punkt anger värdet för CORS Access-Control-Allow-Origin-huvudet i preflight-svar som jokertecknet " \* ". Det här värdet fungerar bra med de flesta spelare, inklusive Azure Media Player, Roku och JWPlayer och andra. Men vissa spelare som använder dash.js fungerar inte eftersom, med läget för autentiseringsuppgifter inställt på "inkludera", XMLHttpRequest i deras dash.js tillåter inte jokertecknet " \* " som värde för Access-Control-Allow-Origin. Som en lösning på den här begränsningen i dash.js, om du är värd för din klient från en enda domän, kan Media Services ange domänen i svars huvudet för preflight. Om du behöver hjälp öppnar du ett support ärende via Azure Portal.

## <a name="configure-the-key-authorization-policy"></a>Konfigurera auktoriseringsprincipen för nyckel
Om du vill konfigurera auktoriseringsprincipen för nyckel väljer du sidan **INNEHÅLLS skydd** .

Media Services stöder flera olika sätt att autentisera användare som utför nyckel begär Anden. Auktoriseringsprincipen för innehålls nyckeln kan ha begränsningar för öppen, token eller IP-auktorisering. (IP kan konfigureras med REST eller .NET SDK.)

### <a name="open-restriction"></a>Öppna begränsning
Den öppna begränsningen innebär att systemet levererar nyckeln till alla som gör en nyckel förfrågan. Den här begränsningen kan vara användbar i test syfte.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Begränsning av token
Välj **token** -knappen om du vill välja en begränsad princip för token.

Den begränsade token-principen måste åtföljas av en token som utfärdats av en säkerhetstokentjänst (STS). Media Services stöder tokens i formaten simple web token ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) och JSON Web token (JWT). Mer information finns i [JWT-autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services tillhandahåller inte STS. Du kan skapa en anpassad STS för att utfärda tokens. STS måste konfigureras för att skapa en token som signerats med den angivna nyckeln och utfärda anspråk som du angav i konfigurationen för token-begränsning. Om token är giltig och anspråk i token matchar de som kon figurer ATS för innehålls nyckeln, returnerar tjänsten Media Services Key Delivery krypterings nyckeln till klienten.

När du konfigurerar den token-begränsade principen måste du ange primär verifierings nyckel, utfärdare och mål grupps parametrar. Den primära verifierings nyckeln innehåller den nyckel som token har signerats med. Utfärdaren är den STS som utfärdar token. Mål gruppen (kallas ibland omfång) beskriver syftet med den token eller resurs som token tillåter åtkomst till. Tjänsten Media Services Key Delivery verifierar att värdena i token matchar värdena i mallen.

### <a name="playready"></a>PlayReady
När du skyddar ditt innehåll med PlayReady är ett av de saker du behöver ange i auktoriseringsprincipen en XML-sträng som definierar licens mal len PlayReady. Som standard anges följande princip:

```xml
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <LicenseType>Nonpersistent</LicenseType>
      <PlayRight>
        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
      </PlayRight>
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

Du kan välja **XML-knappen Importera princip** och ange en annan XML-kod som överensstämmer med det XML-schema som definierats i [Översikt över Media Services PlayReady-licens mal len](media-services-playready-license-template-overview.md).

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png
