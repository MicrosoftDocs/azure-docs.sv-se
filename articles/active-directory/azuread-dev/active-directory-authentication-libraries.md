---
title: Azure Active Directory autentiseringsscheman | Microsoft Docs
description: Med Azure AD Authentication Library (ADAL) kan klient program utvecklare enkelt autentisera användare till molnet eller lokala Active Directory (AD) och sedan hämta åtkomsttoken för att skydda API-anrop.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: reference
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7b6ffd885e1be2dd59cea87cacd6e5fd5e0f8f49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861178"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication Library-bibliotek

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory Authentication Library (ADAL) v 1.0 gör det möjligt för programutvecklare att autentisera användare till molnet eller lokalt Active Directory (AD) och hämta token för att skydda API-anrop. ADAL gör autentisering enklare för utvecklare genom funktioner som:

- Konfigurerbart token-cache som lagrar åtkomsttoken och uppdaterade tokens
- Automatisk uppdatering av token när en åtkomsttoken upphör att gälla och en uppdateringstoken är tillgänglig
- Stöd för asynkrona metod anrop

> [!NOTE]
> Letar du efter Azure AD v 2.0-biblioteken (MSAL)? Checka ut [biblioteks guiden för MSAL](../develop/reference-v2-libraries.md).
>
>

## <a name="microsoft-supported-client-libraries"></a>Klient bibliotek som stöds av Microsoft

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET-klient, Windows Store, UWP, Xamarin iOS och Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Skrivbordsapp](../develop/quickstart-v2-windows-desktop.md) |[Referens](/dotnet/api/microsoft.identitymodel.clients.activedirectory) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Ensidesapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-app](../develop/quickstart-v2-ios.md) | [Referens](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-app](../develop/quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js-webbapp](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referens](/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java-webbapp](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referens](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python-webbapp](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referens](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Microsoft-Server bibliotek som stöds

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN för AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[MVC-app](../develop/quickstart-v2-aspnet-webapp.md) | |
| .NET |OWIN för OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Webbapp](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN för WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[MVC-webbapp](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Identitets protokolls tillägg för .NET 4,5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT-hanterare för .NET 4,5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webb-API](../develop/authentication-flows-app-scenarios.md)| |

## <a name="scenarios"></a>Scenarier

Här är tre vanliga scenarier för att använda ADAL i en klient som ansluter till en fjärran sluten resurs:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autentisera användare av ett internt klient program som körs på en enhet

I det här scenariot har en utvecklare en mobil klient eller ett Skriv bords program som behöver åtkomst till en fjär resurs, till exempel ett webb-API. Webb-API: et tillåter inte anonyma anrop och måste anropas i kontexten för en autentiserad användare. Webb-API: et är förkonfigurerat för att lita på åtkomsttoken som utfärdats av en särskild Azure AD-klient. Azure AD är förkonfigurerat för att utfärda åtkomsttoken för resursen. För att anropa webb-API: et från klienten använder utvecklaren ADAL för att under lätta autentisering med Azure AD. Det säkraste sättet att använda ADAL är att låta det återge användar gränssnittet för att samla in användarautentiseringsuppgifter (återges som webbläsarfönstret).

ADAL gör det enkelt att autentisera användaren, skaffa en åtkomsttoken och uppdatera token från Azure AD och sedan anropa webb-API: et med hjälp av åtkomsttoken.

Ett kod exempel som visar det här scenariot med autentisering i Azure AD finns i [Native-klientens WPF-program till webb-API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autentisering av ett konfidentiellt klient program som körs på en webb server

I det här scenariot har en utvecklare ett program som körs på en server som behöver åtkomst till en fjär resurs, till exempel ett webb-API. Webb-API: et tillåter inte anonyma anrop, så det måste anropas från en auktoriserad tjänst. Webb-API: et är förkonfigurerat för att lita på åtkomsttoken som utfärdats av en särskild Azure AD-klient. Azure AD är förkonfigurerat för att utfärda åtkomsttoken för resursen till en tjänst med klientautentiseringsuppgifterna (klient-ID och hemlighet). ADAL underlättar autentiseringen av tjänsten med Azure AD och returnerar en åtkomsttoken som kan användas för att anropa webb-API: et. ADAL hanterar också hanteringen av åtkomsttokens livs längd genom att cachelagra den och förnya den vid behov. Ett kod exempel som visar det här scenariot finns i [daemon-konsolens program till webb-API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autentisering av ett konfidentiellt klient program som körs på en server, å en användares vägnar

I det här scenariot har en utvecklare ett webb program som körs på en server som behöver åtkomst till en fjär resurs, till exempel ett webb-API. Webb-API: et tillåter inte anonyma anrop, så det måste anropas från en auktoriserad tjänst åt en autentiserad användare. Webb-API: et är förkonfigurerat för att lita på åtkomst till token som utfärdats av en särskild Azure AD-klient, och Azure AD är förkonfigurerat för att utfärda åtkomsttoken för resursen till en tjänst med klientautentiseringsuppgifterna. När användaren har autentiserats i webb programmet kan programmet hämta en auktoriseringskod för användaren från Azure AD. Webb programmet kan sedan använda ADAL för att hämta en åtkomsttoken och uppdatera token för en användares räkning med hjälp av den auktoriseringskod och de klientautentiseringsuppgifter som är kopplade till programmet från Azure AD. När webb programmet har till gång till åtkomsttoken kan den anropa webb-API: et tills token upphör att gälla. När token går ut kan webb programmet använda ADAL för att hämta en ny åtkomsttoken genom att använda den uppdateringstoken som togs emot tidigare. Ett kod exempel som visar det här scenariot finns i [intern klient-till-webb-API till webb-API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Se även

- [Guide för Azure Active Directory utvecklare](v1-overview.md)
- [Autentiserings scenarier för Azure Active Directory](v1-authentication-scenarios.md)
- [Azure Active Directory kod exempel](sample-v1-code.md)
