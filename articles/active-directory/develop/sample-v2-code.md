---
title: Kod exempel för Microsoft Identity Platform
description: Innehåller ett index över tillgängliga kod exempel för Microsoft Identity Platform, ordnat efter scenario.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dc740acb366212640015ca0fb527d34fade1097d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010770"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Kod exempel för Microsoft Identity Platform (v 2.0-slut punkt)

Du kan använda Microsoft Identity Platform för att:

- Lägg till autentisering och auktorisering för dina webb program och webb-API: er.
- Kräv en åtkomsttoken för att få åtkomst till ett skyddat webb-API.

Den här artikeln beskriver och tillhandahåller länkar till exempel för Microsoft Identity Platform. De här exemplen visar hur det är färdigt och innehåller även kodfragment som du kan använda i dina program. På sidan kod exempel hittar du detaljerade README-avsnitt som hjälper dig med krav, installation och konfiguration. Kommentarer i koden hjälper dig att förstå de kritiska avsnitten.

Information om de grundläggande scenarierna för varje exempel typ finns i [app types för Microsoft Identity Platform](v2-app-types.md).

Du kan också bidra till exemplen på GitHub. Mer information finns i [Microsoft Azure Active Directory exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Program med en enda sida

De här exemplen visar hur du skriver ett program med en enda sida som skyddas med Microsoft Identity Platform. I de här exemplen används en varianter av MSAL.js.

| Plattform | Beskrivning | Länk |
| -------- | --------------------- | -------- |
| ![Den här bilden visar JavaScript-filens ](media/sample-v2-code/logo_js.png) [JavaScript-skript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-anrop Microsoft Graph |[Java Script-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Den här bilden visar JavaScript-filens ](media/sample-v2-code/logo_js.png) [JavaScript-skript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA-anrop Microsoft Graph att använda auth Code Flow med PKCE |[Java Script-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Den här bilden visar JavaScript-filens ](media/sample-v2-code/logo_js.png) [JavaScript-skript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-anrop B2C |[B2C – Java Script-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Den här bilden visar JavaScript-filens ](media/sample-v2-code/logo_js.png) [JavaScript-skript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA-anrop B2C med auth Code Flow w/PKCE |[B2C – Java Script – Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![Den här bilden visar JavaScript-filens ](media/sample-v2-code/logo_js.png) [JavaScript-skript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA anropar anpassat webb-API som i sin tur anropar Microsoft Graph  | [MS-Identity-JavaScript-självstudie-chapter4-OBO](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![Den här bilden visar vinkel typ ](media/sample-v2-code/logo_angular.png) [vinkeln (MSAL vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA-anrop Microsoft Graph  | [Active-Directory-JavaScript-singlepageapp-vinkel](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Den här bilden visar vinkel typ ](media/sample-v2-code/logo_angular.png) [vinkeln (MSAL vinkel 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA-anrop Microsoft Graph att använda auth Code Flow med PKCE | [MS-Identity-JavaScript-vinkel-Spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![Den här bilden visar vinkel typ ](media/sample-v2-code/logo_angular.png) [vinkeln (MSAL vinkel 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA-anrop anpassad webb-API | [MS-Identity-JavaScript-vinkel-Spa-aspnetcore-WebAPI](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Den här bilden visar vinkel typ ](media/sample-v2-code/logo_angular.png) [vinkeln (MSAL vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA-anrop B2C |[Active-Directory-B2C-JavaScript-vinkel-Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Den här bilden visar vinkel typ ](media/sample-v2-code/logo_angular.png) [vinkeln (MSAL vinkel 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA anropar anpassat webb-API med app-roller och säkerhets grupper |[MS-Identity-JavaScript-vinkel-Spa-dotnetcore-WebAPI-roles-Groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![Den här bilden visar att den reagerar på logo typen ](media/sample-v2-code/logo_react.png) [reagerar (MSAL reagerar)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA-anrop Microsoft Graph att använda auth Code Flow med PKCE | [MS-Identity-JavaScript-reakta-Spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![Den här bilden visar att den reagerar på logo typen ](media/sample-v2-code/logo_react.png) [reagerar (MSAL reagerar)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA-anrop anpassad webb-API | [MS-Identity-JavaScript-preakta – självstudie](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api) |
| ![Den här bilden visar att den reagera på logo typen ](media/sample-v2-code/logo_react.png) [reagerar (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA anropar anpassat webb-API som i sin tur anropar Microsoft Graph  | [MS-Identity-JavaScript-reakta-Spa-dotnetcore-WebAPI-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Den här bilden visar ](media/sample-v2-code/logo-blazor.png) [WebAssembly-webbsammansättningen](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) från blixtens logo typ (MSAL.js) | Själv studie kurs om blixt websammansättning för inloggnings användare och anropar API: er med Azure Active Directory |[MS-Identity-blixte-wasm](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>Webbprogram

Följande exempel illustrerar webb program som loggar in användare. Några exempel visar också programmet som anropar Microsoft Graph eller ditt eget webb-API med användarens identitet.

| Plattform | Endast tecken i användare | Loggar in användare och anropar Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Själv studie kurs om ASP.NET Core WebApp-loggar – användare](https://aka.ms/aspnetcore-webapp-sign-in) | Samma exempel i [ASP.net Core webbappens anrop Microsoft Graph-](https://aka.ms/aspnetcore-webapp-call-msgraph) fasen</p>Avancerat exempel [som använder den inloggade användarens token-cache från huvudappar, API: er och tjänster](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) |
| ![Den här bilden visar ASP.NET Framework-logotypen](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [AD FS till Spelbok för Azure AD-programmigrering för utvecklare](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) för att lära sig att säkert och säkert migrera dina program som är integrerade med Active Directory Federation Services (AD FS) (AD FS) till Azure Active Directory (Azure AD) | |
| ![Den här bilden visar ASP.NET Framework-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Snabb start för ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotNet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotNet-admin-restricted-scope – v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph – utbildning – aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  |[Själv studie kurs om Java-servlet – kapitel 1,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Logga in med AAD| |
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  |[Själv studie kurs om Java-servlet – kapitel 1,2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Logga in med B2C |
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  | | [Själv studie kurs om Java-servlet – kapitel 2,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Logga in med AAD och ring diagram|
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  |[Själv studie kurs om Java-servlet – kapitel 3,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Logga in med AAD och kontrol lera åtkomst med roll anspråk| |
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  | | [Själv studie kurs om Java-servlet – kapitel 3,2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Logga in med AAD och kontrol lera åtkomst med grupp anspråk|
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | |[Själv studie kurs om Java-servlet – kapitel 4,1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Distribuera till Azure App Service|
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  | | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  | [MS-Identity-B2C-Java-servlet-webapp-Authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Användar handbok för Express Web App-inloggningar](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | [Självstudie om python-mätkolv – kapitel 1,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Logga in med AAD  |  |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | [Självstudie om python-mätkolv – kapitel 1,2](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Logga in med B2C                    |  |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | | [Självstudie om python-mätkolv – kapitel 2,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Logga in med AAD och ring diagram |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | |[Självstudie om python-mätkolv – kapitel 3,1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Distribuera till Azure App Service  |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | [Självstudie om python-django – kapitel 1,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)   Logga in med AAD  | |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | [Självstudie om python-django – kapitel 1,2](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Logga in med B2C                    |  |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | | [Självstudie om python-django – kapitel 2,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)  Logga in med AAD och ring diagram|
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | | [Självstudie om python-django – kapitel 3,1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Distribuera till Azure App Service                    |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  | | [Python Flask-webbapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Den här bilden visar ruby-logotypen](media/sample-v2-code/logo_ruby.png) |                   | [msgraph – utbildning – rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![Den här bilden visar den snabba logo typen](media/sample-v2-code/logo-blazor.png)</p>Blazor Server | [Själv studie kurs om hur du loggar in med programmet för blixt](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [Program anrop till programmet för blixt Server Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Själv studie kurs [om Chapterwise: programmet för att logga in användare och anropa API: er med Azure Active Directory](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop och mobila offentliga klient program

I följande exempel visas offentliga klient program (Station ära eller mobila program) som har åtkomst till Microsoft Graph-API: et eller ditt eget webb-API i namnet på en användare. Förutom *Skriv bordet (konsolen) med ett WAM* -exempel använder alla dessa klient program Microsoft Authentication Library (MSAL).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph | Anropar ett webb-API för ASP.NET Core |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Skriv bords självstudie (.NET Core) – du kan välja att använda:</p>– cacheminnet för mellanplattforms-token</p>– anpassat webb gränssnitt | ![Den här bilden visar .NET/C#-logo typen](media/sample-v2-code/logo_NETcore.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code)| [MS-Identity-dotNet-Desktop – självstudie](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| Desktop (WPF)      | ![Den här bilden visar logo typen .NET Desktop/C#](media/sample-v2-code/logo_NET.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code)| [dotNet-Desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Skriv bord (konsol)   | ![Bild som visar logo typen .NET/C# (skriv bord)](media/sample-v2-code/logo_NET.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) | [dotNet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Skriv bord (konsol)   | ![Detta är .NET/C#-logo typen (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Användar namn/lösen ord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Skriv bord (konsol) med WAM  | ![Detta är logo typen för .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Interaktiv med [Web Account Manager](/windows/uwp/security/web-account-manager) (WAM) |[dotNet-Native-UWP-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Användar namn/lösen ord](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Skriv bord (konsol)   | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Användar namn/lösen ord](msal-authentication-flows.md#usernamepassword) |[MS-Identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Skriv bord (Electron)   | ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Auktoriseringskod (PKCE)](msal-authentication-flows.md#authorization-code) |[MS-Identity-Java Script-NodeJS-Desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET/C#-logo typen (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |[Xamarin – inbyggt-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![Den här bilden visar iOS/mål-C eller Swift](media/sample-v2-code/logo_iOS.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |[iOS – Swift-objc-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS – Native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |[macOS-Swift-objc-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobil (Android-Java)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |  [Android – Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android-Kotlin)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |  [Android – Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon-program

I följande exempel visas ett program som har åtkomst till Microsoft Graph-API med en egen identitet (utan användare).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsol | ![Den här bilden visar .NET Core-logotypen](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webbapp | ![Skärm bild som visar ASP.NET-logotypen.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [Dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konsol | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konsol | ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node)| [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java Script-NodeJS-Console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| Konsol | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [MS-Identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Konsol löst program

I följande exempel visas ett offentligt klient program som körs på en enhet utan en webbläsare. Appen kan vara ett kommando rads verktyg, en app som körs på Linux eller Mac eller ett IoT-program. Exemplet innehåller en app som använder Microsoft Graph API, i namnet på en användare som loggar in interaktivt på en annan enhet (till exempel en mobil telefon). Det här klient programmet använder Microsoft Authentication Library (MSAL).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Skriv bord (konsol)   | ![Den här bilden visar logo typen .NET/C# (skriv bord)](media/sample-v2-code/logo_NETcore.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Skriv bord (konsol)   | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[MS-Identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>SaaS-program för flera innehavare

Följande exempel visar hur du konfigurerar programmet så att det accepterar inloggningar från valfri Azure Active Directory (Azure AD)-klient. Att konfigurera ditt program så att det blir *flera klienter* innebär att du kan erbjuda ett SaaS-program ( **program vara som en tjänst** ) till många organisationer, så att användarna kan logga in till ditt program efter att de har gett sitt medgivande.

| Plattform | Beskrivning | Länk |
| -------- | --------------------- | -------- |
| ![Den här bilden visar vinkel typ ](media/sample-v2-code/logo_angular.png) [vinkeln (MSAL vinkel 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA-anrop för flera innehavare Graph API |[MS-Identity-JavaScript-vinkel-Spa-ASPNET-ASPNET-WebAPI-flera innehavare](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Den här bilden visar vinkel typ ](media/sample-v2-code/logo_angular.png) [vinkeln (MSAL vinkel 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA-anrop för flera innehavare med anpassat webb-API |[MS-Identity-JavaScript-vinkel-Spa-ASPNET-ASPNET-WebAPI-flera innehavare](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Den här bilden visar ASP.NET Core ](media/sample-v2-code/logo_NETcore.png) [-logotypen .net Core (MSAL.net)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC-webbprogram-anrop Graph API |[Active-Directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Den här bilden visar ASP.NET Core ](media/sample-v2-code/logo_NETcore.png) [-logotypen .net Core (MSAL.net)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC-webbprogram-anrop ASP.NET Core webb-API |[Active-Directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Webb-API:er

Följande exempel visar hur du skyddar ett webb-API med Microsoft Identity Platform och hur du anropar ett underordnat API från webb-API: et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web API (Service) för [dotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webb-API (tjänst) för [MS-Identity-ASPNET-WebAPI-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | Webb-API (Service) för [MS-Identity-Java-WebAPI](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Webb-API (tjänst) för [Active-Directory-JavaScript-NodeJS-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| B2C Web API (Service) för [Active-Directory-B2C-JavaScript-NodeJS-WebAPI](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions som webb-API: er

Följande exempel visar hur du skyddar en Azure-funktion med HttpTrigger och exponerar ett webb-API med Microsoft Identity Platform och hur du anropar ett underordnat API från webb-API: et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web API (Service) Azure Function i [dotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)</p>Python | Webb-API (Service) för [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Webb-API (tjänst) för [Node.js och Passport – Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Webb-API (Service) för [Node.js och Passport-Azure-AD som använder på uppdrag av](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph exempel

Mer information om [exempel](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) och självstudier som demonstrerar olika användnings mönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community-exempel & självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se även

[Microsoft Graph API-konceptuell och referens](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
