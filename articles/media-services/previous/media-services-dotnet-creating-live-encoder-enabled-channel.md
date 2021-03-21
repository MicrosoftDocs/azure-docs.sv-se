---
title: Så här utför du en liveuppspelning med Azure Media Services för att skapa dataströmmar med flera bithastigheter med .NET  | Microsoft Docs
description: Den här självstudien visar dig stegen för att skapa en kanal som tar emot en direktsänd dataström med enkel bithastighet och kodar den till en dataström med multibithastighet med hjälp av .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 4df5e690-ff63-47cc-879b-9c57cb8ec240
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 7bf00abfaa56453303fa85e3062a7fafc7e0ac56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014182"
---
# <a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Så här utför du direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet med .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST-API](/rest/api/media/operations/channel)
> 
> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> 

## <a name="overview"></a>Översikt
Den här självstudien visar dig stegen för att skapa en **kanal** som tar emot en  direktsänd dataström med enkel bithastighet och kodar den till en dataström med multibithastighet.

Mer konceptinformation relaterad till kanaler som är aktiverade för Live Encoding finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).

## <a name="common-live-streaming-scenario"></a>Vanligt scenario för direktsänd strömning
Följande steg beskriver uppgifter som ingår i att skapa vanliga program för direktsänd strömning.

> [!NOTE]
> Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amshelp@microsoft.com om du behöver köra en kanal under en längre tidsperiod.

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal livekodare som kan mata ut en dataström med enkel bithastighet i något av följande protokoll: RTMP eller Smooth Streaming. Mer information finns i [Support och direktsända kodare för Azure Media Services RTMP](https://go.microsoft.com/fwlink/?LinkId=532824).

    Det här steget kan också utföras när du har skapat din kanal.

2. Skapa och starta en kanal.
3. Hämta kanalens infognings-URL.

    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.

4. Hämta kanalens förhandsgransknings-URL.

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.

5. Skapa en tillgång.
6. Om du vill att tillgången ska vara dynamiskt krypterad under uppspelningen gör du följande:
7. Skapa en innehållsnyckel.
8. Konfigurera en auktoriseringsprincip  för innehållsnyckeln.
9. Konfigurera en princip för tillgångsleveranser (används av dynamisk paketering och dynamisk kryptering).
10. Skapa ett program och ange att den tillgång som du skapade ska användas.
11. Publicera tillgången som är associerad till programmet genom att skapa en OnDemand-positionerare.

    >[!NOTE]
    >När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**. 

12. Starta programmet när du är redo att påbörja strömning och arkivering.
13. Som alternativ kan den direktsända kodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
14. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.
15. Ta bort programmet (och ta eventuellt bort tillgången).

## <a name="what-youll-learn"></a>Det här lär du dig
I den här artikeln visas hur du utför olika åtgärder i kanaler och program med hjälp av Media Services .NET SDK. Eftersom många åtgärder är långvariga används .NET-API:er som hanterar långvariga åtgärder.

Artikeln visar hur du gör följande:

1. Skapa och starta en kanal. Långvariga API:er används.
2. Hämta kanalernas infogningsslutpunkter (indata). Den här slutpunkten ska tillhandahållas till den kodare som kan skicka en direktsänd dataström med enkel bithastighet.
3. Hämta förhandsgranskningsslutpunkten. Den här slutpunkten används för att förhandsgranska dataströmmen.
4. Skapa en tillgång som används för att lagra innehållet. Principerna för tillgångsleverans ska också konfigureras, så som visas i det här exemplet.
5. Skapa ett program och ange att den tillgång som du skapade tidigare ska användas. Starta programmet. Långvariga API:er används.
6. Skapa en positionerare för tillgången, så att innehållet publiceras och kan strömmas till dina klienter.
7. Visa eller dölj pekdatorer. Starta och stoppa annonser. Långvariga API:er används.
8. Rensa din kanal och alla associerade resurser.

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att kunna genomföra självstudien.

* Ett Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Du får kredit som kan användas för att prova Azure-tjänster som normalt inte är kostnadsfria. Du kan behålla kontot även efter att krediten är slut och använda gratis Azure-tjänster och -funktioner som  Web Apps-funktionen i Azure App Service.
* Ett Media Services-konto. Mer information om att skapa ett Media Services-konto finns i [Skapa konto](media-services-portal-create-account.md).
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate eller Express) eller senare versioner.
* Du måste använda Media Services .NET SDK version 3.2.0.0 eller senare.
* En webbkamera och en kodare som kan skicka en direktsänd dataström i enkel bithastighet.

## <a name="considerations"></a>Överväganden
* Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amshelp@microsoft.com om du behöver köra en kanal under en längre tidsperiod.
* Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

## <a name="download-sample"></a>Ladda ned exempel

Du kan hämta exemplet som beskrivs i artikeln [här](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).

## <a name="set-up-for-development-with-media-services-sdk-for-net"></a>Konfigurera för utveckling med Media Services SDK för .NET

Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md). 

## <a name="code-example"></a>Kodexempel

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Net;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

namespace EncodeLiveStreamWithAmsClear
{
    class Program
    {
        private const string ChannelName = "channel001";
        private const string AssetName = "asset001";
        private const string ProgramName = "program001";

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            IChannel channel = CreateAndStartChannel();

            // The channel's input endpoint:
            string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();

            Console.WriteLine("Intest URL: {0}", ingestUrl);


            // Use the previewEndpoint to preview and verify 
            // that the input from the encoder is actually reaching the Channel. 
            string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();

            Console.WriteLine("Preview URL: {0}", previewEndpoint);

            // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
            // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
            // The thumbnail is exposed via the same end-point as the Channel Preview URL.
            string thumbnailUri = new UriBuilder
            {
                Scheme = Uri.UriSchemeHttps,
                Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                Path = "thumbnails/input.jpg"
            }.Uri.ToString();

            Console.WriteLine("Thumbain URL: {0}", thumbnailUri);

            // Once you previewed your stream and verified that it is flowing into your Channel, 
            // you can create an event by creating an Asset, Program, and Streaming Locator. 
            IAsset asset = CreateAndConfigureAsset();

            IProgram program = CreateAndStartProgram(channel, asset);

            ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);

            // You can use slates and ads only if the channel type is Standard.  
            StartStopAdsSlates(channel);

            // Once you are done streaming, clean up your resources.
            Cleanup(channel);
        }

        public static IChannel CreateAndStartChannel()
        {
            var channelInput = CreateChannelInput();
            var channelPreview = CreateChannelPreview();
            var channelEncoding = CreateChannelEncoding();

            ChannelCreationOptions options = new ChannelCreationOptions
            {
                EncodingType = ChannelEncodingType.Standard,
                Name = ChannelName,
                Input = channelInput,
                Preview = channelPreview,
                Encoding = channelEncoding
            };

            Log("Creating channel");
            IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
            string channelId = TrackOperation(channelCreateOperation, "Channel create");

            IChannel channel = _context.Channels.FirstOrDefault(c => c.Id == channelId);

            Log("Starting channel");
            var channelStartOperation = channel.SendStartOperation();
            TrackOperation(channelStartOperation, "Channel start");

            return channel;
        }

        /// <summary>
        /// Create channel input, used in channel creation options. 
        /// </summary>
        /// <returns></returns>
        private static ChannelInput CreateChannelInput()
        {
            // When creating a Channel, you can specify allowed IP addresses in one of the following formats: 
            // IpV4 address with 4 numbers
            // CIDR address range

            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.FragmentedMP4,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                        Name = "TestChannelInput001",
                        Address = IPAddress.Parse("0.0.0.0"),
                        SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }

        /// <summary>
        /// Create channel preview, used in channel creation options. 
        /// </summary>
        /// <returns></returns>
        private static ChannelPreview CreateChannelPreview()
        {
            // When creating a Channel, you can specify allowed IP addresses in one of the following formats: 
            // IpV4 address with 4 numbers
            // CIDR address range

            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                {
                    new IPRange
                    {
                    Name = "TestChannelPreview001",
                    Address = IPAddress.Parse("0.0.0.0"),
                    SubnetPrefixLength = 0
                    }
                }
                }
            };
        }

        /// <summary>
        /// Create channel encoding, used in channel creation options. 
        /// </summary>
        /// <returns></returns>
        private static ChannelEncoding CreateChannelEncoding()
        {
            return new ChannelEncoding
            {
                SystemPreset = "Default720p",
                IgnoreCea708ClosedCaptions = false,
                AdMarkerSource = AdMarkerSource.Api
            };
        }

        /// <summary>
        /// Create an asset and configure asset delivery policies.
        /// </summary>
        /// <returns></returns>
        public static IAsset CreateAndConfigureAsset()
        {
            IAsset asset = _context.Assets.Create(AssetName, AssetCreationOptions.None);

            IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption,
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

            asset.DeliveryPolicies.Add(policy);

            return asset;
        }

        /// <summary>
        /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
        /// however each Program must have a unique name within your Media Services account.
        /// </summary>
        /// <param name="channel"></param>
        /// <param name="asset"></param>
        /// <returns></returns>
        public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
        {
            IProgram program = channel.Programs.Create(ProgramName, TimeSpan.FromHours(3), asset.Id);
            Log("Program created", program.Id);

            Log("Starting program");
            var programStartOperation = program.SendStartOperation();
            TrackOperation(programStartOperation, "Program start");

            return program;
        }

        /// <summary>
        /// Create locators in order to be able to publish and stream the video.
        /// </summary>
        /// <param name="asset"></param>
        /// <param name="ArchiveWindowLength"></param>
        /// <returns></returns>
        public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
        {
            // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
            var locator = _context.Locators.CreateLocator
            (
                LocatorType.OnDemandOrigin,
                asset,
                _context.AccessPolicies.Create
                (
                    "Live Stream Policy",
                    ArchiveWindowLength,
                    AccessPermissions.Read
                )
            );

            return locator;
        }

        /// <summary>
        /// Perform operations on slates.
        /// </summary>
        /// <param name="channel"></param>
        public static void StartStopAdsSlates(IChannel channel)
        {
            int cueId = new Random().Next(int.MaxValue);
            var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));

            Log("Creating asset");
            var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
            Log("Slate asset created", slateAsset.Id);

            Log("Uploading file");
            var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
            assetFile.Upload(path);
            assetFile.IsPrimary = true;
            assetFile.Update();

            Log("Showing slate");
            var showSlateOperation = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
            TrackOperation(showSlateOperation, "Show slate");

            Log("Hiding slate");
            var hideSlateOperation = channel.SendHideSlateOperation();
            TrackOperation(hideSlateOperation, "Hide slate");

            Log("Starting ad");
            var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
            TrackOperation(startAdOperation, "Start ad");

            Log("Ending ad");
            var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
            TrackOperation(endAdOperation, "End ad");

            Log("Deleting slate asset");
            slateAsset.Delete();
        }

        /// <summary>
        /// Clean up resources associated with the channel.
        /// </summary>
        /// <param name="channel"></param>
        public static void Cleanup(IChannel channel)
        {
            IAsset asset;
            if (channel != null)
            {
                foreach (var program in channel.Programs)
                {
                    asset = _context.Assets.FirstOrDefault(se => se.Id == program.AssetId);

                    Log("Stopping program");
                    var programStopOperation = program.SendStopOperation();
                    TrackOperation(programStopOperation, "Program stop");

                    program.Delete();

                    if (asset != null)
                    {
                        Log("Deleting locators");
                        foreach (var l in asset.Locators)
                            l.Delete();

                        Log("Deleting asset");
                        asset.Delete();
                    }
                }

                Log("Stopping channel");
                var channelStopOperation = channel.SendStopOperation();
                TrackOperation(channelStopOperation, "Channel stop");

                Log("Deleting channel");
                var channelDeleteOperation = channel.SendDeleteOperation();
                TrackOperation(channelDeleteOperation, "Channel delete");
            }
        }

        /// <summary>
        /// Track long running operations.
        /// </summary>
        /// <param name="operation"></param>
        /// <param name="description"></param>
        /// <returns></returns>
        public static string TrackOperation(IOperation operation, string description)
        {
            string entityId = null;
            bool isCompleted = false;

            Log("starting to track ", null, operation.Id);
            while (isCompleted == false)
            {
                operation = _context.Operations.GetOperation(operation.Id);
                isCompleted = IsCompleted(operation, out entityId);
                System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
            }
            // If we got here, the operation succeeded.
            Log(description + " in completed", operation.TargetEntityId, operation.Id);

            return entityId;
        }

        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created entity Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the entity Id associated with the successful operation is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        private static bool IsCompleted(IOperation operation, out string entityId)
        {
            bool completed = false;

            entityId = null;

            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    Log("operation failed", operation.TargetEntityId, operation.Id);
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    entityId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    Log("operation in progress", operation.TargetEntityId, operation.Id);
                    break;
            }
            return completed;
        }

        private static void Log(string action, string entityId = null, string operationId = null)
        {
            Console.WriteLine(
            "{0,-21}{1,-51}{2,-51}{3,-51}",
            DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
            action,
            entityId ?? string.Empty,
            operationId ?? string.Empty);
        }
    }
}
```

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
