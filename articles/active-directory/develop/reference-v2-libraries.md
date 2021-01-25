---
title: Bibliotek för Microsoft Identity Platform-autentisering
description: Kompatibla klient bibliotek och Server mellan bibliotek, tillsammans med relaterade bibliotek, käll-och exempel länkar för Microsoft Identity Platform.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 51b60d7b81d7402f69415b79cd575f51915dc38f
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756658"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotek för Microsoft Identity Platform-autentisering

[Microsoft Identity Platform](../azuread-dev/azure-ad-endpoint-comparison.md) stöder OAuth 2,0-och OpenID Connect 1,0-protokoll som är bransch standard. Microsoft Authentication Library (MSAL) är utformat för att fungera med Microsoft Identity Platform. Du kan också använda bibliotek med öppen källkod som har stöd för OAuth 2,0 och OpenID Connect 1,0.

Vi rekommenderar att du använder bibliotek som skrivits av protokoll domän experter som följer en SDL-metod (Security Development Lifecycle). Dessa metoder omfattar [det som Microsoft följer][Microsoft-SDL]. Om du får en kod för protokollen bör du följa en metod som Microsoft SDL. Var noga med säkerhets aspekterna i specifikationerna för varje protokoll.

> [!NOTE]
> Letar du efter ADAL (Azure Active Directory Authentication Library)? Kolla in [biblioteks guiden för ADAL](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typer av bibliotek

Microsoft Identity Platform fungerar med två typer av bibliotek:

* **Klient bibliotek**: interna klienter och servrar använder klient bibliotek för att hämta åtkomsttoken för att anropa en resurs, till exempel Microsoft Graph.
* **Server mellan bibliotek**: webbappar använder serverprogram bibliotek för användar inloggning. Webb-API: er använder Server mellanprogram bibliotek för att verifiera token som skickas av interna klienter eller andra servrar.

## <a name="library-support"></a>Biblioteks stöd

Biblioteken ingår i två support kategorier:

* **Microsoft-Support**: Microsoft tillhandahåller korrigeringar för dessa bibliotek och har utfört sdl-noggrannhet på dessa bibliotek.
* **Kompatibel**: Microsoft har testat dessa bibliotek i grundläggande scenarier och har bekräftat att de fungerar med Microsoft Identity Platform. Microsoft tillhandahåller inte några korrigeringar för dessa bibliotek och har inte genomfört en granskning av dessa bibliotek. Problem och funktions begär Anden ska dirigeras till bibliotekets projekt med öppen källkod.

En lista över bibliotek som fungerar med Microsoft Identity Platform finns i följande avsnitt.

## <a name="microsoft-supported-client-libraries"></a>Klient bibliotek som stöds av Microsoft

Använd bibliotek för klientautentisering för att hämta en token för att anropa ett skyddat webb-API.

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens | Konceptuellt dokument | Översikt |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Ensidesapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referens](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Konceptuella dokument](msal-overview.md)| [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL-vinkel | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Vinkel SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Referens](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Konceptuella dokument](msal-overview.md) | [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Skrivbordsapp](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview&preserve-view=true) |[Konceptuella dokument](msal-overview.md) | [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![.NET Core-ikon](media/sample-v2-code/logo_NETCore.png) | Microsoft Identity Web  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Web) |[GitHub](https://github.com/AzureAD/microsoft-identity-web) | [Exempel](https://aka.ms/ms-id-web/samples) | [Microsoft. Identity. Web](/dotnet/api/microsoft.identity.web?view=azure-dotnet-preview&preserve-view=true) |[Konceptuella dokument](https://aka.ms/ms-id-web/conceptual-doc) | [Översikt](https://github.com/AzureAD/microsoft-identity-web/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Exempel](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://search.maven.org/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Referens](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS och macOS | MSAL iOS och macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-app](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [MacOS-app](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Referens](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Konceptuella dokument](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Central lagrings plats](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-app](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Konceptuella dokument](msal-overview.md) |[Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft-servrar som stöds av mellanprogram

Använd mellanprogram bibliotek för att skydda webb program och webb-API: er. Webbappar eller webb-API: er skrivna med ASP.NET eller ASP.NET Core använda mellanprogram biblioteken.

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET-säkerhet |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC-app](quickstart-v2-aspnet-webapp.md) |[ASP.NET API-referens](/dotnet/api/?view=aspnetcore-2.0&preserve-view=true) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel-tillägg för .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-app](quickstart-v2-aspnet-webapp.md) |[Referens](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet&preserve-view=true) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webbapp](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Microsoft-bibliotek som stöds av OS/språk

Vid stöd för operativ system vs-språk, är mappningen följande:

| Plattform    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET VB, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Xamarin. iOS) | MSAL.Net (Xamarin. Android)|
| Swift <br> Objective-C |            |            | [MSAL för iOS och macOS](msal-overview.md) | [MSAL för iOS och macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL python | MSAL python | MSAL python |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.js | Passport. Node | Passport. Node | Passport. Node |

Se även [scenarier av plattformar och språk som stöds](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Kompatibla klient bibliotek

| Plattform | Biblioteksnamn | Testad version | Källkod | Exempel |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Version 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[Vue-MSAL](https://github.com/mvertopoulos/vue-msal) | Version 3.0.3 |[Vue – msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Java](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect-bibliotek](https://github.com/GluuFederation/oxAuth) | [Version 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect-bibliotek](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Begär Anden – OAuthlib](https://github.com/requests/requests-oauthlib) | [Version 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Begär Anden – OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [OpenID-klient](https://github.com/panva/node-openid-client) | [Version 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [OpenID-klient](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP-OAuth2 – klient](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [OAuth2-klient](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-OAuth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reagera på inbyggd app-autentisering](https://github.com/FormidableLabs/react-native-app-auth) | [Version 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagera på inbyggd app-autentisering](https://github.com/FormidableLabs/react-native-app-auth) | |

För alla standarder-kompatibla bibliotek kan du använda Microsoft Identity Platform. Det är viktigt att veta var du ska gå till supporten:

* Kontakta bibliotekets ägare för problem och nya funktions begär anden i biblioteks kod.
* Kontakta Microsoft om du vill ha problem och nya funktions begär anden i implementeringen på tjänst sidan.
* [Fil en funktions förfrågan](https://feedback.azure.com/forums/169401-azure-active-directory) för ytterligare funktioner som du vill se i protokollet.
* [Skapa en support förfrågan](../../azure-portal/supportability/how-to-create-azure-support-request.md) om du hittar ett problem där Microsoft Identity Platform inte är kompatibel med OAuth 2,0 eller OpenID Connect 1,0.

## <a name="related-content"></a>Relaterat innehåll

Mer information om Microsoft Identity Platform finns i [Översikt över Microsoft Identity Platform][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
