---
title: itsme OpenID Connect med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar Azure AD B2C-autentisering med itsme-OIDC med hjälp av client_secret användar flödes princip. itsme är en digital ID-app. Det gör att du kan logga in på ett säkert sätt utan kort läsare, lösen ord, tvåfaktorautentisering och flera PIN-koder.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba7875caa6a1db7638bfeafcfea1efa7b2462152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87489523"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Konfigurera itsme OpenID Connect (OIDC) med Azure Active Directory B2C

Med appen itsme Digital ID kan du logga in på ett säkert sätt utan kort läsare, lösen ord, tvåfaktorautentisering eller flera PIN-koder. Itsme-appen ger stark kundautentisering med en verifierad identitet. I den här artikeln lär du dig att integrera Azure AD B2C-autentisering med itsme OpenID Connect (OIDC) med hjälp av en användar flödes princip för klient hemlighet.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* [En Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.
* Ditt klient-ID, även kallat partner kod, som tillhandahålls av itsme.
* Service koden från itsme.
* Din klient hemlighet för ditt itsme-konto.

## <a name="scenario-description"></a>Scenariobeskrivning

![diagram över itsme-arkitektur](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Steg | Beskrivning |
|------|------|
|1     | På din webbplats eller i ditt program inkluderar du knappen **Logga in med itsme** genom att anpassa i Azure AD B2C användar flödet. Interaktions flödet startar när användaren klickar på den här knappen.  |
|2     | Azure AD B2C startar OpenID Connect-flödet genom att skicka en auktoriserad begäran till itsme-klientens hemliga API. En välkänd/OpenID-konfigurations slut punkt finns tillgänglig som innehåller information om slut punkterna.  |
|3     | Itsme-miljön omdirigerar användaren till sidan itsme identifiera dig, så att användaren kan fylla i sitt telefonnummer.  |
|4     | Itsme-miljön tar emot telefonnumret från användaren och kontrollerar att det är korrekt.  |
|5     | Om telefonnumret tillhör en aktiv itsme-användare skapas en åtgärd för itsme-appen.  |
|6     | Användaren öppnar itsme-appen, kontrollerar begäran och bekräftar åtgärden.  |
|7     |  Appen informerar itsme-miljön om att åtgärden har bekräftats. |
|8     |  Itsme-miljön returnerar OAuth-auktoriserad kod för att Azure AD B2C. |
|9     |  Med hjälp av auktoriserad kod kan Azure AD B2C en Tokenbegäran. |
| 10 | Itsme-miljön kontrollerar Tokenbegäran och om den fortfarande är giltig returneras OAuth-åtkomsttoken och ID-token som innehåller den begärda användar informationen. |
| 11 | Slutligen omdirigeras användaren till omdirigerings-URL: en som en autentiserad användare.  |
|   |   |

## <a name="onboard-with-itsme"></a>Publicera med itsme

1. Om du vill skapa ett konto med itsme går du till itsme på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

2. Aktivera ditt itsme-konto genom att skicka ett e-postmeddelande till onboarding@itsme.be . Du får en **partner kod** och en **service kod** som behövs för din B2C-installation.

3. När du har aktiverat ditt itsme-partner konto får du ett e-postmeddelande med en eng ång slö anslutning till **klient hemligheten**.

4. Följ anvisningarna på [itsme](https://business.itsme.be/en) för att slutföra konfigurationen.

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Konfigurera en ny identitets leverantör i Azure AD B2C

> [!NOTE]
> Om du inte redan har en, [skapar du en Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

1. Kontrol lera att du använder den katalog som innehåller Azure AD B2C klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.

2. Under **Azure-tjänster** väljer du **Azure AD B2C** (eller Välj **fler tjänster** och använder sökrutan **alla tjänster** för att söka efter *Azure AD B2C*).

3. Välj **identitets leverantörer** och välj sedan **ny OpenID Connect-Provider**.

4. Fyll i formuläret med följande information:

   |Egenskap | Värde |
   |------------ |------- |
   | Namn | itsme |
   | Metadata-URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>där `<environment>` är antingen `e2e` (test miljö) eller `prd` (produktion)  |
   | ClientID     | Ditt **klient-ID**, även kallat **partner kod**  |
   | Client Secret (Klienthemlighet) | Din **client_secret** |
   | Omfång  | OpenID-tjänst: YOURSERVICECODE Profile e-mail [Phone] [address]  |
   |Svarstyp | kod |
   |Svars läge | DocumentDB |
   |Domän tips | *Du kan lämna detta tomt* |
   |UserID | Build |
   |Visningsnamn | name |
   |Tilltalsnamn | given_name |
   |Efternamn | family_name |
   |E-post | e-post|

5. Välj **Spara**.

### <a name="configure-a-user-flow"></a>Konfigurera ett användar flöde

1. I Azure AD B2C klient väljer du **användar flöden** under **principer**.

2. Välj **nytt användar flöde**.

3. Välj **Registrera dig och logga** in, Välj en version och välj sedan **skapa**.

4. Ange ett **namn**.

5. I avsnittet **identitets leverantörer** väljer du **itsme**.

6. Välj **Skapa**.

7. Öppna det nyligen skapade användar flödet genom att välja användar flödets namn.

8. Välj **Egenskaper** och justera följande värden:

   * Ändra **åtkomst & livstid för ID-token (minuter)** till **5**.
   * Ändra tids **perioden för glidande uppdateringstoken** till **ingen förfallo** tid.

### <a name="register-an-application"></a>Registrera ett program

1. I B2C-klienten under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.

2. Ange ett **namn** för programmet och ange **omdirigerings-URI**. I test syfte anger du `https://jwt.ms` .

3. Se till att Multi-Factor Authentication är **inaktiverat**.

4. Välj **Register** (Registrera).

   a. I test syfte väljer du **autentisering** och under **implicit tilldelning** markerar du kryss rutorna **åtkomsttoken** och **ID-token** .  

   b. Välj **Spara**.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. I B2C-klienten under **principer** väljer du **användar flöden**.

2. Välj ditt tidigare skapade användar flöde.

3. Välj **Kör användar flöde**.

   a. **Program**: *Välj den registrerade appen*

   b. **Svars-URL**: *Välj omdirigerings-URL*

4. Sidan itsme **identifiera dig själv** visas.  

5. Ange ditt mobiltelefon nummer och välj **Skicka**.

6. Bekräfta åtgärden i itsme-appen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

* [Anpassade principer i Azure AD B2C](custom-policy-overview.md)

* [Kom igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md?tabs=applications)