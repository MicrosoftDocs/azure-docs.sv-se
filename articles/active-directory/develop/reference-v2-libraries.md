---
title: Bibliotek för Microsoft Identity Platform-autentisering | Azure
description: Lista över klient bibliotek och mellanprogram som är kompatibla med Microsoft Identity Platform. Använd de här biblioteken för att lägga till stöd för användar inloggning (autentisering) och skyddat webb-API-åtkomst (auktorisering) till dina program.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 228a15e9e9e27cbcfd71d4762db2f4ab9f6dfffe
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560159"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotek för Microsoft Identity Platform-autentisering

Följande tabeller visar stöd för Microsoft Authentication Library för flera program typer. De innehåller länkar till bibliotekets källkod, där paketet för appens projekt hämtas, och om biblioteket stöder användar inloggning (autentisering), åtkomst till skyddade webb-API: er (auktorisering) eller både och.

Microsoft Identity Platform har certifierats av OpenID Foundation som en [certifierad OpenID-Provider](https://openid.net/certification/). Om du föredrar att använda ett annat bibliotek än Microsoft Authentication Library (MSAL) eller ett annat bibliotek som stöds av Microsoft väljer du ett med en [certifierad OpenID Connect-implementering](https://openid.net/developers/certified/).

Om du väljer att manuellt koda din egen protokoll nivå implementering av [OAuth 2,0 eller OpenID Connect 1,0](active-directory-v2-protocols.md), bör du gå noga med säkerhets aspekterna i varje Standards specifikation och följa en sdl-metod (Software Development Lifecycle) som [Microsoft sdl][Microsoft-SDL].

## <a name="single-page-application-spa"></a>SPA (Single-Side Application)

Ett program med en enda sida körs helt och hållet på webbläsarens yta och hämtar sid data (HTML, CSS och Java Script) dynamiskt eller vid programmets inläsnings tid. Den kan anropa webb-API: er för att interagera med Server dels data källor.

Eftersom en SPA-kod körs helt i webbläsaren, betraktas den som en *offentlig klient* som inte kan lagra hemligheter på ett säkert sätt.

| Språk/ramverk | Projekt på<br/>GitHub                                                                                                    | Paket                                                                      | Komma<br/>igång                             | Logga in användare                                         | Få åtkomst till webb-API: er                                                 | Allmänt tillgänglig (GA) *eller*<br/>Offentlig för hands version<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL-vinkel 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Offentlig för hands version                                               |
| Angular              | [MSAL-vinkel](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Självstudie](tutorial-v2-angular.md)              | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Offentlig för hands version                                               |
| JavaScript           | [MSAL.js 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Självstudie](tutorial-v2-javascript-auth-code.md) | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| JavaScript           | [MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)              | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)     | [Självstudie](tutorial-v2-javascript-spa.md) | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| React                | [MSAL-reagera](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Offentlig för hands version                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [kompletterande användnings villkor för Microsoft Azure för][preview-tos] hands versioner gäller för bibliotek i *offentlig för hands version*.

## <a name="web-application"></a>Webbprogram

Ett webb program kör kod på en server som genererar och skickar HTML, CSS och Java Script till en användares webbläsare som ska återges. Användarens identitet upprätthålls som en session mellan användarens webbläsare (klient delen) och webb servern (Server delen).

Eftersom ett webb programs kod körs på webb servern betraktas det som en *konfidentiell klient* som kan lagra hemligheter på ett säkert sätt.

| Språk/ramverk | Projekt på<br/>GitHub                                                                                     | Paket                                                                                                    | Komma<br/>igång                               | Logga in användare                                            | Få åtkomst till webb-API: er                                                    | Allmänt tillgänglig (GA) *eller*<br/>Offentlig för hands version<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y]    | Allmän tillgänglighet (GA)                                                           |
| ASP.NET Core         | [ASP.NET-säkerhet](/aspnet/core/security/)                                                                | [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![Bibliotek kan begära ID-token för användar inloggning.][y]    | ![Bibliotek kan inte begära åtkomsttoken för skyddade webb-API: er.][n] | Allmän tillgänglighet (GA)                                                           |
| ASP.NET Core         | [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![Bibliotek kan begära ID-token för användar inloggning.][y]    | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y]    | Allmän tillgänglighet (GA)                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Snabbstart](quickstart-v2-java-webapp.md)        | ![Bibliotek kan begära ID-token för användar inloggning.][y]    | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y]    | Allmän tillgänglighet (GA)                                                           |
| Node.js              | [MSAL-nod](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-nod](https://www.npmjs.com/package/@azure/msal-node)                                                | [Snabbstart](quickstart-v2-nodejs-webapp-msal.md) | ![Bibliotek kan begära ID-token för användar inloggning.][y]    | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y]    | Allmän tillgänglighet (GA)                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport – Azure-AD](https://www.npmjs.com/package/passport-azure-ad)                                       | [Snabbstart](quickstart-v2-nodejs-webapp.md)      | ![Bibliotek kan begära ID-token för användar inloggning.][y]    | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Python               | [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Snabbstart](quickstart-v2-python-webapp.md)      | ![Bibliotek kan begära ID-token för användar inloggning.][y]    | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y]    | Allmän tillgänglighet (GA)                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [kompletterande användnings villkor för Microsoft Azure för][preview-tos] hands versioner gäller för bibliotek i *offentlig för hands version*.

## <a name="desktop-application"></a>Skriv bords program

Ett Skriv bords program är vanligt vis binär (kompilerad) kod som utgör ett användar gränssnitt och är avsett att köras på en användares skriv bord.

Eftersom ett Skriv bords program körs på användarens skriv bord, betraktas det som en *offentlig klient* som inte kan lagra hemligheter på ett säkert sätt.

| Språk/ramverk | Projekt på<br/>GitHub                                                                                     | Paket                                                                               | Komma<br/>igång                        | Logga in användare                                         | Få åtkomst till webb-API: er                                                 | Allmänt tillgänglig (GA) *eller*<br/>Offentlig för hands version<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL-nod](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | [Självstudie](tutorial-v2-nodejs-desktop.md)   | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| macOS (SWIFT/OBJ-C)  | [MSAL för iOS och macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Självstudie](tutorial-v2-ios.md)             | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Självstudie](tutorial-v2-windows-uwp.md)     | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Självstudie](tutorial-v2-windows-desktop.md) | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [kompletterande användnings villkor för Microsoft Azure för][preview-tos] hands versioner gäller för bibliotek i *offentlig för hands version*.

## <a name="mobile-application"></a>Mobilprogram

Ett mobil program är vanligt vis binär (kompilerad) kod som utgör ett användar gränssnitt och är avsett att köras på en användares mobila enhet.

Eftersom ett mobil program körs på användarens mobila enhet anses det vara en *offentlig klient* som inte kan lagra hemligheter på ett säkert sätt.

| Plattform          | Projekt på<br/>GitHub                                                                          | Paket                                                                               | Komma<br/>igång                    | Logga in användare                                         | Få åtkomst till webb-API: er                                                 | Allmänt tillgänglig (GA) *eller*<br/>Offentlig för hands version<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Snabbstart](quickstart-v2-android.md) | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| iOS (SWIFT/OBJ-C) | [MSAL för iOS och macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Självstudie](tutorial-v2-ios.md)         | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Bibliotek kan begära ID-token för användar inloggning.][y] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [kompletterande användnings villkor för Microsoft Azure för][preview-tos] hands versioner gäller för bibliotek i *offentlig för hands version*.

## <a name="service--daemon"></a>Service/daemon

Tjänster och daemon används ofta för server-till-Server och annan obevakad kommunikation (kallas ibland för kommunikation *).* Eftersom det inte finns någon användare på tangent bordet för att ange autentiseringsuppgifter eller medgivande till resurs åtkomst, autentiseras dessa program som de ska, inte en användare, när de begär åtkomst till en webb-API-resurs.

En tjänst eller daemon som körs på en server betraktas som en *konfidentiell klient* som kan lagra sina hemligheter på ett säkert sätt.

| Språk/ramverk | Projekt på<br/>GitHub                                                                 | Paket                                                                                | Komma<br/>igång                           | Logga in användare                                            | Få åtkomst till webb-API: er                                                 | Allmänt tillgänglig (GA) *eller*<br/>Offentlig för hands version<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Snabbstart](quickstart-v2-netcore-daemon.md) | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)                                                           |
| Nod               | [MSAL-nod](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-nod](https://www.npmjs.com/package/@azure/msal-node)  | [Snabbstart](quickstart-v2-nodejs-console.md)  | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA)  |
| Python               | [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal – python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | —  | ![Bibliotek kan inte begära ID-token för användar inloggning.][n] | ![-Biblioteket kan begära åtkomsttoken för skyddade webb-API: er.][y] | Allmän tillgänglighet (GA) |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [kompletterande användnings villkor för Microsoft Azure för][preview-tos] hands versioner gäller för bibliotek i *offentlig för hands version*.

## <a name="next-steps"></a>Nästa steg

Mer information om Microsoft Authentication Library finns i [Översikt över Microsoft Authentication Library (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
