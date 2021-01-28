---
title: Media Services DRM-kryptering och licens leverans
description: Lär dig hur du använder DRM Dynamic Encryption och licens leverans tjänsten för att leverera strömmar som är krypterade med Microsoft PlayReady-, Google Widevine-eller Apple FairPlay-licenser.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: a4f5810f76af7116421bac3953b4eb65370055e3
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955197"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Självstudie: använda DRM dynamisk kryptering och licens leverans tjänst

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Även om den här självstudien använder [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) -exemplen är de allmänna stegen desamma för [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event?view=azure-cli-latest)eller andra [SDK](media-services-apis-overview.md#sdks): er som stöds.

Du kan använda Azure Media Services till att leverera strömmar som krypterats med Microsoft PlayReady, Google Widevine eller Apple FairPlay-licenser. För djupgående förklaringar, se [innehålls skydd med dynamisk kryptering](content-protection-overview.md).

Media Services tillhandahåller också en tjänst för att leverera PlayReady-, Widevine-och FairPlay DRM-licenser. När en användare begär DRM-skyddat innehåll begär Player-appen en licens från Media Services licens tjänsten. Om Player-appen är auktoriserad, utfärdar Media Services licens tjänsten en licens till spelaren. En licens innehåller en krypteringsnyckel som kan användas av klientspelaren för att dekryptera och direktuppspela innehållet.

Den här artikeln bygger på exemplet om [kryptering med DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

Exemplet som beskrivs i den här artikeln ger följande resultat:

![AMS med DRM-skyddad video i Azure Media Player](./media/protect-with-drm/ams_player.png)

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Skapa en transformation av kodning.
> * Ställ in den signeringsnyckel som användes för att verifiera din token.
> * Ange krav för innehålls nyckel principen.
> * Skapa en StreamingLocator med den angivna streaming-principen.
> * Skapa en URL som används för att spela upp filen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra vägledningen:

* Gå igenom artikeln med [översikten om innehållsskydd](content-protection-overview.md).
* Granska [innehålls skydds systemet design multi-DRM med åtkomst kontroll](design-multi-drm-system-with-access-control.md).
* Installera Visual Studio Code eller Visual Studio.
* Skapa ett nytt Azure Media Services-konto, som beskrivs i [den här snabbstarten](./create-account-howto.md).
* Hämta autentiseringsuppgifter som krävs för att använda API:er för Media Services med hjälp av [åtkomst till API:er](./access-api-howto.md)
* Ange lämpliga värden i appens konfigurations fil (appsettings.jspå).

## <a name="download-code"></a>Ladda ned kod

Klona en GitHub-lagringsplats som innehåller hela det .NET-exempel som diskuteras i den här artikeln till din dator med följande kommando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Du hittar exemplet om att kryptera med DRM i mappen [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Exemplet skapar unika resurser varje gång du kör appen. Normalt återanvänder du befintliga resurser som transformeringar och principer (om den befintliga resursen har obligatoriska konfigurationer).

## <a name="start-using-media-services-apis-with-net-sdk"></a>Börja använda API:er för Media Services med .NET SDK

Du börjar använda Media Services API: er med .NET genom att skapa ett **AzureMediaServicesClient** -objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång  

Utmatnings [till gången](assets-concept.md) lagrar resultatet av ditt kodnings jobb.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Hämta eller skapa en kodtransformering

När du skapar en ny instans för en [Transformering](transforms-jobs-concept.md), måste du ange vilken utdata du vill att den ska skapa. Den nödvändiga parametern är ett `transformOutput` objekt, som du ser i koden nedan. Varje TransformOutput innehåller en **Förinställning**. I Förinställning finns stegvisa anvisningar för den video- och/eller ljudbearbetning som ska användas för att generera önskad TransformOutput. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. För inställningen kodar indata till en steg-för-bit-uppräkning (bit hastighets upplösnings par) baserat på indata för upplösning och bit hastighet och genererar ISO MP4-filer med H. 264-video och AAC-ljud som motsvarar varje bit hastighets upplösnings par. 

Innan du skapar en ny **transformering** bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan.  I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Skicka jobb

Som nämns ovan är objektet **Transformering** receptet och ett [Jobb](transforms-jobs-concept.md) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobbet** anger information som platsen för indata-videon och platsen för utdata.

I den här självstudien skapar vi jobbets indata baserat på en fil som matas in direkt från en [https-källans URL](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Det tar lite tid att slutföra jobbet. När du gör det, vill du bli meddelad. Kod exemplet nedan visar hur du avsöker tjänsten för **jobbets** status. Avsökningen är inte en rekommenderad metod för produktion av appar på grund av potentiell latens. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid. Se [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobb** har vanligtvis följande tillstånd: **Schemalagd**, **I kö**, **Bearbetas**, **Slutförd** (slutlig status). Om jobbet har kommit över ett fel visas **fel** tillstånd. Om jobbet håller på att avbrytas **avbryts du och** **annulleras** när det är färdigt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Skapa en princip för innehålls nyckel

En innehållsnyckel ger säker åtkomst till dina tillgångar. Du måste skapa en [princip för innehålls nyckel](content-key-policy-concept.md) när du krypterar ditt innehåll med ett DRM. Principen konfigurerar hur innehålls nyckeln levereras till slut klienter. Innehålls nyckeln är associerad med en strömmande positionerare. Media Services tillhandahåller också nyckelleveranstjänsten som levererar krypteringsnycklar och licenser till behöriga användare.

Du måste ange kraven (begränsningar) för **innehålls nyckel principen** som måste uppfyllas för att leverera nycklar med den angivna konfigurationen. I det här exemplet anger vi följande konfigurationer och krav:

* Konfiguration

    Licenserna för [PlayReady](playready-license-template-overview.md) och [Widevine](widevine-license-template-overview.md) är konfigurerade så att de kan levereras av Media Services-licensleveranstjänsten. Även om den här exempel appen inte konfigurerar [Fairplay](fairplay-license-overview.md) -licensen, innehåller den en metod som du kan använda för att konfigurera Fairplay. Du kan lägga till FairPlay-konfiguration som ett annat alternativ.

* Begränsning

    Appen anger en begränsning för token för en JSON Web Token (JWT) för principen.

När en ström begärs av en spelare krypterar Media Services innehållet med den angivna nyckeln. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren är behörig för nyckeln utvärderas den innehållsnyckelprincip som du angav för nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

När kodningen är klar och innehållsnyckelprincipen är inställd är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du gör videon tillgänglig i två steg:

1. Skapa en [strömmande positionerare](streaming-locators-concept.md).
2. Skapa webbadresserna för direktuppspelning som klienter kan använda.

Processen för att skapa **streaming Locator** kallas publicering. Som standard är **streaming Locator** giltig omedelbart efter att du har gjort API-anropen. Det varar tills det tas bort, om du inte konfigurerar de valfria start-och slut tiderna.

När du skapar en **strömmande positionerare** måste du ange önskad `StreamingPolicyName` . I den här självstudien använder vi en av de fördefinierade strömmande principerna, som visar Azure Media Services hur du publicerar innehållet för strömning. I det här exemplet ställer vi in StreamingLocator.StreamingPolicyName för principen ”Predefined_MultiDrmCencStreaming”. PlayReady-och Widevine-krypteringarna används och nyckeln levereras till uppspelnings klienten baserat på de konfigurerade DRM-licenserna. Om du dessutom vill kryptera strömmen med CBCS (FairPlay) använder du ”Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> Om du använder en anpassad [strömningsprincip](streaming-policy-concept.md) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Hämta en testtoken

I den här självstudien anger vi att innehållsnyckelprincipen ska ha en tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder tokens i [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) -format och det är det som vi konfigurerar i exemplet.

ContentKeyIdentifierClaim används i ContentKeyPolicy, vilket innebär att den token som presenteras för nyckelleveranstjänst måste innehålla identifieraren för ContentKey. I exemplet anger vi inte en innehålls nyckel när du skapar en strömmande lokaliserare. systemet skapar en slumpmässig en för oss. För att generera testtoken måste vi få ContentKeyId att placera i ContentKeyIdentifierClaim-anspråket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Bygg en strömmande URL

Nu när [StreamingLocator](/rest/api/media/streaminglocators) har skapats kan du hämta direktuppspelningswebbadresserna. Om du vill bygga en URL måste du sammanfoga [StreamingEndpoint](/rest/api/media/streamingendpoints) -värdnamnet och sökvägen för **strömmande lokalisering** . I det här exemplet används *standardvärdet* **Slutpunkt för direktuppspelning**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde för* **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

När du kör appen visas följande skärm:

![Skydda med DRM](./media/protect-with-drm/playready_encrypted_url.png)

Du kan öppna en webbläsare och klistra in den webbadress som bildas för att starta Azure Media Player-demosidan där webbadressen en och token redan är ifyllda.

## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

I allmänhet bör du rensa allt utom de objekt som du planerar att återanvända (normalt återanvänder du transformationer, StreamingLocators och så vidare). Om du vill att ditt konto ska rensas efter experimentering tar du bort de resurser som du inte planerar att återanvända. Följande kod tar till exempel bort jobbet, skapade till gångar och innehålls nyckel princip:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

Checka ut

> [!div class="nextstepaction"]
> [Skydda med AES-128](protect-with-aes128.md)
