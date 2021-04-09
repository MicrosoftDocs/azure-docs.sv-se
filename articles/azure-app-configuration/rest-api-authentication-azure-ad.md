---
title: Azure Active Directory REST API-autentisering
description: Använd Azure Active Directory för att autentisera till Azure App konfiguration med hjälp av REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930525"
---
# <a name="azure-active-directory-authentication"></a>Azure Active Directory-autentisering

Du kan autentisera HTTP-begäranden med hjälp av `Bearer` autentiseringsschemat med en token som hämtats från Azure Active Directory (Azure AD). Du måste skicka dessa förfrågningar via Transport Layer Security (TLS).

## <a name="prerequisites"></a>Förutsättningar

Du måste tilldela huvud kontot som används för att begära en Azure AD-token till en av de tillämpliga [Azure App konfigurations rollerna](./rest-api-authorization-azure-ad.md).

Ange varje begäran med alla HTTP-huvuden som krävs för autentisering. Följande är minimi kravet:

|  Begärandehuvud | Beskrivning  |
| --------------- | ------------ |
| `Authorization` | Autentiseringsinformation som krävs av `Bearer` schemat. |

**Exempel:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Azure AD-token-hämtning

Innan du skaffar en Azure AD-token måste du identifiera vilken användare som du vill autentisera som, vilken mål grupp du begär token för, samt vilken Azure AD-slutpunkt (auktoritet) som ska användas.

### <a name="audience"></a>Målgrupp

Begär Azure AD-token med rätt mål grupp. Använd någon av följande mål grupper för Azure App konfiguration. Mål gruppen kan också kallas för den *resurs* som token begärs för.

- {configurationStoreName}. azconfig. io
- *. azconfig.io

> [!IMPORTANT]
> När den begärda mål gruppen är `{configurationStoreName}.azconfig.io` måste den exakt matcha `Host` begär ande huvudet (Skift läges känsligt) som används för att skicka begäran.

### <a name="azure-ad-authority"></a>Azure AD-auktoritet

Azure AD-utfärdaren är den slut punkt som du använder för att skaffa en Azure AD-token. Det är i form av `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`Segmentet refererar till det Azure AD-klient-ID som användaren eller programmet som försöker autentisera tillhör.

### <a name="authentication-libraries"></a>Autentiseringsbibliotek

Azure tillhandahåller en uppsättning bibliotek, som kallas Azure Active Directory autentiseringsscheman, för att förenkla processen med att förvärva en Azure AD-token. Azure skapar dessa bibliotek för flera språk. Mer information finns i [dokumentationen](../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="errors"></a>Fel

Följande fel kan uppstå.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Orsak:** Du har inte angett något huvud för auktoriseringsbegäran för begäran med `Bearer` schemat.

**Lösning:** Ange ett giltigt `Authorization` http-begär ande huvud.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Orsak:** Azure AD-token är inte giltig.

**Lösning:** Skaffa en Azure AD-token från Azure AD-utfärdaren och kontrol lera att du har använt rätt mål grupp.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Orsak:** Azure AD-token är inte giltig.

**Lösning:** Hämta en Azure AD-token från Azure AD-utfärdaren. Se till att Azure AD-klienten är den som är associerad med den prenumeration som konfigurations lagret tillhör. Det här felet kan uppstå om huvudobjektet tillhör fler än en Azure AD-klient.