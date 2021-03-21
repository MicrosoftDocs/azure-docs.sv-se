---
title: Azure App konfiguration REST API-autentisering
description: Referens sidor för autentisering med Azure App konfigurations REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932701"
---
# <a name="authentication"></a>Autentisering

Alla HTTP-begäranden måste autentiseras. Följande autentiseringsscheman stöds.

## <a name="hmac"></a>HMAC

[HMAC-autentisering](./rest-api-authentication-hmac.md) använder en slumpmässigt genererad hemlighet för att signera nytto laster för begäran. Information om hur förfrågningar som använder den här autentiseringsmetoden är auktoriserade finns i avsnittet [HMAC-auktorisering](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) använder en Bearer-token som erhålls från Azure Active Directory för att autentisera begär Anden. Information om hur begär Anden som använder den här autentiseringsmetoden är auktoriserade i avsnittet [Azure AD-auktorisering](./rest-api-authorization-azure-ad.md) .