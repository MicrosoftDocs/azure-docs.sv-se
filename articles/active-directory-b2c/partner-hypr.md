---
title: Självstudie för att konfigurera Azure Active Directory B2C med HYPR
titleSuffix: Azure AD B2C
description: Självstudie för att konfigurera Azure Active Directory B2C med Hypr för äkta lösen ords lös starkt kundautentisering
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953771"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera HYPR med Azure Active Directory B2C

I den här exempel guiden ger vi vägledning om hur du konfigurerar Azure AD B2C med [HYPR](https://get.hypr.com). Med Azure AD B2C som identitets leverantör kan du integrera HYPR med något av dina kund program för att ge användarna sann autentisering med lösen ord. HYPR ersätter lösen ord med krypteringar med offentliga nycklar som eliminerar bedrägerier, nätfiske och åter användning av autentiseringsuppgifter.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](./tutorial-create-tenant.md). Klienten är länkad till din Azure-prenumeration.

- En HYPR Cloud-klient, får du ett kostnads fritt [utvärderings konto](https://get.hypr.com/free-trial).

- En användares mobila enhet som registrerats med hjälp av HYPR REST API: er eller HYPR-Enhetshanteraren i HYPR-klienten. Du kan till exempel använda [HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) för att utföra den här uppgiften.

## <a name="scenario-description"></a>Scenariobeskrivning

HYRP-integreringen innehåller följande komponenter:

- Azure AD B2C – auktoriseringsservern som ansvarar för att verifiera användarens autentiseringsuppgifter, även kallat identitets leverantören

- Webb program och mobilappar – dina mobil-eller webb program som du väljer att skydda med HYPR och Azure AD B2C. HYPR tillhandahåller en robust mobil-SDK även en mobilapp som du kan använda på iOS-och Android-plattformar för att göra en lösen ords lös autentisering.

- HYPR Mobile-appen – HYPR-mobilappen kan användas för att köra det här exemplet om du föredrar att inte använda de mobila SDK: erna i dina egna mobila program.

- REST-API: er för HYPR – du kan använda HYPR-API: er för att göra både registrering och autentisering av användarens enheter. Dessa API: er hittar du [här](https://apidocs.hypr.com).

I följande arkitektur diagram visas implementeringen.

![Skärm bild för hypr-Architecture-diagram](media/partner-hypr/hypr-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | Användaren kommer till inloggnings sidan. Användare väljer inloggning/registrera och anger användar namn på sidan.
| 2. | Programmet skickar användarattribut till Azure AD B2C för att identifiera verifiering.
| 3. | Azure AD B2C samlar in användarattribut och skickar attributen till HYPR för att autentisera användaren via HYPR-mobilappen.
| 4. | HYPR skickar ett push-meddelande till den registrerade användarens mobila enhet för en certifierad autentisering online (FIDO) för snabb identitet Online (). Det kan vara en användares finger utskrift, bio metrisk eller decentraliserad PIN-kod.  
| 5. | När användaren har godkänt push-meddelandet är användaren antingen beviljad eller nekad åtkomst till kund programmet baserat på verifierings resultatet.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurera principen för Azure AD B2C

1. Gå till principen för [Azure AD B2C HYPR](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) i mappen princip.

2. Följ det här [dokumentet](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) för att ladda ned [LocalAccounts start paket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Konfigurera principen för Azure AD B2C klient.

>[!NOTE]
>Uppdatera de angivna principerna så att de relaterar till din angivna klient.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten och under principer väljer du **identitets miljö ramverk**.

2. Välj din tidigare skapade **SignUpSignIn**.

3. Välj **Kör användar flöde** och välj inställningarna:

   a. **Program**: Välj den registrerade appen (EXEMPLET är JWT)

   b. **Svars-URL**: Välj **omdirigerings-URL**

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto

5. HYPR kommer att anropas under flödet efter att användarattribut har skapats. Om flödet är ofullständigt, kontrollerar du att användaren inte har sparats i katalogen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)