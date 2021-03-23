---
title: Azure AD-autentisering & nationella moln | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om app-registrering och autentiserings slut punkter för nationella moln.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: 719d89fafe57e7f5132698404bcc0a41a9f9db37
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780080"
---
# <a name="national-clouds"></a>Nationella moln

Nationella moln är fysiskt isolerade instanser av Azure. Dessa regioner i Azure är utformade för att säkerställa att kraven på data placering, suveränitet och efterlevnad är uppfyllda inom geografiska gränser.

Med det globala molnet distribueras Azure Active Directory (Azure AD) i följande nationella moln:  

- Azure Government
- Azure Tyskland
- Azure Kina 21Vianet

Nationella moln är unika och en separat miljö från Azure Global. Det är viktigt att vara medveten om viktiga skillnader när du utvecklar ditt program för de här miljöerna. Skillnaderna är att registrera program, förvärva token och konfigurera slut punkter.

## <a name="app-registration-endpoints"></a>Slut punkter för program registrering

Det finns en separat Azure Portal för var och en av de nationella molnen. Om du vill integrera program med Microsoft Identity Platform i ett nationellt moln måste du registrera ditt program separat i varje Azure Portal som är specifik för miljön.

I följande tabell visas de grundläggande URL: erna för de Azure AD-slutpunkter som används för att registrera ett program för varje nationellt moln.

| Nationellt moln | Azure AD Portal-slutpunkt |
|----------------|--------------------------|
| Azure AD för amerikanska myndigheter | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| Azure AD, Kina drivs av 21Vianet | `https://portal.azure.cn` |
| Azure AD (global tjänst) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD-autentiserings slut punkter

Alla nationella moln autentiserar användare separat i varje miljö och har separata autentiserings slut punkter.

I följande tabell visas de grundläggande URL: erna för de Azure AD-slutpunkter som används för att hämta tokens för varje nationellt moln.

| Nationellt moln | Azure AD-autentiseringens slut punkt |
|----------------|-------------------------|
| Azure AD för amerikanska myndigheter | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| Azure AD, Kina drivs av 21Vianet | `https://login.partner.microsoftonline.cn/common` |
| Azure AD (global tjänst)| `https://login.microsoftonline.com` |

Du kan skapa förfrågningar till Azure AD-auktorisering eller token-slutpunkter med hjälp av lämplig region-/regionsspecifika bas-URL. Till exempel för Azure Germany:

- Vanlig slut punkt för auktorisering är `https://login.microsoftonline.de/common/oauth2/v2.0/authorize` .
- Vanlig slut punkt för token är `https://login.microsoftonline.de/common/oauth2/v2.0/token` .

För program med en enda klient ersätter du "common" i föregående URL: er med ditt klient-ID eller namn. Ett exempel är `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Information om hur du anropar Microsoft Graph-API: er i en nationell moln miljö finns i [Microsoft Graph i nationella moln distributioner](/graph/deployments).

> [!IMPORTANT]
> Vissa tjänster och funktioner i vissa regioner i den globala tjänsten kanske inte är tillgängliga i alla nationella moln. Om du vill ta reda på vilka tjänster som är tillgängliga går du till [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Om du vill lära dig hur du skapar ett program med hjälp av Microsoft Identity Platform följer du [själv studie kursen för Microsoft Authentication Library (MSAL)](msal-national-cloud.md). Mer specifikt kommer den här appen att logga in en användare och få en åtkomsttoken för att anropa Microsoft Graph-API: et.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder [Microsoft Authentication Library (MSAL) i en nationell moln miljö](msal-national-cloud.md).

National Cloud-dokumentation:

- [Azure Government](../../azure-government/index.yml)
- [Azure Kina 21Vianet](/azure/china/)
- [Azure Tyskland](../../germany/index.yml)
