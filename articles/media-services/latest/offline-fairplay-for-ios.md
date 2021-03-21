---
title: Media Services v3 offline-FairPlay strömning för iOS
description: Det här avsnittet innehåller en översikt och visar hur du använder Azure Media Services v3 för att dynamiskt Kryptera ditt HTTP Live Streaming (HLS)-innehåll med Apple FairPlay i offline-läge.
services: media-services
keywords: HLS, DRM, FairPlay streaming (FPS), offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 399f9b0184217db8180fcec38e4f78917d5c9bc8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955249"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline-FairPlay strömning för iOS med Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 Azure Media Services tillhandahåller en uppsättning välkända [innehålls skydds tjänster](https://azure.microsoft.com/services/media-services/content-protection/) som omfattar:

- Microsoft PlayReady
- Google-Widevine
    
    Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.
- Apple-FairPlay
- AES-128-kryptering

Digital Rights Management (DRM)/Advanced Encryption Standard (AES) kryptering av innehåll utförs dynamiskt vid begäran om olika strömnings protokoll. DRM-Licens/AES-dekryptering av nyckel leverans tjänster tillhandahålls också av Media Services.

Förutom att skydda innehåll för online-direktuppspelning över olika strömnings protokoll, är offline-läge för skyddat innehåll också en ofta efterfrågad funktion. Stöd för offline-läge krävs i följande scenarier:

* Uppspelning när Internet anslutning inte är tillgängligt, till exempel under resan.
* Vissa innehålls leverantörer kan förhindra att DRM-licenser levereras bortom ett land/regions gräns. Om användarna vill titta på innehåll medan de reser utanför landet/regionen krävs offline-nedladdning.
* I vissa länder/regioner är Internet tillgänglighet och/eller bandbredd fortfarande begränsad. Användarna kan välja att ladda ned för att kunna se innehåll i en upplösning som är tillräckligt hög för en tillfredsställande visnings upplevelse. I det här fallet är problemet vanligt vis inte nätverks tillgänglighet men begränsad nätverks bandbredd. OVP-providers (OTT)/online-video plattform () stöder offline-läge.

Den här artikeln beskriver stöd för FairPlay streaming (FPS) offline-läge som är riktad mot enheter som kör iOS 10 eller senare. Den här funktionen stöds inte för andra Apple-plattformar, till exempel Watch, tvOS eller Safari på macOS.

> [!NOTE]
> Offline-DRM debiteras bara för att skapa en enskild begäran om en licens när du laddar ned innehållet. Eventuella fel faktureras inte.

## <a name="prerequisites"></a>Förutsättningar

Innan du implementerar offline DRM för FairPlay på en iOS 10 +-enhet:

* Granska innehålls skydd online för FairPlay: 

    - [Licenskrav för och konfiguration av Apple FairPlay](fairplay-license-overview.md)
    - [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
    - Ett .NET-exempel som innehåller konfiguration av strömmande online-FPS: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Hämta SDK: n för FPS från Apple Developer Network. SDK: n för FPS innehåller två komponenter:

    - Server-SDK för FPS, som innehåller nyckel säkerhetsmodulen (KSM), klient exempel, en specifikation och en uppsättning test vektorer.
    - Distributions paketet för FPS, som innehåller D-funktions specifikationen, tillsammans med anvisningar om hur du genererar ett FPS-certifikat, kundspecifik privat nyckel och en hemlig program nyckel. Apple utfärdar distributions paketet för FPS endast till licensierade innehålls leverantörer.
* Klona https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git . 

    Du måste ändra koden i [kryptera med DRM med .net](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) för att lägga till Fairplay-konfigurationer.  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurera innehålls skydd i Azure Media Services

Gör följande i [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) -metoden:

Ta bort kommentaren till den kod som konfigurerar FairPlay-princip alternativet:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Du kan också ta bort kommentaren till koden som lägger till CBCS ContentKeyPolicyOption i listan över ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Aktivera offline-läge

Om du vill aktivera offline-läge skapar du en anpassad StreamingPolicy och använder dess namn när du skapar en StreamingLocator i [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

Nu är ditt Media Services-konto konfigurerat för att leverera FairPlay-licenser offline.

## <a name="sample-ios-player"></a>Exempel på iOS-spelare

Stöd för offline-läge i FPS är bara tillgängligt på iOS 10 och senare. Server-SDK: n för FPS (version 3,0 eller senare) innehåller dokumentet och exemplet för offline-läge för FPS. Mer specifikt innehåller Server-SDK för FPS (version 3,0 eller senare) följande två objekt som är relaterade till offline-läge:

* Dokument: "offline-uppspelning med FairPlay streaming och HTTP Live Streaming". Apple, 14 september 2016. I FPS Server SDK version 4,0 är det här dokumentet sammanslaget i huvud-FPS-dokumentet.
* Exempel kod: HLSCatalog-exempel (en del av Apples Server-SDK för FPS) för offline-läge för FPS i \FairPlay Streaming Server SDK version 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. I HLSCatalog-exempel appen används följande kod för att implementera offline-läges funktioner:

    - AssetPersistenceManager. SWIFT-kod fil: AssetPersistenceManager är huvud klassen i det här exemplet som visar hur du:

        - Hantera nedladdning av HLS-strömmar, till exempel de API: er som används för att starta och avbryta hämtningar och ta bort befintliga till gångar från enheter.
        - Övervaka hämtnings förloppet.
    - AssetListTableViewController. SWIFT och AssetListTableViewCell. SWIFT-Koda filer: AssetListTableViewController är huvud gränssnittet i det här exemplet. Den innehåller en lista över till gångar som exemplet kan använda för att spela upp, Hämta, ta bort eller avbryta en nedladdning. 

De här stegen visar hur du konfigurerar en iOS-spelare som körs. Förutsatt att du börjar med HLSCatalog-exemplet i FPS Server SDK-version 4.0.1 gör du följande kod ändringar:

Implementera metoden med `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` hjälp av följande kod i HLSCatalog\Shared\Managers\ContentKeyDelegate.Swift. Låt "drmUr" vara en variabel som tilldelats HLS-URL: en.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Implementera metoden i HLSCatalog\Shared\Managers\ContentKeyDelegate.swift `requestApplicationCertificate()` . Den här implementeringen beror på om du bäddar in certifikatet (endast offentlig nyckel) med enheten eller är värd för certifikatet på webben. Följande implementering använder det värdbaserade program certifikatet som används i test exemplen. Låt "certUrl" vara en variabel som innehåller URL: en för applikations certifikatet.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

För det slutliga integrerade testet finns både video-URL: en och URL: en för program certifikatet i avsnittet "integrerat test".

Lägg till din test video-URL i HLSCatalog\Shared\Resources\Streams.plist. För innehålls nyckel-ID: t använder du URL: en för FairPlay licens hämtning med SKD-protokollet som unikt värde.

![Offline-FairPlay iOS app-strömmar](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Använd din egen test video-URL, FairPlay License Acquisition URL och URL för program certifikat om du har konfigurerat dem. Eller också kan du fortsätta till nästa avsnitt, som innehåller test exempel.

## <a name="integrated-test"></a>Integrerat test

Tre test exempel i Media Services som tar upp följande tre scenarier:

* FPS-skyddad, med video, ljud och alternativt ljud spår
* FPS-skyddad, med video och ljud, men utan alternativ ljud spår
* FPS skyddas, med endast video och inget ljud

Du hittar dessa exempel på [den här demo webbplatsen](https://aka.ms/poc#22), med motsvarande program certifikat som finns i en Azure-webbapp.
Med version 3 eller version 4-exemplet på Server-SDK: n för FPS, om en Master-spelnings lista innehåller alternativ ljud, i offlineläge spelar den bara ljud. Därför måste du randig det alternativa ljudet. De andra och tredje exemplen som listas tidigare fungerar med andra ord i onlineläge och offline-läge. Exemplet som visas först spelar bara ljud i offlineläge, medan direkt uppspelningen fungerar som den ska.

## <a name="faq"></a>Vanliga frågor

I [vanliga frågor och svar får du hjälp med fel sökning](frequently-asked-questions.md#why-does-only-audio-play-but-not-video-during-offline-mode).

## <a name="next-steps"></a>Nästa steg

Se hur du [skyddar med AES-128](protect-with-aes128.md)
