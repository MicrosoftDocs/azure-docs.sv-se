---
title: Avsökning Long-Running åtgärder | Microsoft Docs
description: 'Azure Media Services erbjuder API: er som skickar begär anden till Media Services för att starta åtgärder (till exempel skapa, starta, stoppa eller ta bort en kanal) körs de här åtgärderna länge. Det här avsnittet visar hur du avsöker tids krävande åtgärder.'
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
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
ms.openlocfilehash: 7926f7aaa427d49d13cab5e13f5153bcd22e5898
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013830"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Leverera Direktsänd strömning med Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt

Microsoft Azure Media Services erbjuder API: er som skickar begär anden till Media Services för att starta åtgärder (till exempel: skapa, starta, stoppa eller ta bort en kanal). De här åtgärderna är tids krävande.

Media Services .NET SDK innehåller API: er som skickar begäran och väntar på att åtgärden ska slutföras (internt, API: erna avsöker för åtgärds förlopp i vissa intervall). Till exempel när du anropar en kanal. Start (), metoden returnerar när kanalen har startats. Du kan också använda den asynkrona versionen: väntar på kanal. StartAsync () (för information om uppgiftsbaserade asynkrona mönster, se [Knacka](./media-services-mes-schema.md)). API: er som skickar en åtgärds förfrågan och sedan söker efter statusen tills åtgärden har slutförts kallas för "avsöknings metoder". Dessa metoder (särskilt den asynkrona versionen) rekommenderas för omfattande klient program och/eller tillstånds känsliga tjänster.

Det finns scenarier där ett program inte kan vänta på en tids krävande http-begäran och vill söka efter åtgärdens förlopp manuellt. Ett typiskt exempel är en webbläsare som interagerar med en tillstånds lös webb tjänst: när webbläsaren begär att skapa en kanal, initierar webb tjänsten en tids krävande åtgärd och returnerar åtgärds-ID: t till webbläsaren. Webbläsaren kan sedan be webb tjänsten att hämta åtgärds status baserat på ID. Media Services .NET SDK innehåller API: er som är användbara för det här scenariot. Dessa API: er kallas "icke-avsöknings metoder".
"Icke-avsöknings metoder" har följande namn mönster: skicka *OperationName*-åtgärd (till exempel SendCreateOperation). Skicka *OperationName*-åtgärds metoder returnerar **IOperation** -objektet; det returnerade objektet innehåller information som kan användas för att spåra åtgärden. **Uppgiften \<IOperation>** skicka *OperationName*-OperationAsync metoder returneras.

För närvarande stöder följande klasser icke-avsöknings metoder:  **kanal**, **StreamingEndpoint** och **program**.

Om du vill avsöka efter åtgärds status använder du **Get operation** -metoden i **OperationBaseCollection** -klassen. Använd följande intervall för att kontrol lera åtgärds status: för **kanal** -och **StreamingEndpoint** -åtgärder ska du använda 30 sekunder; Använd 10 sekunder för **program** åtgärder.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Exempel

I följande exempel definieras en klass med namnet **ChannelOperations**. Den här klass definitionen kan vara en start punkt för din webb tjänst klass definition. För enkelhetens skull använder följande exempel de icke-asynkrona versionerna av metoderna.

Exemplet visar också hur klienten kan använda den här klassen.

### <a name="channeloperations-class-definition"></a>Definition av ChannelOperations-klass

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
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

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
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

    private static ChannelPreview CreateChannelPreview()
    {
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

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>Klient koden

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]