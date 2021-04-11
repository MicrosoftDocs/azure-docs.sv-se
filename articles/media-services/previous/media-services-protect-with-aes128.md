---
title: Använd AES-128 dynamisk kryptering och Key Delivery Service | Microsoft Docs
description: Det här avsnittet visar hur du krypterar dynamiskt med AES-128 och använder Key Delivery Service.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b4375e7b68f0b279a971e92775ca454f06dcbd45
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067341"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Använda dynamisk AES-128-kryptering och tjänsten för nyckelleverans

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-playready-license-template-overview.md)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Du kan använda Media Services för att leverera HTTP Live Streaming (HLS) och Smooth Streaming krypterat med AES genom att använda 128-bitars krypterings nycklar. Media Services tillhandahåller också den nyckel leverans tjänst som levererar krypterings nycklar till behöriga användare. Om du vill Media Services kryptera en till gång, associerar du en krypterings nyckel med till gången och konfigurerar sedan Auktoriseringsprinciper för nyckeln. När en data ström begärs av en spelare, använder Media Services den angivna nyckeln för att dynamiskt Kryptera ditt innehåll med hjälp av AES-kryptering. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de Auktoriseringsprinciper som du har angett för nyckeln.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: antingen öppen eller tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten [simple web token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) och [JSON Web Token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT). Mer information finns i [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy).

För att dra fördel av dynamisk kryptering behöver du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Du måste också konfigurera leverans principen för till gången (beskrivs längre fram i den här artikeln). Sedan, baserat på det format som anges i strömnings-URL:en, kommer servern för strömning på begäran att säkerställa att dataströmmen levereras i det protokoll som du har valt. Därför behöver du bara lagra och betala för filerna i ett enda lagrings format. Media Services skapar och hanterar lämpligt svar baserat på begäran från en klient.

Den här artikeln är användbar för utvecklare som arbetar med program som levererar skyddade medier. Artikeln visar hur du konfigurerar Key Delivery Service med auktoriseringsprinciper så att endast auktoriserade klienter kan ta emot krypterings nycklar. Det visar också hur du använder dynamisk kryptering.

Information om hur du krypterar innehåll med Advanced Encryption Standard (AES) för leverans till Safari på macOS finns i [det här blogg inlägget](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
En översikt över hur du skyddar medie innehåll med AES-kryptering finns i [den här videon](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Arbets flöde för AES-128 Dynamic Encryption och Key Delivery Service

Utför följande allmänna steg när du krypterar dina till gångar med AES med hjälp av Media Services Key Delivery service och också med dynamisk kryptering:

1. [Skapa en till gång och ladda upp filer till till gången](media-services-protect-with-aes128.md#create_asset).

2. [Koda den till gång som innehåller filen till MP4-uppsättningen med anpassad bit hastighet](media-services-protect-with-aes128.md#encode_asset).

3. [Skapa en innehålls nyckel och associera den med den kodade till gången] Media-Services-Protect-with-AES128. MD # create_contentkey). I Media Services innehåller innehållsnyckeln tillgångens krypteringsnyckel.

4. [Konfigurera en auktoriseringsprincip för innehålls nyckeln](media-services-protect-with-aes128.md#configure_key_auth_policy). Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten måste uppfylla principen innan innehållsnyckeln kan levereras till klienten.

5. [Konfigurera leverans principen för en till gång](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfigurationen för leverans principen omfattar URL för nyckel hämtning och en initierings vektor (IV). (AES-128 kräver samma IV för kryptering och dekryptering.) Konfigurationen inkluderar även leverans protokollet (till exempel MPEG-streck, HLS, Smooth Streaming eller alla) och typen av dynamisk kryptering (till exempel kuvert eller ingen dynamisk kryptering).

    Du kan använda olika principer för varje protokoll för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering för Smooth/DASH och AES envelope för HLS. Alla protokoll som inte har definierats i en leverans princip blockeras från strömning. (Ett exempel är om du lägger till en enskild princip som endast anger HLS som protokoll.) Undantaget är om du inte har definierat någon till gångs leverans princip alls. Därefter tillåts alla protokoll fritt.

6. [Skapa en OnDemand-lokaliserare](media-services-protect-with-aes128.md#create_locator) för att få en strömnings-URL.

Artikeln visar också [hur ett klient program kan begära en nyckel från Key Delivery Service](media-services-protect-with-aes128.md#client_request).

Du kan hitta ett fullständigt [.net-exempel](media-services-protect-with-aes128.md#example) i slutet av artikeln.

Följande bild visar arbetsflödet som beskrevs tidigare. Här används aktuellt token för autentisering.

![Skydda med AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Resten av den här artikeln innehåller förklaringar, kod exempel och länkar till ämnen som visar hur du uppnår de uppgifter som beskrivs ovan.

## <a name="current-limitations"></a>Aktuella begränsningar
Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort eventuella befintliga lokaliserare och skapa en ny.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Skapa en till gång och överför filer till till gången
För att hantera, koda och strömma videor måste du först överföra innehållet till Media Services. När du har överfört innehållet lagras det på ett säkert sätt i molnet för vidare bearbetning och strömning. 

Utförlig information finns i [Överföra filer till ett Media Services-konto](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet
Med dynamisk kryptering skapar du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Sedan, baserat på det angivna formatet i manifest-eller fragment förfrågningen, ser servern för strömning på begäran att du får data strömmen i det protokoll som du har valt. Sedan behöver du bara lagra och betala för filerna i ett enda lagrings format. Media Services skapar och hanterar lämpligt svar baserat på begäran från en klient. Mer information finns i [Översikt över dynamisk paketering](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet ”Körs”. 
>
>För att använda dynamisk paketering och dynamisk kryptering måste till gången också innehålla en uppsättning anpassad bit hastighet hastigheter eller anpassad bit hastighet Smooth Streaming filer.

Mer information om att koda finns i [Koda en tillgång med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Skapa en innehållsnyckel och associera den med den kodade tillgången
I Media Services innehåller innehållsnyckeln den nyckel som du vill kryptera en tillgång med.

Mer information finns i [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md).

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Konfigurera en auktoriseringsprincip för innehålls nyckeln
Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten (spelaren) måste uppfylla principen innan nyckeln kan levereras till klienten. Auktoriseringsprincipen för innehålls nyckeln kan ha en eller flera auktoriseringsregler, antingen öppna, token-restriktion eller IP-begränsning.

Mer information finns i [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-dotnet-configure-content-key-auth-policy.md).

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Konfigurera en tillgångsleveransprincip
Konfigurera leveransprincipen för din tillgång. Tillgångsleveransprincipen innehåller bland annat följande:

* URL för nyckel hämtning. 
* Initierings vektorn (IV) som ska användas för kuvert kryptering. AES-128 kräver samma IV för kryptering och dekryptering. 
* Protokollet för tillgångsleverans (t.ex. MPEG DASH, HLS, jämn direktuppspelning eller alla).
* Typen av dynamisk kryptering (till exempel AES-kuvert) eller ingen dynamisk kryptering. 

Mer information finns i [Konfigurera tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md).

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Skapa en lokaliserare för OnDemand-strömning för att få en strömnings-URL
Du måste förse din användare med strömnings-URL:en för Smooth Streaming, DASH eller HLS.

> [!NOTE]
> Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort eventuella befintliga lokaliserare och skapa en ny.
> 
> 

Anvisningar för hur du publicerar en tillgång och skapar en strömnings-URL finns i [Skapa en strömnings-URL](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Hämta en testtoken
Hämta en testtoken baserat på de tokenbegränsningar som användes för nyckelauktoriseringsprincipen.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Du kan använda [Azure Media Services Player](https://aka.ms/azuremediaplayer) för att testa din dataström.

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Hur kan din klient begära en nyckel från Key Delivery Service?
I föregående steg konstruerade du den URL som pekar på en manifest fil. Klienten måste extrahera nödvändig information från de strömmande manifest filerna för att göra en begäran till nyckel leverans tjänsten.

### <a name="manifest-files"></a>MANIFEST filer
Klienten måste extrahera URL: en (som också innehåller värde för innehålls nyckel-ID [barn]) från manifest filen. Klienten försöker sedan hämta krypterings nyckeln från Key Delivery Service. Klienten måste också extrahera IV-värdet och använda det för att dekryptera data strömmen. Följande fragment visar `<Protection>` elementet i Smooth Streaming manifestet:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

Vid HLS är rot manifestet indelat i segmentera filer. 

Rot manifestet är till exempel: http: \/ /test001.ORIGIN.MediaServices.Windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ISM/manifest (format = M3U8-AAPL). Den innehåller en lista över segment fil namn.

```text
. . . 
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
…
```

Om du öppnar en av segment filerna i en text redigerare (till exempel http: \/ /test001.ORIGIN.MediaServices.Windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ISM/QualityLevels (514369)/manifest (video, format = M3U8-AAPL), innehåller den #EXT-X-Key, som anger att filen är krypterad.

```text
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:9
#EXT-X-KEY:METHOD=AES-128,
URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
        kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
        IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:8.425708,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXT-X-ENDLIST
```

>[!NOTE] 
>Om du planerar att spela upp en AES-krypterad HLS i Safari kan du läsa [den här bloggen](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Begär nyckeln från Key Delivery Service

Följande kod visar hur du skickar en begäran till tjänsten Media Services Key Delivery genom att använda en nyckel leverans-URI (som har extraherats från manifestet) och en token. (Den här artikeln förklarar inte hur du får SWTs från en STS.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Skydda ditt innehåll med AES-128 med hjälp av .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

1. Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md).

2. Lägg till följande element i appSettings, som definieras i app.config-filen:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Exempel

Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.
 
>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika Media Servicess principer (till exempel för lokaliserings principer eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomst behörigheter. Ett exempel är principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i avsnittet begränsa åtkomst principer i [Hantera till gångar och relaterade entiteter med Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Se till att uppdatera variablerna så att de pekar på mappar där dina indatafiler finns.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
