---
title: Azure Active Directory B2C-kodexempel | Microsoft Docs
description: Kodexempel för Azure Active Directory B2C-appar för mobil, skrivbord och enskild sida.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b09587d90024a8c376be8b0d93f7ef7b6cc51a1e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008492"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C-kodexempel

Följande tabeller innehåller länkar till exempel för program, inklusive iOS, Android, .NET och Node.js.

## <a name="mobile-and-desktop-apps"></a>Mobilappar och skrivbordsappar

| Exempel | Beskrivning |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Ett iOS-exempel i Swift som autentiserar Azure AD B2C-användare och anropar ett API med OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | En enkel Android-app som visar hur du använder MSAL för att autentisera användare via Azure Active Directory B2C och få åtkomst till ett webb-API med resulterande token. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Ett exempel som visar hur du kan använda ett bibliotek från tredje part för att bygga ett iOS-program i mål-C som autentiserar Microsoft Identity-användare till vår Azure AD B2C identitets tjänst. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Ett exempel som visar hur du kan använda ett bibliotek från tredje part för att bygga ett Android-program som autentiserar Microsoft Identity-användare till vår B2C-identitets tjänst och anropar ett webb-API med OAuth 2,0-åtkomsttoken. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Ett exempel som visar hur ett Windows Desktop .NET-program (WPF-program) kan logga in en användare som använder Azure AD B2C, få ett åtkomsttoken med hjälp av MSAL.NET och anropa ett API. |
| [xamarin-intern](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | En enkel Xamarin Forms-app som visar hur du använder MSAL för att autentisera användare via Azure Active Directory B2C och få åtkomst till ett webb-API med resulterande token. |

## <a name="web-apps-and-apis"></a>Webbappar och API:er

| Exempel | Beskrivning |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Ett kombinerat exempel för ett .NET-webbprogram som anropar ett .NET-webb-API, båda skyddade med Azure AD B2C. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Ett ASP.NET Core webb program som använder OpenID Connect för att logga in användare i Azure AD B2C. |
| [dotnetcore-webapp-msal-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Ett ASP.NET Core-webbprogram som kan logga in en användare som använder Azure AD B2C, få ett åtkomsttoken med hjälp av MSAL.NET och anropa ett API. |
| [openidconnect nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | En Node.js-app som ger ett snabbt och enkelt sätt att konfigurera ett webbprogram med Express med hjälp av OpenID Connect. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Ett litet node.js-webb-API för Azure AD B2C som visar hur du skyddar ditt webb-api och accepterar B2C-åtkomsttoken med passport.js. |
| [MS-Identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Demonstrerar hur du integrerar B2C av Microsoft Identity Platform med ett python-webbprogram.  |

## <a name="single-page-apps"></a>Appar med en sida

| Exempel | Beskrivning |
|--------| ----------- |
| [MS-Identity-JavaScript-preakta – självstudie](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | Ett enda sid program (SPA) som anropar ett webb-API. Autentisering görs med Azure AD B2C med hjälp av MSAL reagerar. I det här exemplet används Authorization Code Flow med PKCE. |
| [MS-Identity-B2C-JavaScript-Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Ett enda sid program (SPA) som anropar ett webb-API. Autentisering görs med Azure AD B2C med hjälp av MSAL.js. I det här exemplet används Authorization Code Flow med PKCE. |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Ett enda sid program (SPA) som anropar ett webb-API. Autentisering görs med Azure AD B2C med hjälp av MSAL.js. I det här exemplet används det implicita flödet.|
| [Java Script-NodeJS-Management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | Ett enda sid program (SPA) som anropar Microsoft Graph för att hantera användare i en B2C-katalog. Autentisering görs med Azure AD B2C med hjälp av MSAL.js. I det här exemplet används Authorization Code Flow med PKCE.|

## <a name="consoledaemon-apps"></a>Konsol/daemon-appar

| Exempel | Beskrivning |
|--------| ----------- |
| [Java Script-NodeJS-Management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | Ett Node.js-och Express Console daemon-program anropar Microsoft Graph med sin egen identitet för att hantera användare i en B2C-katalog. Autentisering görs med Azure AD B2C med hjälp av MSAL-noden. I det här exemplet används Authorization Code Flow.|
| [dotnetcore-B2C-Account-Management](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | Ett .NET Core-konsolprogram som anropar Microsoft Graph med sin egen identitet för att hantera användare i en B2C-katalog. Autentisering görs med Azure AD B2C med hjälp av MSAL.NET. I det här exemplet används Authorization Code Flow.|

## <a name="saml-test-application"></a>SAML-testprogram

| Exempel | Beskrivning |
|--------| ----------- |
| [SAML-SP-testare](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | SAML-testprogram för att testa Azure AD B2C som har kon figurer ATS för att fungera som SAML Identity Provider. |

## <a name="api-connectors"></a>API-anslutningsprogram

Följande tabeller innehåller länkar till kod exempel för att använda webb-API: er i dina användar flöden med [API-kopplingar](api-connectors-overview.md).

### <a name="azure-function-quickstarts"></a>Snabb starter för Azure Function

| Exempel                                                                                                                          | Beskrivning                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Den här .NET Core Azure Function-exemplet visar hur du begränsar registreringen till vissa e-postdomäner och validerar information från användaren. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Det här Node.js Azure Function-exemplet visar hur du begränsar registreringen till vissa e-postdomäner och validerar information från användaren.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Det här python Azure Function-exemplet visar hur du begränsar registreringen till vissa e-postdomäner och verifierar information från användaren.    |


### <a name="automated-fraud-protection-services--captcha"></a>Automatiserat skydds tjänster för bedrägeri & CAPTCHA
| Exempel                                                                                                            | Beskrivning                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs bedrägeri-och missbruks skydd](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | Det här exemplet visar hur du skyddar dina användar inloggningar med hjälp av Arkose Labs bedrägerier och missbruks skydds tjänst. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | Det här exemplet visar hur du skyddar användar inloggningen med en reCAPTCHA-utmaning för att förhindra automatiserat missbruk. |


### <a name="identity-verification"></a>Identitets verifiering

| Exempel                                                                                                            | Beskrivning                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Det här exemplet visar hur du verifierar en användar identitet som en del av dina registrerings flöden genom att använda en API-anslutning för att integrera med IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Det här exemplet visar hur du verifierar en användar identitet som en del av dina registrerings flöden genom att använda en API-anslutning för att integrera med Experian:. |


### <a name="other"></a>Annat

| Exempel                                                                                                            | Beskrivning                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Inbjudnings kod](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | Det här exemplet visar hur du begränsar registreringen till vissa mål grupper genom att använda Inbjudnings koder.|
| [Community-exempel för API-anslutning](https://github.com/azure-ad-b2c/api-connector-samples) | Den här lagrings platsen innehåller Community-exempel för scenarier som har Aktiver ATS av API-kopplingar|
