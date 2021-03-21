---
title: Skydda HLS-innehåll med Microsoft PlayReady eller Apple FairPlay – Azure | Microsoft Docs
description: Det här avsnittet ger en översikt och visar hur du använder Azure Media Services för att dynamiskt Kryptera ditt HTTP Live Streaming (HLS)-innehåll med Apple FairPlay. Det visar också hur du använder leverans tjänsten Media Services licens för att leverera FairPlay-licenser till klienter.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 15aab28b7dfbaf305412f1080346b54cc6827437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009648"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Skydda ditt HLS-innehåll med Apple FairPlay eller Microsoft PlayReady

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).   > inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Med Azure Media Services kan du dynamiskt Kryptera ditt HTTP Live Streaming-innehåll (HLS) med hjälp av följande format:  

* **Klar text nyckel för AES-128-kuvert**

    Hela segmentet krypteras med **AES-128 CBC** -läget. Dekrypteringen av data strömmen stöds av iOS och OS X Player internt. Mer information finns i [använda AES-128 dynamisk kryptering och nyckel leverans tjänst](media-services-protect-with-aes128.md).
* **Apple-FairPlay**

    De enskilda video-och ljud exemplen krypteras med **AES-128 CBC** -läget. **Fairplay Streaming** (FPS) är integrerat i enhetens operativ system, med inbyggt stöd för iOS och Apple TV. Safari på OS X aktiverar FPS med hjälp av EME-gränssnittet (Encrypted Media Extensions).
* **Microsoft PlayReady**

Följande bild visar arbets flödet **HLS + Fairplay eller PlayReady Dynamic Encryption** .

![Diagram över dynamiskt krypterings arbets flöde](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Den här artikeln visar hur du använder Media Services för att dynamiskt Kryptera ditt HLS-innehåll med Apple FairPlay. Det visar också hur du använder leverans tjänsten Media Services licens för att leverera FairPlay-licenser till klienter.

> [!NOTE]
> Om du också vill kryptera ditt HLS-innehåll med PlayReady måste du skapa en gemensam innehålls nyckel och associera den med din till gång. Du måste också konfigurera innehålls nyckelns auktoriseringsprincip enligt beskrivningen i [använda PlayReady Dynamic common Encryption](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Krav och överväganden

Följande krävs när du använder Media Services för att leverera HLS-krypterade med FairPlay och leverera FairPlay-licenser:

  * Ett Azure-konto. Mer information finns i [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Ett Media Services-konto. För att skapa ett, se [skapa ett Azure Media Services konto med hjälp av Azure Portal](media-services-portal-create-account.md).
  * Registrera dig med [Apple Development-programmet](https://developer.apple.com/).
  * Apple kräver att innehålls ägaren hämtar [distributions paketet](https://developer.apple.com/contact/fps/). Ange att du redan har implementerat KSM (Key Security Module) med Media Services och att du begär det slutliga FPS-paketet. Det finns instruktioner i det sista FPS-paketet för att generera certifiering och hämta programmets hemliga nyckel (fråga). Du kan använda be att konfigurera FairPlay.
  * Azure Media Services .NET SDK-version **3.6.0** eller senare.

Följande saker måste anges på sidan för Media Services nyckel leverans:

  * **App-certifikat (AC)**: det här är en PFX-fil som innehåller den privata nyckeln. Du skapar filen och krypterar den med ett lösen ord.

       När du konfigurerar en nyckel leverans princip måste du ange lösen ordet och. pfx-filen i base64-format.

      Följande steg beskriver hur du skapar en. PFX-certifikat fil för FairPlay:

    1. Installera OpenSSL från https://slproweb.com/products/Win32OpenSSL.html .

        Gå till mappen där FairPlay-certifikatet och andra filer som levererats av Apple är.
    2. Kör följande kommando från kommandoraden. Detta konverterar. CER-filen till en. PEM-fil.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-informerad der-in FairPlay. cer-out FairPlay-out. pem
    3. Kör följande kommando från kommandoraden. Detta konverterar. pem-filen till en. pfx-fil med den privata nyckeln. Lösen ordet för. pfx-filen uppmanas sedan av OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12-export FairPlay-out. pfx-INKEY PrivateKey. pem-in FairPlay-out. pem-Passin file:privatekey-pem-pass.txt
  * **Lösen ord för app-certifikat**: lösen ordet för att skapa. pfx-filen.
  * **Lösen ord för app-certifikat**: du måste överföra lösen ordet, på samma sätt som du överför andra Media Services nycklar. Använd Enum-värdet **ContentKeyType. FairPlayPfxPassword** för att hämta Media Services-ID: t. Detta är vad de behöver använda i alternativet för nyckel leverans princip.
  * **IV**: Detta är ett slumpmässigt värde på 16 byte. Den måste matcha IV i till gångs leverans principen. Du genererar IV och placerar det på båda platserna: till gångs leverans principen och alternativet för nyckel leverans princip.
  * **Fråga**: den här nyckeln tas emot när du genererar certifieringen med hjälp av Apple Developer-portalen. Varje utvecklings grupp får en unik fråga. Spara en kopia av frågan och lagra den på ett säkert ställe. Du måste konfigurera fråga som FairPlayAsk om du vill Media Services senare.
  * Fråge **-ID**: det här ID: t hämtas när du överför frågan till Media Services. Du måste ladda upp fråga med hjälp av uppräkning svärdet **ContentKeyType. FairPlayAsk** . Resultatet blir att Media Services-ID: t returneras, och det är vad som ska användas när du anger alternativ för nyckel leverans princip.

Följande saker måste anges av klient sidan för FPS:

  * **App-certifikat (AC)**: det här är en CER/. der-fil som innehåller den offentliga nyckeln, som operativ systemet använder för att kryptera vissa nytto laster. Media Services behöver veta om det, eftersom det krävs av spelaren. Nyckel leverans tjänsten dekrypterar den med motsvarande privata nyckel.

Om du vill spela upp en FairPlay-krypterad ström, få en riktig fråga först och sedan skapa ett verkligt certifikat. Den här processen skapar alla tre delarna:

  * . der-fil
  * . pfx-fil
  * lösen ord för. pfx

Följande klienter stöder HLS med **AES-128 CBC** -kryptering: Safari på OS X, Apple TV och iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurera FairPlay Dynamic Encryption and License Delivery Services
Följande är allmänna steg för att skydda dina till gångar med FairPlay med hjälp av Media Services licens leverans tjänst och även med dynamisk kryptering.

1. Skapa en tillgång och överföra filer till tillgången.
2. Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet.
3. Skapa en innehållsnyckel och associera den med den kodade tillgången.  
4. Konfigurera innehållsnyckelns auktoriseringsprincip. Ange följande:

   * Leverans metoden (i det här fallet FairPlay).
   * Konfiguration av princip alternativ för FairPlay. Mer information om hur du konfigurerar FairPlay finns i **ConfigureFairPlayPolicyOptions ()-** metoden i exemplet nedan.

     > [!NOTE]
     > Vanligt vis vill du bara konfigurera FairPlay-principinställningar en gång, eftersom du bara kommer att ha en uppsättning certifiering och en fråga.
     >
     >
   * Begränsningar (öppna eller token).
   * Information som är unik för den viktiga leverans typen som definierar hur nyckeln levereras till klienten.
5. Konfigurera till gångs leverans principen. Konfigurationen för leverans principen omfattar:

   * Leverans protokoll (HLS).
   * Typen av dynamisk kryptering (common CBC Encryption).
   * URL för licens hämtning.

     > [!NOTE]
     > Om du vill leverera en ström som är krypterad med FairPlay och ett annat DRM-system (Digital Rights Management) måste du konfigurera separata leverans principer:
     >
     > * En IAssetDeliveryPolicy för att konfigurera dynamisk anpassningsbar strömning via HTTP (bindestreck) med Common Encryption (CENC) (PlayReady + Widevine) och smidigt med PlayReady
     > * En annan IAssetDeliveryPolicy för att konfigurera FairPlay för HLS
     >
     >
6. Skapa en OnDemand-lokaliserare för att få en strömnings-URL.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Använd FairPlay Key Delivery från Player-appar
Du kan utveckla spelarens appar med hjälp av iOS SDK. För att kunna spela upp FairPlay innehåll måste du implementera License Exchange-protokollet. Det här protokollet har inte angetts av Apple. Det är upp till varje app att skicka förfrågningar om nyckel leverans. Den Media Services FairPlay Key Delivery service förväntar sig att SPC ska komma som ett meddelande via www-form-URL-kodat inlägg i följande format:

`spc=<Base64 encoded SPC>`

> [!NOTE]
> Azure Media Player stöder uppspelning av FairPlay. Mer information finns i [Azure Media Player-dokumentationen](https://amp.azure.net/libs/amp/latest/docs/index.html) .
>
>

## <a name="streaming-urls"></a>Strömmande URL: er
Om din till gång har krypterats med fler än ett DRM, bör du använda en krypterings tagg i streaming-URL: en: (format = ' 3u8-AAPL ', Encryption = ' xxx ').

Följande gäller:

* Endast noll eller en krypterings typ kan anges.
* Krypterings typen behöver inte anges i URL: en om bara en kryptering tillämpades på till gången.
* Krypterings typen är Skift läges okänslig.
* Följande krypterings typer kan anges:  
  * **Cenc**: common Encryption (PlayReady eller Widevine)
  * **CBCS-AAPL**: Fairplay
  * **CBC**: kryptering med AES-kuvert

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

1. Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md). 
2. Lägg till följande element i **appSettings** som definieras i filen app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exempel

Följande exempel visar möjligheten att använda Media Services för att leverera ditt innehåll som krypteras med FairPlay. Den här funktionen introducerades i Azure Media Services SDK för .NET version 3.6.0. 

Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

Se till att uppdatera variablerna så att de pekar på mappar där dina indatafiler finns.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                    };

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }


        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps-media-services-learning-paths"></a>Nästa steg: Utbildningsvägar för Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
