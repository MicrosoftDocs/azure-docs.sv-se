---
title: Konfigurera en dator för Media Services utveckling med .NET
description: Läs om kraven för Media Services med hjälp av Media Services SDK för .NET. Lär dig också hur du skapar en Visual Studio-app.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: dda7849b6a5e22eea4891eacb2678b4c500dc1e1
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693671"
---
# <a name="media-services-development-with-net"></a>Media Services utveckling med .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Den här artikeln beskriver hur du börjar utveckla Media Services program med hjälp av .NET.

Med **Azure Media Services .NET SDK** Library kan du program mera Media Services med .net. För att göra det ännu enklare att utveckla med .NET, finns **Azure Media Services .NET SDK-tillägg** . Det här biblioteket innehåller en uppsättning tilläggs metoder och hjälp funktioner som fören klar din .NET-kod. Båda biblioteken är tillgängliga via **NuGet** och **GitHub**.

## <a name="prerequisites"></a>Förutsättningar
* Ett Media Services-konto i en ny eller befintlig Azure-prenumeration. Se artikeln [så här skapar du ett Media Services-konto](media-services-portal-create-account.md).
* Operativ system: Windows 10, Windows 7, Windows 2008 R2 eller Windows 8.
* .NET Framework 4,5 eller senare.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt
I det här avsnittet visas hur du skapar ett projekt i Visual Studio och konfigurerar det för Media Services utveckling.  I det här fallet är projektet ett C#-program i Windows-konsolen, men samma installations steg som visas här gäller för andra typer av projekt som du kan skapa för Media Services program (till exempel ett Windows Forms program eller ett ASP.NET-webbprogram).

Det här avsnittet visar hur du använder **NuGet** för att lägga till Media Services .NET SDK-tillägg och andra beroende bibliotek.

Du kan också hämta de senaste Media Services .NET SDK-bitarna från GitHub ([GitHub.com/Azure/Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services) eller [GitHub.com/Azure/Azure-SDK-for-Media-Services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), skapa lösningen och lägga till referenserna till klient projektet. Alla nödvändiga beroenden hämtas och extraheras automatiskt.

1. Skapa ett nytt C#-konsolprogram i Visual Studio. Ange **namn**, **plats** och **lösnings namn** och klicka sedan på OK.
2. Skapa lösningen.
3. Använd **NuGet** för att installera och lägga till **Azure Media Services .NET SDK-tillägg** (**windowsazure. Media Services. Extensions**). När du installerar det här paketet installeras även **Media Services .NET SDK** och lägger till alla andra nödvändiga beroenden.
   
    Se till att du har den senaste versionen av NuGet installerad. Mer information och Installationsinstruktioner finns i [NuGet](https://nuget.codeplex.com/).

    1. I Solution Explorer högerklickar du på namnet på projektet och väljer **Hantera NuGet-paket**.

    2. Dialogrutan Hantera NuGet-paket öppnas.

    3. I Onlinegalleri, Sök efter Azure Media Services-tillägg, Välj **Azure Media Services .NET SDK-tillägg** (**windowsazure. Media Services. Extensions**) och klicka sedan på knappen **Installera** .
   
    4. Projektet ändras och refererar till Media Services .NET SDK-tillägg, Media Services .NET SDK och andra beroende sammansättningar läggs till.
4. Överväg att aktivera återställning av NuGet-paket för att främja en renare utvecklings miljö. Mer information finns i [NuGet Package Restore "](https://docs.nuget.org/consume/package-restore).
5. Lägg till en referens till **System.Configuration** -sammansättning. Den här sammansättningen innehåller System.Configuration. **ConfigurationManager** -klass som används för att komma åt konfigurationsfiler (till exempel App.config).
   
    1. Om du vill lägga till referenser med hjälp av dialog rutan hantera referenser högerklickar du på projekt namnet i Solution Explorer. Klicka sedan på **Lägg till** och sedan på **referens...**.
   
    2. Dialog rutan hantera referenser visas.
    3. Under .NET Framework-sammansättningar kan du söka efter och välja System.Configuration-sammansättningen och klicka på **OK**.
6. Öppna filen App.config och Lägg till ett **appSettings** -avsnitt i filen. Ange de värden som krävs för att ansluta till Media Services API. Mer information finns i [få åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

    Ange de värden som behövs för att ansluta med hjälp av autentiseringsmetoden för **tjänstens huvud namn** .

    ```xml
    <configuration>
    ...
        <appSettings>
            <add key="AMSAADTenantDomain" value="tenant"/>
            <add key="AMSRESTAPIEndpoint" value="endpoint"/>
            <add key="AMSClientId" value="id"/>
            <add key="AMSClientSecret" value="secret"/>
        </appSettings>
    </configuration>
    ```

7. Lägg till referensen **System.Configuration** till ditt projekt.
8. Skriv över de befintliga **using** -instruktionerna i början av program.cs-filen med följande kod:

    ```csharp      
    using System;
    using System.Configuration;
    using System.IO;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Collections.Generic;
    using System.Linq;
    ```

    Nu är du redo att börja utveckla ett Media Services-program.    

## <a name="example"></a>Exempel

Här är ett litet exempel som ansluter till AMS-API: et och visar en lista över alla tillgängliga medie processorer.

```csharp
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
        
    private static CloudMediaContext _context = null;
    static void Main(string[] args)
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
        // List all available Media Processors
        foreach (var mp in _context.MediaProcessors)
        {
            Console.WriteLine(mp.Name);
        }
        
    }
 ```

## <a name="next-steps"></a>Nästa steg

Nu [kan du ansluta till AMS-API: et](media-services-use-aad-auth-to-access-ams-api.md) och börja [utveckla](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
