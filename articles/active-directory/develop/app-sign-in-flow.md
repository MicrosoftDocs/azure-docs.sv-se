---
title: Inloggnings flödet för appen med Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: Läs om inloggnings flödet för webb-, skriv bords-och mobilappar i Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1f9f330ab140fa66b5a66a112c47ca2a68ba56bf
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755697"
---
# <a name="app-sign-in-flow-with-the-microsoft-identity-platform"></a>Inloggnings flöde för appar med Microsoft Identity Platform

I det här avsnittet beskrivs det grundläggande inloggnings flödet för webb-, skriv bords-och mobilappar med Microsoft Identity Platform. Se [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md) för att lära dig om inloggnings scenarier som stöds av Microsoft Identity Platform.

## <a name="web-app-sign-in-flow"></a>Inloggnings flöde för webb program

När en användare navigerar i webbläsaren till en webbapp händer följande:

* Webb programmet avgör om användaren är autentiserad.
* Om användaren inte har autentiserats delegerar webbappen till Azure AD för att logga in användaren. Inloggningen är kompatibel med principen för organisationen, vilket kan innebära att användaren anger sina autentiseringsuppgifter med hjälp av [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) (kallas ibland TVÅFAKTORAUTENTISERING eller 2fa) eller inte använder ett lösen ord alls (till exempel med hjälp av Windows Hello).
* Användaren uppmanas att godkänna åtkomsten som klient appen behöver. Detta är anledningen till att klient program måste registreras med Azure AD, så att Microsoft Identity Platform kan leverera tokens som representerar den åtkomst som användaren har samtyckt till.

När användaren har autentiserats:

* Microsoft Identity Platform skickar en token till webbappen.
* En cookie sparas som är kopplad till Azure AD-domänen och som innehåller användarens identitet i webbläsarens jar-jar. Nästa gång en app använder webbläsaren för att navigera till Microsoft Identity Platform Authorization-slutpunkten presenterar webbläsaren cookien så att användaren inte behöver logga in igen. Detta är också det sätt på vilket SSO uppnås. Cookien skapas av Azure AD och kan bara tolkas av Azure AD.
* Webbappen validerar sedan token. Om verifieringen lyckas visas den skyddade sidan i webbappen och en sessions-cookie sparas i webbläsarens Jar-Jar-form. När användaren navigerar till en annan sida vet webbappen att användaren är autentiserad baserat på sessionens cookie.

Följande sekvensdiagram sammanfattar interaktionen:

![autentisering av webbapp](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hur en webbapp fastställer om användaren är autentiserad

Utvecklare av webbappar kan ange om alla eller endast vissa sidor kräver autentisering. I ASP.NET/ASP.NET Core görs detta genom att `[Authorize]` attributet läggs till i styrenhets åtgärderna.

Det här attributet gör att ASP.NET söker efter en sessions-cookie som innehåller användarens identitet. Om en cookie inte finns omdirigerar ASP.NET autentiseringen till den angivna identitets leverantören. Om identitets leverantören är Azure AD omdirigerar webbappen autentiseringen till `https://login.microsoftonline.com` , som visar en dialog ruta för inloggning.

### <a name="how-a-web-app-delegates-sign-in-to-the-microsoft-identity-platform-and-obtains-a-token"></a>Hur en webbapp delegerar inloggning till Microsoft Identity Platform och hämtar en token

Användarautentisering sker via webbläsaren. OpenID-protokollet använder vanliga HTTP-protokoll meddelanden.

* Webbappen skickar en HTTP 302 (Omdirigerad) till webbläsaren för att använda Microsoft Identity Platform.
* När användaren autentiseras skickar Microsoft Identity Platform token till webbappen med hjälp av en omdirigering via webbläsaren.
* Omdirigeringen tillhandahålls av webb programmet i form av en omdirigerings-URI. Den här omdirigerings-URI: n har registrerats med objektet Azure AD-program. Det kan finnas flera omdirigerings-URI: er eftersom programmet kan distribueras på flera URL: er. Webbappen måste också ange den omdirigerings-URI som ska användas.
* Azure AD kontrollerar att den omdirigerings-URI som skickas av webbappen är en av de registrerade omdirigerings-URI: erna för appen.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Inloggnings flöde för skriv bord och mobilapp

Flödet som beskrivs ovan gäller, med små skillnader, för Station ära och mobila program.

Skriv bords-och mobil program kan använda en inbäddad webb kontroll eller en system webbläsare för autentisering. Följande diagram visar hur en stationär eller mobilapp använder Microsoft Authentication Library (MSAL) för att hämta åtkomsttoken och anropa webb-API: er.

![Desktop-appen hur det verkar vara](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL använder en webbläsare för att hämta tokens. Precis som med Web Apps delegeras autentiseringen till Microsoft Identity Platform.

Eftersom Azure AD sparar samma identitets-cookie i webbläsaren som den gör för webbappar, kommer den interna eller mobilappen att använda system läsaren omedelbart för att få enkel inloggning med motsvarande webbapp.

Som standard använder MSAL system webbläsare. Undantaget är .NET Framework Skriv bords program där en inbäddad kontroll används för att ge en mer integrerad användar upplevelse.

## <a name="next-steps"></a>Nästa steg

För andra ämnen som behandlar grundläggande autentisering och auktorisering:

* Se [autentisering kontra auktorisering](authentication-vs-authorization.md) för att lära dig mer om grundläggande begrepp för autentisering och auktorisering i Microsoft Identity Platform.
* Se [säkerhetstoken](security-tokens.md) för att lära dig hur åtkomsttoken, uppdaterings-token och ID-token används vid autentisering och auktorisering.
* Se [program modell](application-model.md) för att lära dig mer om hur du registrerar ditt program så att det kan integreras med Microsoft Identity Platform.

Läs mer om app-inloggnings flöde:

* Mer information om andra scenarier för autentisering av användare som stöds av Microsoft Identity Platform finns i [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md) .
* Se [MSAL-bibliotek](msal-overview.md) för att lära dig om de Microsoft-bibliotek som hjälper dig att utveckla program som fungerar med Microsoft-konton, Azure AD-konton och Azure AD B2C användare i en enda, strömlinjeformad programmerings modell.
