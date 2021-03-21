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
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954976"
---
# <a name="single-page-application-move-to-production"></a>Program med en sida: flytta till produktion

Nu när du vet hur du kan hämta en token för att anropa webb-API: er, här är några saker att tänka på när du flyttar ditt program till produktion.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Distribuera din app

Kolla in ett [distributions exempel](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) för att lära dig hur du distribuerar dina spa-och webb-API-projekt med Azure Storage-och Azure App-tjänster. 

## <a name="code-samples"></a>Kodexempel

Dessa kod exempel demonstrerar flera viktiga åtgärder för en app med en enda sida.
- [Spa med en ASP.NET Server](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)del: så här hämtar du tokens för din egen server dels webb-API (ASP.net Core) med hjälp av **MSAL.js**.

- [Node.js webb-API (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): verifiera åtkomsttoken för ditt Server dels webb-API (Node.js) med hjälp av **Passport-Azure-AD**.

- [Spa med Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): använda **MSAL.js** för att logga in användare i en app som är registrerad med **Azure Active Directory B2C** (Azure AD B2C).

- [Node.js webb-API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): så här använder du **Passport-Azure-AD** för att verifiera åtkomsttoken för appar som registrerats med **Azure Active Directory B2C** (Azure AD B2C).

## <a name="next-steps"></a>Nästa steg

- [Själv studie kurs för Java Script](./tutorial-v2-javascript-spa.md): djup gå till hur du ska logga in användare och få en åtkomsttoken att anropa **Microsoft Graph API** med hjälp av **MSAL.js**.