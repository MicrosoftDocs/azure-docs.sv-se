---
title: Konfigurera till gångs leverans principer med Media Services REST API | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar olika till gångs leverans principer med hjälp av Media Services REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 65a7b9479dae1826f478ca5e038ef5feb1caefb6
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017332"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurera till gångs leverans principer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Om du planerar att leverera dynamiskt krypterade till gångar, är ett av stegen i arbets flödet för Media Services innehålls leverans konfigurera leverans principer för till gångar. Policyn för till gångs leverans anger Media Services hur du vill att din till gång ska levereras: i vilket strömnings protokoll ska din till gång vara dynamiskt paketerad (till exempel MPEG-streck, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera till gången dynamiskt och hur (kuvert eller gemensam kryptering).

I det här avsnittet beskrivs varför och hur du skapar och konfigurerar till gångs leverans principer.

> [!NOTE]
> När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
>
> För att kunna använda dynamisk paketering och dynamisk kryptering måste till gången också innehålla en uppsättning anpassad bit hastighet hastigheter eller anpassad bit hastighet Smooth Streaming filer.

Du kan använda olika principer för samma till gång. Du kan till exempel använda PlayReady-kryptering för att Smooth Streaming och AES-kryptering till MPEG-streck och HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag till detta är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

Om du vill leverera en lagrings krypterad till gång måste du konfigurera till gångens leverans princip. Innan din till gång kan strömmas tar streaming-servern bort lagrings krypteringen och strömmar ditt innehåll med den angivna leverans principen. Om du till exempel vill leverera din till gång krypterad med Advanced Encryption Standard (AES) krypterings nyckel, anger du princip typen till **DynamicEnvelopeEncryption**. Om du vill ta bort lagrings kryptering och strömma till gången i Clear, anger du princip typen till **NoDynamicEncryption**. Exempel som visar hur du konfigurerar dessa princip typer följer.

Beroende på hur du konfigurerar till gångs leverans principen kan du dynamiskt paketera, dynamiskt kryptera och strömma följande strömnings protokoll: Smooth Streaming, HLS, MPEG-dataströmmar.

I följande lista visas de format som du använder för att strömma mjuk, HLS, streck.

Smooth Streaming:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

HLS

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Anvisningar för hur du publicerar en tillgång och skapar en strömnings-URL finns i [Skapa en strömnings-URL](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Överväganden
* Det går inte att ta bort en AssetDeliveryPolicy som är associerad med en till gång medan en OnDemand (streaming) Locator finns för denna till gång. Rekommendationen är att ta bort principen från till gången innan du tar bort principen.
* Det går inte att skapa en strömmande lokaliserare på en lagrings krypterad till gång när ingen till gångs leverans princip har angetts.  Om till gången inte är krypterad kommer systemet att låta dig skapa en lokaliserare och strömma till gången i klartext utan en till gångs leverans princip.
* Du kan ha flera till gångs leverans principer kopplade till en enda till gång, men du kan bara ange ett sätt att hantera en viss AssetDeliveryProtocol.  Innebär att du försöker länka två leverans principer som anger det AssetDeliveryProtocol. SmoothStreaming-protokoll som resulterar i ett fel, eftersom systemet inte vet vilket du vill att det ska gälla när en klient gör en Smooth Streaming begäran.
* Om du har en till gång med en befintlig strömmande lokaliserare kan du inte länka en ny princip till till gången, ta bort länken till en befintlig princip från till gången eller uppdatera en leverans princip som är kopplad till till gången.  Du måste först ta bort den strömmande lokaliseraren, justera principerna och sedan återskapa den strömmande lokaliseraren.  Du kan använda samma locatorId när du återskapar en strömmande lokaliserare, men du bör se till att inte orsakar problem för klienter eftersom innehållet kan cachelagras av ursprunget eller en underordnad CDN.

> [!NOTE]
> 
> När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API: et finns i [komma åt Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Rensa till gångs leverans princip
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Skapa till gångs leverans princip
Följande HTTP-begäran skapar en till gångs leverans princip som anger att dynamisk kryptering inte ska användas och för att leverera data strömmen i något av följande protokoll: MPEG-streck, HLS och Smooth Streaming protokoll. 

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [typer som används när du definierar AssetDeliveryPolicy](#types) .   

Begäran:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
Host: media.windows.net

{"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null}
```

Svar:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 363
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 06:21:27 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null,
"Created":"2015-02-08T06:21:27.6908329Z",
"LastModified":"2015-02-08T06:21:27.6908329Z"}
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Länka till gång med till gångs leverans princip
Följande HTTP-begäran länkar den angivna till gången till till gångs leverans principen till.

Begäran:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
Host: media.windows.net

{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}
```

Svar:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption till gångs leverans princip
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Skapa innehålls nyckel av typen EnvelopeEncryption och länka den till till gången
När du anger DynamicEnvelopeEncryption-leverans princip måste du se till att länka din till gång till en innehålls nyckel av typen EnvelopeEncryption. Mer information finns i: [skapa en innehålls nyckel](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Hämta leverans-URL
Hämta leverans-URL: en för den angivna leverans metoden för innehålls nyckeln som skapades i föregående steg. En klient använder den returnerade URL: en för att begära en AES-nyckel eller en PlayReady-licens för att kunna spela upp det skyddade innehållet.

Ange vilken typ av URL som ska hämtas i bröd texten i HTTP-begäran. Om du skyddar ditt innehåll med PlayReady begär du en Media Services PlayReady License URL för hämtning med 1 för keyDeliveryType: {"keyDeliveryType": 1}. Om du skyddar ditt innehåll med en kuvert kryptering begär du en nyckel hämtnings-URL genom att ange 2 för keyDeliveryType: {"keyDeliveryType": 2}.

Begäran:

```console
POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
Content-Type: application/json
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
Host: media.windows.net
Content-Length: 21

{"keyDeliveryType":2}
```

Svar:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 198
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
request-id: d26f65d2-fe65-4136-8fcf-31545be68377
x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 21:42:30 GMT

{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}
```


### <a name="create-asset-delivery-policy"></a>Skapa till gångs leverans princip
Följande HTTP-begäran skapar den **AssetDeliveryPolicy** som har kon figurer ATS för att tillämpa dynamisk kuvert kryptering (**DynamicEnvelopeEncryption**) på **HLS** -protokollet (i det här exemplet kommer andra protokoll att blockeras från strömningen). 

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [typer som används när du definierar AssetDeliveryPolicy](#types) .   

Begäran:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}
```


Svar:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 460
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 09 Feb 2015 05:24:38 GMT

{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}
```


### <a name="link-asset-with-asset-delivery-policy"></a>Länka till gång med till gångs leverans princip
Se [Länka till gång med till gångs leverans princip](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption till gångs leverans princip
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Skapa innehålls nyckel av typen CommonEncryption och länka den till till gången
När du anger DynamicCommonEncryption-leverans princip måste du se till att länka din till gång till en innehålls nyckel av typen CommonEncryption. Mer information finns i: [skapa en innehålls nyckel](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Hämta leverans-URL
Hämta leverans-URL: en för leverans metoden PlayReady för innehålls nyckeln som skapades i föregående steg. En klient använder den returnerade URL: en för att begära en PlayReady-licens för att kunna spela upp det skyddade innehållet. Mer information finns i [Hämta leverans-URL](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Skapa till gångs leverans princip
Följande HTTP-begäran skapar den **AssetDeliveryPolicy** som har kon figurer ATS för att tillämpa dynamisk common Encryption (**DynamicCommonEncryption**) på **Smooth Streaming** -protokollet (i det här exemplet kommer andra protokoll att blockeras från strömningen). 

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [typer som används när du definierar AssetDeliveryPolicy](#types) .   

Begäran:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}
```


Om du vill skydda ditt innehåll med Widevine DRM uppdaterar du AssetDeliveryConfiguration-värdena för att använda WidevineLicenseAcquisitionUrl (som har värdet 7) och anger URL: en för en licens leverans tjänst. Du kan använda följande AMS-partner för att få hjälp att leverera Widevine-licenser: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Exempel: 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> När du krypterar med Widevine kan du bara leverera med hjälp av tank streck. Se till att ange bindestreck (2) i till gångs leverans protokollet.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Länka till gång med till gångs leverans princip
Se [Länka till gång med till gångs leverans princip](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typer som används för att definiera AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Följande Enum beskriver värden som du kan ange för till gångs leverans protokollet.

```csharp
[Flags]
public enum AssetDeliveryProtocol
{
    /// <summary>
    /// No protocols.
    /// </summary>
    None = 0x0,

    /// <summary>
    /// Smooth streaming protocol.
    /// </summary>
    SmoothStreaming = 0x1,

    /// <summary>
    /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
    /// </summary>
    Dash = 0x2,

    /// <summary>
    /// Apple HTTP Live Streaming protocol.
    /// </summary>
    HLS = 0x4,

    ProgressiveDownload = 0x10, 

    /// <summary>
    /// Include all protocols.
    /// </summary>
    All = 0xFFFF
}
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Följande Enum beskriver värden som du kan ställa in för till gångs leverans princip typen.  

```csharp
public enum AssetDeliveryPolicyType
{
    /// <summary>
    /// Delivery Policy Type not set.  An invalid value.
    /// </summary>
    None,

    /// <summary>
    /// The Asset should not be delivered via this AssetDeliveryProtocol. 
    /// </summary>
    Blocked, 

    /// <summary>
    /// Do not apply dynamic encryption to the asset.
    /// </summary>
    /// 
    NoDynamicEncryption,  

    /// <summary>
    /// Apply Dynamic Envelope encryption.
    /// </summary>
    DynamicEnvelopeEncryption,

    /// <summary>
    /// Apply Dynamic Common encryption.
    /// </summary>
    DynamicCommonEncryption
    }
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

Följande Enum beskriver värden som du kan använda för att konfigurera leverans metoden för innehålls nyckeln till klienten.
    
```csharp
public enum ContentKeyDeliveryType
{
    /// <summary>
    /// None.
    ///
    </summary>
    None = 0,

    /// <summary>
    /// Use PlayReady License acquisition protocol
    ///
    </summary>
    PlayReadyLicense = 1,

    /// <summary>
    /// Use MPEG Baseline HTTP key protocol.
    ///
    </summary>
    BaselineHttp = 2,

    /// <summary>
    /// Use Widevine License acquisition protocol
    ///
    </summary>
    Widevine = 3

}
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Följande Enum beskriver värden som du kan ställa in för att konfigurera nycklar som används för att hämta en speciell konfiguration för en till gångs leverans princip.

```csharp
public enum AssetDeliveryPolicyConfigurationKey
{
    /// <summary>
    /// No policies.
    /// </summary>
    None,

    /// <summary>
    /// Exact Envelope key URL.
    /// </summary>
    EnvelopeKeyAcquisitionUrl,

    /// <summary>
    /// Base key url that will have KID=<Guid> appended for Envelope.
    /// </summary>
    EnvelopeBaseKeyAcquisitionUrl,

    /// <summary>
    /// The initialization vector to use for envelope encryption in Base64 format.
    /// </summary>
    EnvelopeEncryptionIVAsBase64,

    /// <summary>
    /// The PlayReady License Acquisition Url to use for common encryption.
    /// </summary>
    PlayReadyLicenseAcquisitionUrl,

    /// <summary>
    /// The PlayReady Custom Attributes to add to the PlayReady Content Header
    /// </summary>
    PlayReadyCustomAttributes,

    /// <summary>
    /// The initialization vector to use for envelope encryption.
    /// </summary>
    EnvelopeEncryptionIV,

    /// <summary>
    /// Widevine DRM acquisition url
    /// </summary>
    WidevineLicenseAcquisitionUrl
}
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

