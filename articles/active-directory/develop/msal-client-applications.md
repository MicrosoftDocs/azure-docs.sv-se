---
title: Offentliga och konfidentiella klient program (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om offentliga klient program och konfidentiella klient program i Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2ef29f6fe3403809d01fcea382474c514319b7c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063764"
---
# <a name="public-client-and-confidential-client-applications"></a>Offentlig klient och konfidentiella klient program
Microsoft Authentication Library (MSAL) definierar två typer av klienter: offentliga klienter och konfidentiella klienter. De två klient typerna särskiljs genom att deras förmåga att autentisera sig på ett säkert sätt med auktoriseringsservern och upprätthålla konfidentialiteten för deras klientautentiseringsuppgifter. Azure AD Authentication Library (ADAL) använder däremot vad som kallas *autentiserings kontext* (som är en anslutning till Azure AD).

- **Konfidentiella klient program** är appar som körs på servrar (webbappar, webb-API-appar eller till och med service/daemon-appar). De anses vara svåra att komma åt, och därför kan det vara möjligt att hålla en program hemlighet. Konfidentiella klienter kan lagra konfigurations tids hemligheter. Varje instans av klienten har en särskild konfiguration (inklusive klient-ID och klient hemlighet). De här värdena är svåra för slutanvändare att extrahera. En webbapp är den vanligaste konfidentiella klienten. Klient-ID: t visas via webbläsaren, men hemligheten skickas bara i tillbaka-kanalen och aldrig direkt exponeras.

    Konfidentiella klient program: <BR>
    ![](media/msal-client-applications/web-app.png) ![ ](media/msal-client-applications/web-api.png) Daemon/tjänst för webb program webb-API ![](media/msal-client-applications/daemon-service.png)

- **Offentliga klient program** är appar som körs på enheter eller Station ära datorer eller i en webbläsare. De är inte betrodda för att på ett säkert sätt behålla program hemligheter, så de får bara åtkomst till webb-API: er för användarens räkning. (De stöder bara offentliga klient flöden.) Offentliga klienter kan inte lagra konfigurations tids hemligheter, så de har inte klient hemligheter.

    Offentliga klient program: <BR>
    ![Desktop app ](media/msal-client-applications/desktop-app.png) ![ WEBBLÄSARbaserade API- ](media/msal-client-applications/browserless-app.png) ![ mobilapp](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> I MSAL.js finns det ingen åtskillnad mellan offentliga och konfidentiella klient program.  MSAL.js representerar klient program som användar agent-baserade appar, offentliga klienter där klient koden körs i en användar agent som en webbläsare. Dessa klienter lagrar inte hemligheter eftersom webb läsar kontexten är lättillgänglig.

## <a name="comparing-the-client-types"></a>Jämföra klient typerna
Här följer några likheter och skillnader mellan offentliga klient-och konfidentiella klient program:

- Båda typerna av appar upprätthåller en användartoken och kan hämta en token tyst (när token redan finns i token-cachen). Konfidentiella klient program har också en app-token cache för token som gäller för själva appen.
- Båda typerna av appar hanterar användar konton och kan hämta ett konto från användarens token cache, hämta ett konto från dess identifierare eller ta bort ett konto.
- Offentliga klient program har fyra sätt att hämta en token (fyra autentiseringsscheman). Konfidentiella klient program har tre sätt att hämta en token (och ett sätt att beräkna URL: en för den auktoriserade slut punkten för identitets leverantören). Mer information finns i [inhämta token](msal-acquire-cache-tokens.md).

Om du har använt ADAL kanske klient-ID: t (även kallat *program-ID* eller *app-ID*) skickas en gång när programmet byggs. Den behöver inte skickas igen när appen erhåller en token. Detta gäller både för offentliga och konfidentiella klient program. Konstruktörer för konfidentiella klient program skickas också till klientens autentiseringsuppgifter: den hemlighet de delar med identitets leverantören.

## <a name="next-steps"></a>Nästa steg
Läs om:
- [Konfigurations alternativ för klient program](msal-client-application-configuration.md)
- [Instansiera klient program med hjälp av MSAL.NET](msal-net-initializing-client-applications.md)
- [Instansiera klient program med hjälp av MSAL.js](msal-js-initializing-client-applications.md)
