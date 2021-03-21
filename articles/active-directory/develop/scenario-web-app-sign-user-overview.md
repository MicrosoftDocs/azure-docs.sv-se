---
title: Logga in användare från en webbapp | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en webbapp som loggar in användare (översikt)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0bbc799f946d318c305a96d9cb8c6831d9242ff6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578302"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: webb program som loggar in användare

Lär dig allt du behöver för att bygga en webbapp som använder Microsoft Identity Platform för att logga in användare.

## <a name="getting-started"></a>Komma igång

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Följ den här snabb starten om du vill skapa din första bärbara (ASP.NET Core)-webbapp som loggar in användare:

[Snabb start: ASP.NET Core webbapp som loggar in användare](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Om du vill veta hur du lägger till inloggning i ett befintligt ASP.NET-webbprogram, prova följande snabb start:

[Snabb start: ASP.NET-webbapp som loggar in användare](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Om du är en Java-utvecklare kan du prova följande snabb start:

[Snabb start: lägga till inloggning med Microsoft i en Java-webbapp](quickstart-v2-java-webapp.md)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Om du är Node.js utvecklare kan du prova följande snabb start:

[Snabb start: lägga till inloggning med Microsoft i ett Node.js-webbprogram](quickstart-v2-nodejs-webapp-msal.md)

# <a name="python"></a>[Python](#tab/python)

Om du utvecklar med python kan du prova följande snabb start:

[Snabb start: lägga till inloggning med Microsoft i en python-webbapp](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Översikt

Du lägger till autentisering till din webbapp så att den kan logga in användare. Genom att lägga till autentisering kan din webbapp komma åt begränsad profil information för att anpassa användar upplevelsen.

Web Apps autentiserar en användare i en webbläsare. I det här scenariot dirigerar webbappen användarens webbläsare för att logga in till Azure Active Directory (Azure AD). Azure AD returnerar ett inloggnings svar via användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. Inloggnings användare drar nytta av [Open ID Connect](./v2-protocols-oidc.md) standard-protokollet, vilket är förenklat genom användning av mellanprogram [bibliotek](scenario-web-app-sign-user-app-configuration.md#microsoft-libraries-supporting-web-apps).

![Webbapp loggar in användare](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Som en andra fas kan du aktivera ditt program så att det anropar webb-API: er åt den inloggade användaren. Nästa fas är ett annat scenario, som du hittar i [webb program som anropar webb-API: er](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Att lägga till inloggning i en webbapp är att skydda webbappen och verifiera en användartoken, vilket är vad  **mellan** -program varu bibliotek gör. I fallet med .NET kräver det här scenariot ännu inte Microsoft Authentication Library (MSAL), som handlar om att förvärva en token för att anropa skyddade API: er. Autentiseringsscheman för .NET kommer att införas i uppföljnings scenariot när webbappen behöver anropa webb-API: er.

## <a name="specifics"></a>Information

- Under program registreringen måste du ange en eller flera (om du distribuerar appen till flera platser) svars-URI: er. I vissa fall (ASP.NET och ASP.NET Core) måste du aktivera ID-token. Slutligen vill du skapa en utloggnings-URI så att ditt program reagerar på användare som loggar ut.
- I koden för ditt program måste du ange den myndighet som webbappen delegerar inloggning för. Du kanske vill anpassa verifiering av token (särskilt i partner scenarier).
- Webb program stöder alla typer av konton. Mer information finns i [konto typer som stöds](v2-supported-account-types.md).

## <a name="recommended-reading"></a>Rekommenderad läsning

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Nästa steg

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Gå vidare till nästa artikel i det här scenariot, [Registrera appen](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Gå vidare till nästa artikel i det här scenariot, [Registrera appen](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Gå vidare till nästa artikel i det här scenariot, [Registrera appen](./scenario-web-app-sign-user-app-registration.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Gå vidare till nästa artikel i det här scenariot, [Registrera appen](./scenario-web-app-sign-user-app-registration.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Gå vidare till nästa artikel i det här scenariot, [Registrera appen](./scenario-web-app-sign-user-app-registration.md?tabs=python).

---
