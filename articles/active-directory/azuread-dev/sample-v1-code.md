---
title: Kod exempel för Azure Active Directory v 1.0 | Microsoft Docs
description: Innehåller ett index för kod exempel för Azure Active Directory (v 1.0-slutpunkt) som organiseras efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: sample
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 662a0f53a5d5e8e02ef3b29640009fd07bf4f1d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652152"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory kod exempel (v 1.0-slut punkt)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Du kan använda Microsoft Azure Active Directory (Azure AD) för att lägga till autentisering och auktorisering för dina webb program och webb-API: er.

Det här avsnittet innehåller länkar till exempel som du kan använda för att lära dig mer om Azure AD v 1.0-slutpunkten. De här exemplen visar hur det sker tillsammans med kodfragment som du kan använda i dina program. På sidan kod exempel hittar du detaljerade Read-Me-avsnitt som hjälper dig med krav, installation och konfiguration. Och koden kommenteras för att hjälpa dig att förstå de kritiska avsnitten.

> [!NOTE]
> Om du är intresse rad av Azure AD v2-kod exempel, se [v 2.0 kod exempel efter scenario](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Information om de grundläggande scenarierna för varje typ av exempel finns i [autentiserings scenarier för Azure AD](v1-authentication-scenarios.md).

Du kan också bidra till våra exempel på GitHub. Mer information finns i [Microsoft Azure Active Directory exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Program med en enda sida

Det här exemplet visar hur du skriver ett program med en enda sida som skyddas med Azure AD.

| Plattform | Anropar sitt eget API | Anropar ett annat webb-API |
|--|--|--|
| ![Den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo-js.png) | [Java Script – singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
| ![Den här bilden visar en vinkel JS-logotyp](media/sample-v2-code/logo-angular.png) | [AngularJS – singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [AngularJS-singlepageapp-CORS](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) |

## <a name="web-applications"></a>Webb program

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webb program som loggar in användare, anropar Microsoft Graph eller ett webb-API med användarens identitet

I följande exempel visas webb program som signerar användare. Några av dessa program anropar också Microsoft Graph eller ditt eget webb-API i namnet på den inloggade användaren.

| Plattform | Endast tecken i användare | Anrop Microsoft Graph | Anropar en annan ASP.NET eller ASP.NET Core 2,0 webb-API |
|--|--|--|--|
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2,0 | [dotNet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-WebAPI-multiinnehavare-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD-diagram) | [dotNet-webapp-WebAPI-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore) |
| ![Den här bilden visar ASP.NET Framework-logotypen](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotNet-webapp-WebAPI-OAuth2-UserIdentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotNet-webapp-flera innehavare – openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD-diagram) |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo-python.png) |  | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |
| ![Den här bilden visar Java-loggen](media/sample-v2-code/logo-java.png) |  | [Java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect) |
| ![Den här bilden visar PHP-logotypen](media/sample-v2-code/logo-php.png) |  | [php-graphapi – webb](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webb program som demonstrerar rollbaserad åtkomst kontroll (auktorisering)

Följande exempel visar hur du implementerar rollbaserad åtkomst kontroll (RBAC). RBAC används för att begränsa behörigheterna för vissa funktioner i ett webb program till vissa användare. Användarna är auktoriserade beroende på om de tillhör en **Azure AD-grupp** eller har en specifik program **roll**.

| Plattform | Exempel |
|--|--|
| ![Den här bilden visar ASP.NET Framework-logotypen](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotNet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotNet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | En .NET 4,5 MVC-webbapp som använder Azure AD- **roller** för auktorisering |

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Station ära och mobila klient program som anropar Microsoft Graph eller ett webb-API

Följande exempel illustrerar offentliga klient program (Station ära/mobila program) som har åtkomst till Microsoft Graph eller ett webb-API i namnet på en användare. Beroende på enheterna och plattformarna kan program logga in användare på olika sätt (flöden/bidrag):

- Interaktivt
- Tyst (med integrerad Windows-autentisering på Windows, eller användar namn/lösen ord)
- Genom att delegera interaktiv inloggning till en annan enhet (enhets kod flöde som används på enheter som inte tillhandahåller webb kontroller)

Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph | Anropar ett ASP.NET-eller ASP.NET Core 2. x-webb-API
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![Den här bilden visar .NET/C#-logo typen](media/sample-v2-code/logo-net.png)  | Interaktiv | Del av [dotNet-internt-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet – inbyggd – skriv bord](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotNet-Native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotNet-WebAPI-manuell-JWT-Validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobil (UWP)            | .![Den här bilden visar .NET/C#-/UWP](media/sample-v2-code/logo-windows.png)   | Interaktiv | [dotNet-Native-UWP-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Det här exemplet använder [WAM](/windows/uwp/security/web-account-manager), inte [ADAL.net](https://aka.ms/adalnet)|  [dotNet-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-program som använder ADAL.net för att anropa en enda klient webb-API) </p> [dotNet-WebAPI-multiinnehavare-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-program med ADAL.net för att anropa en webb-API för flera innehavare)|
Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interaktiv | [dotNet-internt-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobil (Android)           | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo-android.png) | Interaktiv |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobil (iOS)           | ![Den här bilden visar iOS/mål C eller Swift](media/sample-v2-code/logo-ios.png) | Interaktiv |   [nativeClient – iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Skriv bord (konsol)          | ![Den här bilden visar .NET/C#-logo typen](media/sample-v2-code/logo-net.png) | Användar namn/lösen ord </p>  Integrerad Windows-autentisering | | [dotNet-automatisk-konsol](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Skriv bord (konsol)          | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo-java.png) | Användar namn/lösen ord | | [Java – inbyggd](https://github.com/Azure-Samples/active-directory-java-native-headless)
Skriv bord (konsol)           | ![Den här bilden visar .NET Core/C#-logo typen](media/sample-v2-code/logo-netcore.png) | Enhets kod flöde | | [dotNet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-program (åtkomst till webb-API: er med programmets identitet)

I följande exempel visas Skriv bords-eller webb program som har åtkomst till Microsoft Graph eller ett webb-API utan användare (med program identiteten).

Klient program | Plattform | Flöde/tilldelning | Anropar en ASP.NET-eller ASP.NET Core 2,0-webb-API
------------------ | -------- | ---------- | -------------------- 
Daemon-app (konsol)          | ![Den här bilden visar .NET Framework logo typ](media/sample-v2-code/logo-netframework.png) | Klientautentiseringsuppgifter med appens hemlighet eller certifikat | [Dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [Dotnet-daemon-Certificate Credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon-app (konsol)         | ![Den här bilden visar .NET Core-logotypen](media/sample-v2-code/logo-netcore.png) | Klientautentiseringsuppgifter med certifikat| [dotnetcore-daemon-certifikat-Credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET-webbapp  | ![Den här bilden visar .NET Framework logo typ](media/sample-v2-code/logo-netframework.png) | Klientautentiseringsuppgifter | [dotNet-webapp-WebAPI-OAuth2-APPIDENTITY](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webb-API:er

### <a name="web-api-protected-by-azure-active-directory"></a>Webb-API som skyddas av Azure Active Directory

Följande exempel visar hur du skyddar ett node.js webb-API med Azure AD.

I föregående avsnitt i den här artikeln kan du också hitta andra exempel som illustrerar ett klient program som **anropar** ett ASP.net **-eller ASP.net Core-webb-API**. Dessa exempel nämns inte igen i det här avsnittet, men du hittar dem i den sista kolumnen i tabellerna ovan eller nedanför

| Plattform | Exempel |
|--------|-------------------|
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo-nodejs.png)  | [Node-WebAPI](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webb-API som anropar Microsoft Graph eller något annat webb-API

Följande exempel visar ett webb-API som anropar ett annat webb-API. Det andra exemplet visar hur du hanterar villkorlig åtkomst.

| Plattform |  Anrop Microsoft Graph | Anropar en annan ASP.NET eller ASP.NET Core 2,0 webb-API |
| -------- |  --------------------- | ------------------------- |
| ![Den här bilden visar ASP.NET Framework-logotypen](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotNet-WebAPI-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotNet-WebAPI-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotNet-WebAPI-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotNet-WebAPI-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph exempel

Exempel och självstudier som demonstrerar olika användnings mönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph community-exempel & självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se även

- [Guide för Azure Active Directory utvecklare](v1-overview.md)
- [Azure Active Directory autentiseringsscheman](active-directory-authentication-libraries.md)
- [Microsoft Graph API-konceptuell och referens](/graph/use-the-api)