---
title: Flytta en app med en enda sida till produktion
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett program med en sida (flytta till produktion)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0c1e9e6da02478f5e4703676b74fc8247a4f619b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753519"
---
# <a name="single-page-application-move-to-production"></a>Program med en sida: flytta till produktion

Nu när du vet hur du kan hämta en token för att anropa webb-API: er, lär du dig hur du flyttar till produktion.

## <a name="improve-your-app"></a>Förbättra din app

[Aktivera loggning](msal-logging.md) för att göra din app-produktion klar.

## <a name="test-your-integration"></a>Testa din integrering

Testa din integrering genom att följa [Check lista för Microsoft Identity Platform-integration](identity-platform-integration-checklist.md).

## <a name="deploy-your-app"></a>Distribuera din app

Kolla in ett [distributions exempel](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) för att lära dig hur du distribuerar dina spa-och webb-API-projekt med Azure Storage-och Azure App-tjänster. 

## <a name="next-steps"></a>Nästa steg

- Djupgående snabb starts exempel, som förklarar koden för hur du loggar in användare och hämtar en åtkomsttoken för att anropa **Microsoft Graph-API: et** genom att använda **MSAL.js**: [Java Script Spa själv studie kurs](./tutorial-v2-javascript-spa.md).

- Exempel som visar hur du hämtar token för din egen server dels webb-API (ASP.NET Core) genom att använda **MSAL.js**: [SPA med en ASP.NET Server](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)del.

- Exempel som visar hur du verifierar åtkomsttoken för din server dels webb-API (Node.js) med hjälp av **Passport-Azure-AD**: [Node.js webb-API (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2).

- Exempel som visar hur du använder **MSAL.js** för att logga in användare i en app som har registrerats med **Azure Active Directory B2C** (Azure AD B2C): [spa med Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

- Exempel som visar hur du använder **Passport-Azure-AD** för att verifiera åtkomsttoken för appar som registrerats med **Azure Active Directory B2C** (Azure AD B2C): [Node.js webb-API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
