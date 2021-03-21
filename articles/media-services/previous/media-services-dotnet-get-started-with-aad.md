---
title: Använd Azure AD-autentisering för att komma åt Azure Media Services API med .NET | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Active Directory (Azure AD)-autentisering för att få åtkomst till Azure Media Services (AMS) API med .NET.
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
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: fd19bce05d18eb8f9b8fd3806082caeebe98bdad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014000"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Använd Azure AD-autentisering för att komma åt Azure Media Services API med .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Från och med windowsazure. Media Services 4.0.0.4 stöder Azure Media Services autentisering baserat på Azure Active Directory (Azure AD). Det här avsnittet visar hur du använder Azure AD-autentisering för att komma åt Azure Media Services API med Microsoft .NET.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Mer information finns i [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/).
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services konto med hjälp av Azure Portal](media-services-portal-create-account.md).
- Det senaste [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) -paketet.
- Hjälp med att [komma åt Azure Media Services API med Azure AD-autentisering – översikt](media-services-use-aad-auth-to-access-ams-api.md).

När du använder Azure AD-autentisering med Azure Media Services kan du autentisera på ett av två sätt:

- **Användarautentisering** autentiserar en person som använder appen för att interagera med Azure Media Services resurser. Det interaktiva programmet bör först uppmana användaren att ange autentiseringsuppgifter. Ett exempel är en hanterings konsol app som används av behöriga användare för att övervaka kodnings jobb eller direktsänd strömning.
- **Autentisering av tjänstens huvud namn** autentiserar en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, tjänster på mellan nivå eller schemalagda jobb, till exempel webbappar, Function-appar, Logic Apps, API: er eller mikrotjänster.

>[!IMPORTANT]
>Azure Media service stöder för närvarande en Azure Access Control Service Authentication-modell. Access Control auktorisering kommer dock att bli inaktuellt den 22 juni 2018. Vi rekommenderar att du migrerar till en Azure Active Directory autentiserings modell så snart som möjligt.

## <a name="get-an-azure-ad-access-token"></a>Hämta en Azure AD-åtkomsttoken

För att ansluta till Azure Media Services-API med Azure AD-autentisering måste klient appen begära en Azure AD-åtkomsttoken. När du använder Media Services .NET-klient-SDK är många av detaljerna för att hämta en Azure AD-åtkomsttoken omslutna och förenklade i klasserna [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) och [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) .

Du behöver till exempel inte ange Azure AD-auktoritet, Media Services resurs-URI eller ursprunglig information om Azure AD-program. Dessa är välkända värden som redan har kon figurer ATS av Azure AD Access token Provider-klassen.

Om du inte använder Azure Media service .NET SDK rekommenderar vi att du använder [Azure AD Authentication-biblioteket](../../active-directory/azuread-dev/active-directory-authentication-libraries.md). För att hämta värden för de parametrar som du behöver använda med Azure AD Authentication Library, se [använd Azure Portal för att komma åt Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md).

Du kan också välja att ersätta standard implementeringen av **AzureAdTokenProvider** med din egen implementering.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Installera och konfigurera Azure Media Services .NET SDK

>[!NOTE]
>Om du vill använda Azure AD-autentisering med Media Services .NET SDK måste du ha det senaste [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) -paketet. Lägg också till en referens till sammansättningen **Microsoft. IdentityModel. clients. ActiveDirectory** . Om du använder en befintlig app inkluderar du **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** sammansättningen.

1. Skapa ett nytt C#-konsolprogram i Visual Studio.
2. Använd NuGet-paketet [windowsazure. Media Services](https://www.nuget.org/packages/windowsazure.mediaservices) för att installera **Azure Media Services .NET SDK**.

    Utför följande steg för att lägga till referenser med NuGet: i **Solution Explorer** högerklickar du på projekt namnet och väljer sedan **Hantera NuGet-paket**. Sök sedan efter **windowsazure. Media Services** och välj **Installera**.

    \- eller -

    Kör följande kommando i **Package Manager-konsolen** i Visual Studio.

    ```console
    Install-Package windowsazure.mediaservices -Version 4.0.0.4
    ```

3. Lägg till **med** i käll koden.

    ```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    ```

## <a name="use-user-authentication"></a>Använd användarautentisering

För att ansluta till Azure Media Service-API med alternativet för användarautentisering måste klient programmet begära en Azure AD-token med hjälp av följande parametrar:

- Azure AD-klientens slut punkt. Klient informationen kan hämtas från Azure Portal. Hovra över den inloggade användaren i det övre högra hörnet.
- Media Services resurs-URI.
- Media Services (inbyggt) program klient-ID.
- Media Services (inbyggt) omdirigerings-URI.

Värdena för dessa parametrar finns i **AzureEnvironments. AzureCloudEnvironment**. Konstanten **AzureEnvironments. AzureCloudEnvironment** är en hjälp program i .NET SDK för att hämta rätt miljö variabel inställningar för ett offentligt Azure-datacenter.

Den innehåller i förväg definierade miljö inställningar för åtkomst till Media Services i offentliga data Center. Du kan använda **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** eller **AzureGermanCloudEnvironment** för statliga moln regioner.

Följande kod exempel skapar en token:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Om du vill starta programmering mot Media Services måste du skapa en **CloudMediaContext** -instans som representerar Server kontexten. **CloudMediaContext** innehåller referenser till viktiga samlingar, till exempel jobb, till gångar, filer, åtkomst principer och positionerare.

Du måste också skicka resurs- **URI för MEDIE rest-tjänster** till **CloudMediaContext** -konstruktorn. Om du vill hämta resurs-URI för medie REST-tjänster loggar du in på Azure Portal, väljer ditt Azure Media Services-konto, väljer **API-åtkomst** och väljer sedan **Anslut till Azure Media Services med användarautentisering**.

Följande kod exempel skapar en **CloudMediaContext** -instans:

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

I följande exempel visas hur du skapar Azure AD-token och kontexten:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }
        }
    }
}
```

>[!NOTE]
>Om du får ett undantag som säger "Fjärrservern returnerade ett fel: (401) obehörig," se avsnittet [åtkomst kontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control) för att komma åt Azure Media Services API med Azure AD-autentisering.

## <a name="use-service-principal-authentication"></a>Använd tjänstens huvud namns autentisering

För att ansluta till Azure Media Services-API med tjänstens huvud namn måste du begära en Azure AD-token med följande parametrar i mellannivåns app (webb-API eller webb program):

- Azure AD-klientens slut punkt. Klient informationen kan hämtas från Azure Portal. Hovra över den inloggade användaren i det övre högra hörnet.
- Media Services resurs-URI.
- Azure AD-program värden: **klient-ID** och **klient hemlighet**.

Värdena för parametrarna **klient-ID** och **klient hemlighet** hittar du i Azure Portal. Mer information finns i [komma igång med Azure AD-autentisering med hjälp av Azure Portal](media-services-portal-get-started-with-aad.md).

I följande kod exempel skapas en token med hjälp av **AzureAdTokenCredentials** -konstruktorn som tar **AzureAdClientSymmetricKey** som parameter:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                        AzureEnvironments.AzureCloudEnvironment);

var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Du kan också ange **AzureAdTokenCredentials** -konstruktorn som tar **AzureAdClientCertificate** som en parameter.

Instruktioner för hur du skapar och konfigurerar ett certifikat i ett formulär som kan användas av Azure AD finns i [autentisera till Azure AD i daemon-appar med certifikat – manuella konfigurations steg](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2).

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                        AzureEnvironments.AzureCloudEnvironment);
```

Om du vill starta programmering mot Media Services måste du skapa en **CloudMediaContext** -instans som representerar Server kontexten. Du måste också skicka resurs- **URI för MEDIE rest-tjänster** till **CloudMediaContext** -konstruktorn. Du kan också hämta **resurs-URI för media rest Services** -värdet från Azure Portal.

Följande kod exempel skapar en **CloudMediaContext** -instans:

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

I följande exempel visas hur du skapar Azure AD-token och kontexten:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                                        AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Kom igång med [att ladda upp filer till ditt konto](media-services-dotnet-upload-files.md).
