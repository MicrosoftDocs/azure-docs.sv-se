---
title: 'Bygg en mobilapp som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform | Azure
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (översikt)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f60e21f98092df65ef01e68fe9c2e64833a24855
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063220"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: mobil program som anropar webb-API: er

Lär dig hur du skapar en mobilapp som anropar webb-API: er.

## <a name="getting-started"></a>Kom igång

Om du inte redan har gjort det kan du skapa ditt första program genom att slutföra en snabb start:

- [Snabb start: Hämta en token och anropa Microsoft Graph API från en Android-app](./quickstart-v2-android.md)
- [Snabb start: Hämta en token och anropa Microsoft Graph API från en iOS-app](./quickstart-v2-ios.md)
- [Snabb start: Hämta en token och anropa Microsoft Graph API från en Xamarin iOS-och Android-app](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) (GitHub)

## <a name="overview"></a>Översikt

En anpassad och smidig användar upplevelse är nödvändig för mobila appar.  Med Microsoft Identity Platform kan mobila utvecklare skapa denna upplevelse för iOS-och Android-användare. Ditt program kan logga in Azure Active Directory (Azure AD)-användare, personliga Microsoft-konto användare och Azure AD B2C användare. Det kan också hämta token för att anropa ett webb-API för deras räkning. För att implementera dessa flöden använder vi Microsoft Authentication Library (MSAL). MSAL implementerar standarden [OAuth 2.0 Authorization Code Flow](v2-oauth2-auth-code-flow.md).

![Daemon-appar](./media/scenarios/mobile-app.svg)

Överväganden för Mobile Apps:

- **Användar upplevelsen är nyckel**: Tillåt användare att se appens värde innan du ber om inloggning. Begär endast de behörigheter som krävs.
- **Stöd för alla användarkonfigurationer**: många användare av mobila företag måste följa principer för villkorlig åtkomst och principer för enhets efterlevnad. Se till att du har stöd för dessa nyckel scenarier.
- **Implementera enkel inloggning (SSO)**: med hjälp av MSAL och Microsoft Identity Platform kan du aktivera enkel inloggning via enhetens webbläsare eller Microsoft Authenticator (och Intune-företagsportal på Android).
- **Implementera delad enhets läge**: gör att ditt program kan användas i scenarier med delad enhet, till exempel sjukhus, tillverkning, detalj handel och ekonomi. [Läs mer om stöd för delad enhets läge](msal-shared-devices.md).

## <a name="specifics"></a>Information

Tänk på följande när du skapar en mobilapp på Microsoft Identity Platform:

- Beroende på plattform kan vissa användar åtgärder krävas första gången användaren loggar in. IOS kräver till exempel appar för att Visa användar interaktion när de använder SSO för första gången via Microsoft Authenticator (och Intune-företagsportal på Android).
- På iOS och Android kan MSAL använda en extern webbläsare för att logga in användare. Den externa webbläsaren kan visas ovanpå din app.
- Använd aldrig en hemlighet i ett mobil program. I dessa program är hemligheter tillgängliga för alla användare.

## <a name="recommended-reading"></a>Rekommenderad läsning

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Registrera appen](scenario-mobile-app-registration.md).
