---
title: 'Flytta en daemon-app som anropar webb-API: er till produktion | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du flyttar en daemon-app som anropar webb-API: er till produktion'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8dc9bff86a07f3d4a0ec6fd224de6d5633165a6d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582849"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-app som anropar webb-API: er – flytta till produktion

Nu när du vet hur man hämtar och använder en token för ett tjänst-till-tjänst-anrop, lär du dig hur du flyttar din app till produktion.

## <a name="deployment---multitenant-daemon-apps"></a>Distribution – daemon-appar för flera innehavare

Om du är en ISV som skapar ett daemon-program som kan köras i flera klienter, kontrollerar du att klient administratören:

- Tillhandahåller ett tjänst huvud namn för programmet.
- Ger medgivande till programmet.

Du måste förklara vad kunderna har för att utföra dessa åtgärder. Mer information finns i [begära medgivande för en hel klient](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Kodexempel

# <a name="net"></a>[.NET](#tab/dotnet)

- Referens dokumentation för:
  - Instansierar [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Anropar [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Andra exempel/Självstudier:
  - [Microsoft-Identity-Platform-Console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) innehåller ett litet .net Core daemon-konsolprogram som visar användare av en klient som frågar Microsoft Graph.

    ![Exempel på daemon app-topologi](media/scenario-daemon-app/daemon-app-sample.svg)

    Samma exempel illustrerar också en variant med certifikat:

    ![Exempel på daemon-app-certifikat](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) har en ASP.NET MVC-webbapp som synkroniserar data från Microsoft Graph med hjälp av identiteten för programmet i stället för en användares räkning. Det här exemplet illustrerar även godkännande processen för administratörer.

    ![topologi](media/scenario-daemon-app/damon-app-sample-web.svg)

---

## <a name="next-steps"></a>Nästa steg

Här följer några länkar som hjälper dig att lära dig mer:

# <a name="python"></a>[Python](#tab/python)

Försök att [Hämta en token med snabb start och anropa Microsoft Graph API från en python-konsol app med appens identitet](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Prova snabb starten [Hämta en token och anropa Microsoft Graph API från en Java-konsol med appens identitet](./quickstart-v2-java-daemon.md).

---
