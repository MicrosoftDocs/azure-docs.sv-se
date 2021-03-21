---
title: Skydda ditt innehåll med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en översikt över innehålls skydd med Azure Media Services v2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: edffa2dddd0ec877a4b825a69a76fb158928c89f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016771"
---
# <a name="content-protection-overview"></a>Översikt över innehållsskydd

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Du kan använda Azure Media Services för att skydda dina medier från den tid det lämnar datorn genom lagring, bearbetning och leverans. Med Media Services kan du leverera direktsänd och innehåll på begäran som krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre större Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. 

Följande bild illustrerar arbets flödet för Media Services innehålls skydd: 

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Den här artikeln förklarar begrepp och terminologi som är relevanta för att förstå innehålls skydd med Media Services. Artikeln innehåller också länkar till artiklar som diskuterar hur du skyddar innehåll. 

## <a name="dynamic-encryption"></a>Dynamisk kryptering

Du kan använda Media Services för att leverera innehåll som krypterats dynamiskt med AES-rensning eller DRM-kryptering med hjälp av PlayReady, Widevine eller FairPlay. Om innehållet krypteras med en AES-klar nyckel och skickas via HTTPS, är det inte i klartext förrän klienten når klienten. 

Varje krypterings metod stöder följande strömnings protokoll:
 
- AES: MPEG-streck, Smooth Streaming och HLS
- PlayReady: MPEG-streck, Smooth Streaming och HLS
- Widevine: MPEG-streck
- FairPlay: HLS

Kryptering av Progressive nedladdning stöds inte. 

Om du vill kryptera en till gång måste du associera en krypterings innehålls nyckel med din till gång och även konfigurera en auktoriseringsprincip för nyckeln. Innehålls nycklar kan anges eller genereras automatiskt av Media Services.

Du måste också konfigurera till gångens leverans princip. Om du vill strömma en lagrings krypterad till gång ska du se till att ange hur du vill leverera den genom att konfigurera till gångs leverans principen.

När en data ström begärs av en spelare, använder Media Services den angivna nyckeln för att dynamiskt Kryptera ditt innehåll med hjälp av AES Clear Key eller DRM-kryptering. För att dekryptera data strömmen begär spelaren nyckeln från Media Services Key Delivery Service. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de Auktoriseringsprinciper som du har angett för nyckeln.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128-rensad nyckel eller DRM
Kunderna undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den främsta skillnaden mellan de två systemen är att med AES-kryptering överförs innehålls nyckeln till klienten i ett okrypterat format ("i klartext"). Det innebär att den nyckel som används för att kryptera innehållet kan visas i ett nätverks spår på klienten som oformaterad text. AES-128-kryptering är lämplig för användnings fall där visnings programmet är en betrodd part (till exempel kryptering av företags videor som distribueras i ett företag som kan ses av anställda).

PlayReady, Widevine och FairPlay alla ger en högre krypterings nivå jämfört med AES-128-kryptering med tydliga nycklar. Innehålls nyckeln överförs i ett krypterat format. Dekrypteringen hanteras dessutom i en säker miljö på nivån operativ system, där det är svårare för en angripare att attackera angrepp. DRM rekommenderas för användnings fall där visnings programmet kanske inte är en betrodd part och du behöver den högsta säkerhets nivån.

## <a name="storage-encryption"></a>Lagringskryptering
Du kan använda lagrings kryptering för att kryptera ditt rensade innehåll lokalt med hjälp av AES 256-bitars kryptering. Du kan sedan överföra den till Azure Storage, där den är lagrad som krypterad i vila. Till gångar som skyddas med lagrings kryptering krypteras automatiskt och placeras i ett krypterat fil system före kodning. Till gångarna krypteras om innan du laddar upp igen som en ny utgående till gång. Det primära användnings fallet för lagrings kryptering är när du vill skydda dina mediefiler med hög kvalitet med stark kryptering i vila på disk.

För att leverera en lagrings krypterad till gång måste du konfigurera till gångens leverans princip så att Media Services vet hur du vill leverera ditt innehåll. Innan din till gång kan strömmas avkrypterar och strömmar ditt innehåll med hjälp av den angivna leverans principen (till exempel AES, gemensam kryptering eller ingen kryptering).

## <a name="types-of-encryption"></a>Krypterings typer
PlayReady och Widevine använder gemensam kryptering (AES-läge). FairPlay använder sig av AES-CBC kryptering. Med AES-128 rensad nyckel kryptering används kuvert kryptering.

## <a name="licenses-and-keys-delivery-service"></a>Licenser och nycklar leverans tjänst
Media Services tillhandahåller en nyckel leverans tjänst för att leverera DRM-licenser (PlayReady, Widevine, FairPlay) och AES-nycklar till auktoriserade klienter. Du kan använda [Azure Portal](media-services-portal-protect-content.md), REST API eller Media Services SDK för .net för att konfigurera principer för auktorisering och autentisering för dina licenser och nycklar.

## <a name="control-content-access"></a>Kontrol lera åtkomst till innehåll
Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera en auktoriseringsprincip för innehålls nyckeln. Auktoriseringsprincipen för innehålls nycklar stöder antingen öppen eller token-begränsning.

### <a name="open-authorization"></a>Öppen auktorisering
Med en öppen auktoriseringsprincip skickas innehålls nyckeln till alla klienter (ingen begränsning).

### <a name="token-authorization"></a>Token-auktorisering
Med en princip för begränsad auktorisering skickas innehålls nyckeln endast till en klient som presenterar en giltig JSON Web Token (JWT) eller enkel webbtoken (SWT) i begäran om nyckel/licens. Denna token måste utfärdas av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med den angivna nyckeln och utfärda anspråk som du angav i konfigurationen för token-begränsning. Media Services Key Delivery Service returnerar begärd nyckel/licens till klienten om token är giltig och anspråk i token matchar de som kon figurer ATS för nyckeln/licensen.

När du konfigurerar den begränsade token-principen måste du ange primär verifierings nyckel, utfärdare och mål grupps parametrar. Den primära verifierings nyckeln innehåller den nyckel som token har signerats med. Utfärdaren är den Secure token-tjänst som utfärdar token. Mål gruppen, som ibland kallas omfång, beskriver syftet med token eller resursen som token tillåter åtkomst till. Tjänsten Media Services Key Delivery verifierar att värdena i token matchar värdena i mallen.

### <a name="token-replay-prevention"></a>Förhindra repetition av token

Funktionen för att *förhindra repetition av token* tillåter Media Services kunder att ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Kunden kan lägga till ett anspråk av typen `urn:microsoft:azure:mediaservices:maxuses` i token, där värdet är antalet gånger som token kan användas för att hämta en licens eller nyckel. Alla efterföljande förfrågningar med samma token till nyckel leverans returnerar ett obehörigt svar. Se hur du lägger till anspråk i [DRM-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Överväganden

* Kunder måste ha kontroll över genereringen av token. Anspråket måste placeras i själva token.
* När du använder den här funktionen avvisas begär Anden med token vars utgångs tid är mer än en timme från den tidpunkt då begäran tas emot avvisas med ett obehörigt svar.
* Tokens identifieras unikt av signaturen. Alla ändringar av nytto lasten (till exempel uppdatering till förfallo tiden eller anspråket) ändrar signaturen för token och den räknas som en ny token som nyckel leveransen inte tar emot över innan.
* Uppspelningen Miss lyckas om token har överskridit `maxuses` värdet som anges av kunden.
* Den här funktionen kan användas för allt befintligt skyddat innehåll (bara token som utfärdats måste ändras).
* Den här funktionen fungerar med både JWT och SWT.

## <a name="streaming-urls"></a>Strömmande URL: er
Om din till gång har krypterats med fler än ett DRM, använder du en krypterings tagg i direkt uppspelnings-URL: (format = ' 3u8-AAPL ', Encryption = ' xxx ').

Följande gäller:

* Det går inte att ange fler än en krypterings typ.
* Krypterings typen behöver inte anges i URL: en om bara en kryptering tillämpades på till gången.
* Krypterings typen är Skift läges okänslig.
* Följande krypterings typer kan anges:

  * **Cenc**: för PlayReady eller Widevine (common Encryption)
  * **CBCS – AAPL**: för FAIRPLAY (AES CBC-kryptering)
  * **CBC**: för kryptering med AES-kuvert

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg
I följande artiklar beskrivs nästa steg som hjälper dig att komma igång med innehålls skydd:

* [Skydda med lagrings kryptering](media-services-rest-storage-encryption.md)
* [Skydda med AES-kryptering](media-services-protect-with-aes128.md)
* [Skydda med PlayReady och/eller Widevine](media-services-protect-with-playready-widevine.md)
* [Skydda med FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Relaterade länkar

* [JWT-token-autentisering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Integrera Azure Media Services OWIN MVC-baserade appar med Azure Active Directory och begränsa leverans av innehålls nycklar baserat på JWT-anspråk](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
