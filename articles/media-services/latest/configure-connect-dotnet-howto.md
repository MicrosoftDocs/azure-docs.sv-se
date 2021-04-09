---
title: Ansluta till Azure Media Services v3 API-.NET
description: Den här artikeln visar hur du ansluter till Media Services v3 API med .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 8946f6e94dd26db45622bc7609fb2375d59bb57e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455390"
---
# <a name="connect-to-media-services-v3-api---net"></a>Ansluta till Media Services v3 API-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du ansluter till Azure Media Services v3 .NET SDK med inloggnings metoden för tjänstens huvud namn.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](./create-account-howto.md). Kom ihåg att spara resurs gruppens namn och Media Services konto namnet
- Installera ett verktyg som du vill använda för .NET-utveckling. Stegen i den här artikeln visar hur du använder [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Du kan använda Visual Studio Code, se [arbeta med C#](https://code.visualstudio.com/docs/languages/csharp). Eller så kan du använda en annan kod redigerare.

> [!IMPORTANT]
> Granska [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Skapa ett konsolprogram

1. Starta Visual Studio. 
1. Från menyn **Arkiv** klickar du på **nytt**  >  **projekt**. 
1. Skapa ett **.net Core** -konsolprogram.

Exempel programmet i det här avsnittet är mål `netcoreapp2.0` . Koden använder async Main, som är tillgänglig från och med C# 7,1. Mer information finns i den här [bloggen](/archive/blogs/benwilli/async-main-is-available-but-hidden) .

## <a name="add-required-nuget-packagesassemblies"></a>Lägg till nödvändiga NuGet-paket/sammansättningar

1. I Visual Studio väljer du **verktyg**  >  **NuGet Package Manager**  >  **NuGet Manager Console**.
2. I fönstret **Package Manager-konsol** använder `Install-Package` du kommandot för att lägga till följande NuGet-paket. Till exempel `Install-Package Microsoft.Azure.Management.Media`.

|Paket|Beskrivning|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Kontrol lera att du använder det senaste Azure Media Services paketet genom att kontrol lera [Microsoft. Azure. Management. Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|

### <a name="other-required-assemblies"></a>Andra nödvändiga sammansättningar

- Azure. Storage. blobar
- Microsoft.Extensions.Configuration
- Microsoft.Extensions.Configuration. EnvironmentVariables
- Microsoft.Extensions.Configuration.Jspå
- Microsoft. rest. ClientRuntime. Azure. Authentication

## <a name="create-and-configure-the-app-settings-file"></a>Skapa och konfigurera appens inställnings fil

### <a name="create-appsettingsjson"></a>Skapa appsettings.jspå

1. Gå till   >  **filen allmän textfil**.
1. Ge den namnet "appsettings.jspå".
1. Ange egenskapen "Kopiera till utgående katalog" för. JSON-filen till "Kopiera om nyare" (så att programmet kan komma åt det när det publiceras).

### <a name="set-values-in-appsettingsjson"></a>Ange värden i appsettings.jspå

Kör `az ams account sp create` kommandot enligt beskrivningen i [API: er för åtkomst](./access-api-howto.md). Kommandot returnerar JSON som du bör kopiera till din "appsettings.js".
 
## <a name="add-configuration-file"></a>Lägga till en konfigurationsfil

För enkelhetens skull lägger du till en konfigurations fil som ansvarar för att läsa värden från "appsettings.jspå".

1. Lägg till en ny CS-klass i projektet. Ge den namnet `ConfigWrapper`. 
1. Klistra in följande kod i den här filen (det här exemplet förutsätter att du har namn området `ConsoleApp1` ).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Location
        {
            get { return _config["Location"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Ansluta till .NET-klienten

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I koden nedan skapar funktionen GetCredentialsAsync ServiceClientCredentials-objektet baserat på de autentiseringsuppgifter som anges i den lokala konfigurations filen.

1. Öppna `Program.cs`.
1. Klistra in följande kod:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Ladda upp, koda och strömma videor – .NET](stream-files-tutorial-with-api.md) 
- [Självstudie: strömma live med Media Services v3-.NET](stream-live-tutorial-with-api.md)
- [Självstudie: analysera videor med Media Services v3-.NET](analyze-videos-tutorial-with-api.md)
- [Skapa jobbindata från en lokal fil – .NET](job-input-from-local-file-how-to.md)
- [Skapa jobbindata från en HTTPS-URL – .NET](job-input-from-http-how-to.md)
- [Koda med en anpassad transformering – .NET](customize-encoder-presets-how-to.md)
- [Använda dynamisk AES-128-kryptering och tjänsten för nyckelleverans – .NET](protect-with-aes128.md)
- [Använda dynamisk DRM-kryptering och tjänsten för licensleverans – .NET](protect-with-drm.md)
- [Hämta en signeringsnyckel från den befintliga principen – .NET](get-content-key-policy-dotnet-howto.md)
- [Skapa filter med Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Avancerad exempel på video på begäran för Azure Functions v2 med Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Se även

* [.NET-referens](/dotnet/api/overview/azure/mediaservices/management)
* Fler kod exempel finns i [.NET SDK-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet) lagrings platsen.
