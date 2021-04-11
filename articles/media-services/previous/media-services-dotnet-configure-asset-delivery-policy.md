---
title: Konfigurera till gångs leverans principer med .NET SDK | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar olika till gångs leverans principer med Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 82e152f257e867dfdbf97c92b041ce57c5a1bdbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014272"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurera till gångs leverans principer med .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Översikt
Om du planerar att leverera krypterade till gångar, är ett av stegen i arbets flödet för Media Services innehålls leverans att konfigurera leverans principer för till gångar. Policyn för till gångs leverans anger Media Services hur du vill att din till gång ska levereras: i vilket strömnings protokoll ska din till gång vara dynamiskt paketerad (till exempel MPEG-streck, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera till gången dynamiskt och hur (kuvert eller gemensam kryptering).

I den här artikeln beskrivs varför och hur du skapar och konfigurerar till gångs leverans principer.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
>
>För att kunna använda dynamisk paketering och dynamisk kryptering måste till gången också innehålla en uppsättning anpassad bit hastighet hastigheter eller anpassad bit hastighet Smooth Streaming filer.

Du kan använda olika principer för samma till gång. Du kan till exempel använda PlayReady-kryptering för att Smooth Streaming och AES-kryptering till MPEG-streck och HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

Om du vill leverera en lagrings krypterad till gång måste du konfigurera till gångens leverans princip. Innan din till gång kan strömmas tar streaming-servern bort lagrings krypteringen och strömmar ditt innehåll med den angivna leverans principen. Om du till exempel vill leverera din till gång krypterad med Advanced Encryption Standard (AES) krypterings nyckel, anger du princip typen till **DynamicEnvelopeEncryption**. Om du vill ta bort lagrings kryptering och strömma till gången i Clear, anger du princip typen till **NoDynamicEncryption**. Exempel som visar hur du konfigurerar dessa princip typer följer.

Beroende på hur du konfigurerar till gångs leverans principen kan du dynamiskt paketera, kryptera och strömma följande strömnings protokoll: Smooth Streaming, HLS och MPEG-streck.

I följande lista visas de format som du använder för att strömma mjuk, HLS och streck.

Smooth Streaming:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

HLS

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Överväganden
* Innan du tar bort AssetDeliveryPolicy bör du ta bort alla de strömmande lokaliseringarna som är kopplade till till gången. Du kan senare skapa nya omströmnings positionerare, om det behövs, med en ny AssetDeliveryPolicy.
* Det går inte att skapa en strömmande lokaliserare på en lagrings krypterad till gång när ingen till gångs leverans princip har angetts.  Om till gången inte är krypterad kommer systemet att låta dig skapa en lokaliserare och strömma till gången i klartext utan en till gångs leverans princip.
* Du kan ha flera till gångs leverans principer kopplade till en enda till gång, men du kan bara ange ett sätt att hantera en viss AssetDeliveryProtocol.  Innebär att du försöker länka två leverans principer som anger det AssetDeliveryProtocol. SmoothStreaming-protokoll som resulterar i ett fel, eftersom systemet inte vet vilket du vill att det ska gälla när en klient gör en Smooth Streaming begäran.
* Om du har en till gång med en befintlig strömmande lokaliserare kan du inte länka en ny princip till till gången (du kan antingen ta bort kopplingen till en befintlig princip från till gången eller uppdatera en leverans princip som är associerad med till gången).  Du måste först ta bort den strömmande lokaliseraren, justera principerna och sedan återskapa den strömmande lokaliseraren.  Du kan använda samma locatorId när du återskapar en strömmande lokaliserare, men du bör se till att inte orsakar problem för klienter eftersom innehållet kan cachelagras av ursprunget eller en underordnad CDN.

## <a name="clear-asset-delivery-policy"></a>Rensa till gångs leverans princip

Följande **ConfigureClearAssetDeliveryPolicy** -Metod anger att dynamisk kryptering inte ska användas och att leverera data strömmen i något av följande protokoll: MPEG-streck, HLS och Smooth Streaming protokoll. Du kanske vill tillämpa den här principen på dina lagrings krypterade till gångar.

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [typer som används när du definierar AssetDeliveryPolicy](#types) .

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption till gångs leverans princip

Följande **CreateAssetDeliveryPolicy** -Metod skapar den **AssetDeliveryPolicy** som har kon figurer ATS för att tillämpa dynamisk common Encryption (**DynamicCommonEncryption**) på ett smidigt strömnings protokoll (andra protokoll kommer att blockeras från strömning). Metoden har två parametrar: **till gång** (till gång till vilken du vill tillämpa leverans principen) och **IContentKey** (innehålls nyckeln för **CommonEncryption** -typen, mer information finns i: [skapa en innehålls nyckel](media-services-dotnet-create-contentkey.md#common_contentkey)).

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [typer som används när du definierar AssetDeliveryPolicy](#types) .

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Med Azure Media Services kan du också lägga till Widevine-kryptering. Följande exempel visar både PlayReady och Widevine som läggs till i till gångs leverans principen.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> När du krypterar med Widevine kan du bara leverera med hjälp av tank streck. Se till att ange bindestreck i till gångs leverans protokollet.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption till gångs leverans princip
Följande **CreateAssetDeliveryPolicy** -Metod skapar den **AssetDeliveryPolicy** som har kon figurer ATS för att tillämpa dynamisk kuvert kryptering (**DynamicEnvelopeEncryption**) för att Smooth Streaming, HLS och streck protokoll (om du väljer att inte ange vissa protokoll kommer de att blockeras från strömningen). Metoden har två parametrar: **till gång** (till gång till vilken du vill tillämpa leverans principen) och **IContentKey** (innehålls nyckeln för **EnvelopeEncryption** -typen, mer information finns i: [skapa en innehålls nyckel](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [typer som används när du definierar AssetDeliveryPolicy](#types) .   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typer som används för att definiera AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

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
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

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
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

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
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

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

