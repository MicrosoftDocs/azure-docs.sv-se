---
title: Skydda HLS-innehåll med offline Apple-FairPlay – Azure | Microsoft Docs
description: Det här avsnittet ger en översikt och visar hur du använder Azure Media Services för att dynamiskt Kryptera ditt HTTP Live Streaming (HLS)-innehåll med Apple FairPlay i offline-läge.
services: media-services
keywords: HLS, DRM, FairPlay streaming (FPS), offline, iOS 10
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: dwgeo
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d05810da6abfd33ff6605fa7a6f3bfb6a0c202b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009580"
---
# <a name="offline-fairplay-streaming-for-ios"></a>FairPlay-direktuppspelning offline för iOS

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 3](../latest/offline-fairplay-for-ios.md)
> * [Version 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services tillhandahåller en uppsättning välkända [innehålls skydds tjänster](https://azure.microsoft.com/services/media-services/content-protection/) som omfattar:

- Microsoft PlayReady
- Google-Widevine
- Apple-FairPlay
- AES-128-kryptering

Digital Rights Management (DRM)/Advanced Encryption Standard (AES) kryptering av innehåll utförs dynamiskt vid begäran om olika strömnings protokoll. DRM-Licens/AES-dekryptering av nyckel leverans tjänster tillhandahålls också av Media Services.

Förutom att skydda innehåll för online-direktuppspelning över olika strömnings protokoll, är offline-läge för skyddat innehåll också en ofta efterfrågad funktion. Stöd för offline-läge krävs i följande scenarier:

* Uppspelning när Internet anslutning inte är tillgängligt, till exempel under resan.
* Vissa innehålls leverantörer kan förhindra att DRM-licenser levereras bortom ett land/regions gräns. Om användarna vill titta på innehåll medan de reser utanför landet/regionen krävs offline-nedladdning.
* I vissa länder/regioner är Internet tillgänglighet och/eller bandbredd fortfarande begränsad. Användarna kan välja att ladda ned för att kunna se innehåll i en upplösning som är tillräckligt hög för en tillfredsställande visnings upplevelse. I det här fallet är problemet vanligt vis inte nätverks tillgänglighet men begränsad nätverks bandbredd. OVP-providers (OTT)/online-video plattform () stöder offline-läge.

Den här artikeln beskriver stöd för FairPlay streaming (FPS) offline-läge som är riktad mot enheter som kör iOS 10 eller senare. Den här funktionen stöds inte för andra Apple-plattformar, till exempel Watch, tvOS eller Safari på macOS.

## <a name="preliminary-steps"></a>Preliminära steg
Innan du implementerar offline DRM för FairPlay på en iOS 10 +-enhet:

* Bekanta dig med innehålls skyddet online för FairPlay. Mer information finns i följande artiklar och exempel:

    - [Apple FairPlay streaming för Azure Media Services är allmänt tillgänglig](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Skydda ditt HLS-innehåll med Apple FairPlay eller Microsoft PlayReady](./media-services-protect-hls-with-fairplay.md)
    - [Ett exempel för strömning online i FPS](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Hämta SDK: n för FPS från Apple Developer Network. SDK: n för FPS innehåller två komponenter:

    - Server-SDK för FPS, som innehåller nyckel säkerhetsmodulen (KSM), klient exempel, en specifikation och en uppsättning test vektorer.
    - Distributions paketet för FPS, som innehåller D-funktions specifikationen, tillsammans med anvisningar om hur du genererar ett FPS-certifikat, kundspecifik privat nyckel och en hemlig program nyckel. Apple utfärdar distributions paketet för FPS endast till licensierade innehålls leverantörer.

## <a name="configuration-in-media-services"></a>Konfiguration i Media Services
För konfiguration av offline-läge i FPS via [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)använder du Media Services .NET SDK-version 4.0.0.4 eller senare, vilket ger nödvändig API för att konfigurera offline-läge för FPS.
Du behöver också fungerande kod för att konfigurera innehålls skydd i online-läges FPS. När du har fått koden för att konfigurera online-läge innehålls skydd för FPS behöver du bara följande två ändringar.

## <a name="code-change-in-the-fairplay-configuration"></a>Kod ändring i FairPlay-konfigurationen
Den första ändringen är att definiera ett booleskt värde för "Aktivera offline-läge", som kallas objDRMSettings. EnableOfflineMode, som är sant när offline-scenariot för aktive ras. Beroende på den här indikatorn gör du följande ändring i FairPlay-konfigurationen:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Kod ändring i konfigureringen av till gångs leverans principen
Den andra ändringen är att lägga till den tredje nyckeln i ord listan<AssetDeliveryPolicyConfigurationKey, String>.
Lägg till AssetDeliveryPolicyConfigurationKey som du ser här:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Efter det här steget innehåller <Dictionary_AssetDeliveryPolicyConfigurationKey> sträng i till gångs leverans principen följande tre poster:

* AssetDeliveryPolicyConfigurationKey. FairPlayBaseLicenseAcquisitionUrl eller AssetDeliveryPolicyConfigurationKey. FairPlayLicenseAcquisitionUrl, beroende på faktorer som en FPS-KSM/nyckel server som används och om du återanvänder samma till gångs leverans princip över flera till gångar
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Nu är ditt Media Services-konto konfigurerat för att leverera FairPlay-licenser offline.

## <a name="sample-ios-player"></a>Exempel på iOS-spelare
Stöd för offline-läge i FPS är bara tillgängligt på iOS 10 och senare. Server-SDK: n för FPS (version 3,0 eller senare) innehåller dokumentet och exemplet för offline-läge för FPS. Mer specifikt innehåller Server-SDK för FPS (version 3,0 eller senare) följande två objekt som är relaterade till offline-läge:

* Dokument: "offline-uppspelning med FairPlay streaming och HTTP Live Streaming". Apple, 14 september 2016. I FPS Server SDK version 4,0 är det här dokumentet sammanslaget i huvud-FPS-dokumentet.
* Exempel kod: HLSCatalog-exempel för offline-läge för FPS i \FairPlay Streaming Server SDK version 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. I HLSCatalog-exempel appen används följande kod för att implementera offline-läges funktioner:

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

![Offline-FairPlay iOS app-strömmar](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Använd din egen test video-URL, FairPlay License Acquisition URL och URL för program certifikat om du har konfigurerat dem. Eller också kan du fortsätta till nästa avsnitt, som innehåller test exempel.

## <a name="integrated-test"></a>Integrerat test
Tre test exempel i Media Services som tar upp följande tre scenarier:

* FPS-skyddad, med video, ljud och alternativt ljud spår
* FPS-skyddad, med video och ljud, men utan alternativ ljud spår
* FPS skyddas, med endast video och inget ljud

Du hittar dessa exempel på [den här demo webbplatsen](https://aka.ms/poc#22), med motsvarande program certifikat som finns i en Azure-webbapp.
Med version 3 eller version 4-exemplet på Server-SDK: n för FPS, om en Master-spelnings lista innehåller alternativ ljud, i offlineläge spelar den bara ljud. Därför måste du randig det alternativa ljudet. De andra och tredje exemplen som listas tidigare fungerar med andra ord i onlineläge och offline-läge. Exemplet som visas först spelar bara ljud i offlineläge, medan direkt uppspelningen fungerar som den ska.

## <a name="faq"></a>Vanliga frågor
Följande vanliga frågor ger hjälp med fel sökning:

- **Varför spelas bara ljud upp men inte video i offlineläge?** Det här beteendet verkar vara avsiktligt av exempel appen. När ett alternativt ljud spår finns (vilket är fallet för HLS) under offlineläge, är både iOS 10 och iOS 11 standardvärdet för det alternativa ljud spåret. För att kompensera det här beteendet för offline-läge för FPS tar du bort det alternativa ljud spåret från data strömmen. Om du vill göra detta på Media Services lägger du till det dynamiska manifest filtret "ljud-Only = false". Med andra ord slutar en HLS-URL med. ISM/manifest (format = M3U8-AAPL, endast ljud = falskt). 
- **Varför spelar det fortfarande bara ljud utan video i offlineläge efter att jag lagt till endast ljud = falskt?** Beroende på cache nyckel designen för Content Delivery Network (CDN) kan innehållet cachelagras. Rensa cacheminnet.
- **Stöds offline-läge för FPS även för iOS 11 förutom iOS 10?** Ja. Offline-läge för FPS stöds för iOS 10 och iOS 11.
- **Varför kan jag inte hitta dokumentet "offline-uppspelning med FairPlay streaming och HTTP Live Streaming" i serverns SDK för FPS?** Eftersom FPS Server SDK version 4 har det här dokumentet slagits samman i programmerings guiden "FairPlay streaming".
- **Vad står den sista parametern för i följande API för offline-läge för FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Dokumentationen för detta API finns i [metoden FairPlayConfiguration. CreateSerializedFairPlayOptionConfiguration](/dotnet/api/microsoft.windowsazure.mediaservices.client.fairplay.fairplayconfiguration.createserializedfairplayoptionconfiguration). Parametern representerar varaktigheten för offline-hyran, med en sekund som är enheten.
- **Vad är den nedladdade/offline-filstrukturen på iOS-enheter?** Den nedladdade fil strukturen på en iOS-enhet ser ut som på följande skärm bild. `_keys`Mappen lagrar hämtade fps-licenser med en lagrings fil för varje licens tjänst värd. `.movpkg`Mappen lagrar ljud-och video innehåll. Den första mappen med ett namn som slutar med ett streck följt av ett numeriskt objekt innehåller video innehåll. Det numeriska värdet är PeakBandwidth för video åter givningarna. Den andra mappen med ett namn som slutar med ett bindestreck följt av 0 innehåller ljud innehåll. Den tredje mappen med namnet "data" innehåller huvud spelnings listan för innehållet i FPS. Slutligen boot.xml ger en fullständig beskrivning av innehållet i `.movpkg` mappen. 

![FairPlay iOS-exempel fil struktur](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

En exempel boot.xml fil:
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

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="summary"></a>Sammanfattning
Det här dokumentet innehåller följande steg och information som du kan använda för att implementera offline-läge för FPS:

* Media Services konfiguration av innehålls skydd via Media Services .NET API konfigurerar dynamisk FairPlay kryptering och FairPlay licens leverans i Media Services.
* En iOS-spelare baserad på exemplet från Server-SDK: n för FPS ställer in en iOS-spelare som kan spela upp FPS-innehåll i direkt uppspelnings läge eller offline-läge.
* Videoklipp med exempel FPS används för att testa offline-läge och direkt uppspelning.
* Vanliga frågor och svar om offline-läge för FPS.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
