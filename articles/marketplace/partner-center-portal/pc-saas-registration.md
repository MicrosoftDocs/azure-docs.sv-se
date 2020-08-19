---
title: Registrera ett SaaS-program – Azure Marketplace
description: Lär dig hur du använder Azure Portal för att registrera ett SaaS-program och ta emot en Azure Active Directory säkerhetstoken.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: dddcdd52709c634f0dcf67cb982804f5556e88b9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548343"
---
# <a name="register-a-saas-application"></a>Registrera ett SaaS-program

I den här artikeln förklaras hur du registrerar ett SaaS-program med hjälp av Microsoft [Azure Portal](https://portal.azure.com/) och hur du hämtar utgivarens åtkomsttoken (Azure Active Directory-åtkomsttoken). Utgivaren kommer att använda denna token för att autentisera SaaS-programmet genom att anropa API: er för SaaS-utförande.  API: erna för utförande använder OAuth 2,0-klientens autentiseringsuppgifter för att bevilja flöde på Azure Active Directory (v 1.0) slut punkter för att göra en begäran om en tjänst-till-tjänst-åtkomsttoken.

Azure Marketplace tillhandahåller inte några begränsningar för autentiseringsmetoden som används av SaaS-tjänsten för slutanvändare. Flödet nedan krävs endast för autentisering av SaaS-tjänsten i Azure Marketplace.

Mer information om Azure AD (Active Directory) finns i [Vad är autentisering](../../active-directory/develop/authentication-scenarios.md)?

## <a name="register-an-azure-ad-secured-app"></a>Registrera en Azure AD-skyddad app

Alla program som vill använda funktionerna i Azure AD måste först registreras i en Azure AD-klientorganisation. Den här registrerings processen innebär att ge Azure AD lite information om ditt program. Gör så här för att registrera ett nytt program med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Om ditt konto ger dig åtkomst till mer än ett, klickar du på ditt konto i det övre högra hörnet och ställer in din portal-session till önskad Azure AD-klient.
3. I det vänstra navigerings fönstret klickar du på **Azure Active Directory** tjänst, klickar på **Appregistreringar**och klickar på **ny program registrering**.

    ![SaaS AD App-registreringar](./media/saas-offer-app-registration-v1.png)

4. På sidan Skapa anger du din program \' s registrerings information:
    -   **Namn**: Ange ett meningsfullt program namn
    -   **Program typ**:  
        
        Välj **webbapp/API** för [klient program](../../active-directory/develop/active-directory-dev-glossary.md#client-application)) och [resurs/API-program](../../active-directory/develop/active-directory-dev-glossary.md#resource-server)som är installerade på en säker server. Den här inställningen används för OAuth-konfidentiella [webb klienter](../../active-directory/develop/active-directory-dev-glossary.md#web-client)) och offentliga [användare-agentbaserade klienter](../../active-directory/develop/active-directory-dev-glossary.md#user-agent-based-client)).
        Samma program kan även visa både en klient och resurs/API.

        För särskilda exempel på webb program, se de guidade installations guiderna som finns i avsnittet [komma igång](../../active-directory/develop/quickstart-create-new-tenant.md) i [Azure AD-utvecklare](../../active-directory/develop/index.yml).

5. När du är färdig klickar du på **Registrera**.  Azure AD tilldelar ett unikt *program-ID* till det nya programmet. Vi rekommenderar att du registrerar en app som endast har åtkomst till API: et och som en enda klient.

6. Om du vill skapa klient hemlighet navigerar du till **sidan certifikat & hemligheter** och klickar på **+ ny klient hemlighet**.  Se till att kopiera det hemliga värdet för att använda det i din kod.

**Azure AD App-ID: t** är associerat med ditt UTGIVAR-ID, så se till att samma *app-ID* används i alla dina erbjudanden.

>[!Note]
>Om en utgivare har två olika konton i Partner Center, ska två olika Azure AD App-ID: n användas.  Varje partner konto i Partner Center bör använda ett unikt Azure AD App-ID för alla SaaS-erbjudanden som publiceras via det här kontot.

## <a name="how-to-get-the-publishers-authorization-token"></a>Hämta utgivarens autentiseringstoken

När du har registrerat ditt program kan du program mässigt begära utgivarens autentiseringstoken (Azure AD-åtkomsttoken med hjälp av Azure AD v1-slutpunkt). Utgivaren måste använda denna token för att anropa de olika API: erna för SaaS-utförande. Denna token är endast giltig i en timme. 

Mer information om dessa tokens finns [Azure Active Directory åtkomsttoken](../../active-directory/develop/access-tokens.md).  Observera att i flödet under v1-slut punkts token används.

### <a name="get-the-token-with-an-http-post"></a>Hämta token med ett HTTP-inlägg

#### <a name="http-method"></a>HTTP-metod

Skicka<br>

##### <a name="request-url"></a>*Begärans-URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI-parameter*

|  Parameternamn    |  Krävs         |  Beskrivning |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  Sant      |  Klient-ID för det registrerade AAD-programmet. |

##### <a name="request-header"></a>*Begär ande huvud*

|  Huvudnamn       |  Krävs         |  Beskrivning |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  Sant      |  Innehålls typ som är associerad med begäran. Standardvärdet är `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Brödtext i begäran*

|  Egenskapsnamn     |  Krävs         |  Beskrivning |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  Sant      |  Typ av beviljande. Använd `"client_credentials"`. |
|  `client_id`       |  Sant      |  Klient/app-identifierare som är associerad med Azure AD-appen. |
|  `client_secret`   |  Sant      |  Hemlighet som är associerad med Azure AD-appen. |
|  `resource`        |  Sant      |  Mål resurs för vilken token begärs. Använd `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` eftersom Marketplace SaaS-API: n alltid är mål resursen i det här fallet. |

##### <a name="response"></a>*Response*

|  Namn     |  Typ         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  Begäran lyckades. |

##### <a name="tokenresponse"></a>*TokenResponse*

Exempel svar:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

`"access_token"`Fältvärdet i svaret är det `<access_token>` som du kommer att skicka som en Authorization-parameter när du anropar alla API: er för SaaS-uppfyllelse och Marketplace-avläsning.

## <a name="next-steps"></a>Nästa steg

Din Azure AD-säkrade app kan nu använda [API-version 2 för SaaS-Uppfyllaning](./pc-saas-fulfillment-api-v2.md).
