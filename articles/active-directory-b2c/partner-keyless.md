---
title: Självstudie för att konfigurera Keyless med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Själv studie kurs om hur du konfigurerar Keyless med Azure Active Directory B2C för lösen ords bevarad autentisering
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c8a5666d373852da5ff79490f435b2d66d5cc6e0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090374"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Självstudie: Konfigurera Keyless med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du konfigurerar Azure Active Directory (AD) B2C med [Keyless](https://keyless.io/). Med Azure AD B2C som identitets leverantör kan du integrera Keyless med något av dina kund program för att ge användarna sann autentisering med lösen ord.

Keyless **Keyless Zero-Knowledge bio metrisk (ZKB™)** ger lösen ords löst multifaktorautentisering som eliminerar bedrägerier, nätfiske och åter användning av autentiseringsuppgifter – allt samtidigt som du förbättrar kund upplevelsen och skyddar deras integritet.

## <a name="pre-requisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Klienten måste vara länkad till din Azure-prenumeration.

- En Keyless Cloud-klient, får du ett kostnads fritt [utvärderings konto](https://keyless.io/go).

- Keyless Authenticator-appen installeras på användarens enhet.

## <a name="scenario-description"></a>Scenariobeskrivning

Keyless-integreringen innehåller följande komponenter:

- Azure AD B2C – auktoriseringsservern som ansvarar för att verifiera användarens autentiseringsuppgifter, även kallat identitets leverantören.

- Webb program och mobilappar – dina mobil-eller webb program som du väljer att skydda med Keyless och Azure AD B2C.

- Keyless-mobilappen – Keyless-mobilappen kommer att användas för autentisering till de Azure AD B2C aktiverade programmen.

I följande arkitektur diagram visas implementeringen.

![Bilden visar Keyless-arkitektur diagram](./media/partner-keyless/keyless-architecture-diagram.png)

|Steg | Description |
|:-----| :-----------|
| 1. | Användaren kommer till inloggnings sidan. Användare väljer inloggning/registrera och anger användar namnet
| 2. | Programmet skickar användarattribut till Azure AD B2C för identitets verifiering.
| 3. | Azure AD B2C samlar in användarattribut och skickar attributen till Keyless för att autentisera användaren via Keyless-mobilappen.
| 4. | Keyless skickar ett push-meddelande till den registrerade användarens mobila enhet för en sekretess-bevara autentisering i form av en bio metrisk sökning i ansikts läge.
| 5. | När användaren svarar på push-meddelandet beviljas eller nekas användaren åtkomst till kund programmet baserat på verifierings resultatet.

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Lägg till en ny identitetsprovider

Följ dessa steg om du vill lägga till en ny identitetsprovider:

1. Logga in på **[Azure Portal](https://portal.azure.com/#home)** som global administratör för din Azure AD B2C-klient.

2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.

4. Gå till **instrument panelen**  >  **Azure Active Directory B2C**  >   **identitets leverantörer**

5. Välj **identitets leverantörer**.

6. Välj **Lägg till**.

### <a name="configure-an-identity-provider"></a>Konfigurera en identitets leverantör

Följ dessa steg om du vill konfigurera en identitets leverantör:

1. Välj **typ av identitetsprovider**  >  **OpenID Connect (för hands version)**
2. Konfigurera identitets leverantören genom att fylla i formuläret:

   |Egenskap | Värde |
   |:-----| :-----------|
   | Namn   | Keyless |
   | Metadata-URL | Infoga URI: n för den värdbaserade Keyless för autentisering, följt av den angivna sökvägen, till exempel https://keyless.auth/.well-known/openid-configuration |
   | Client Secret (Klienthemlighet) | Hemligheten som är associerad med Keyless-autentiseringen, inte samma som den som kon figurer ATS tidigare. Infoga en komplex sträng som du väljer. Den här hemligheten kommer att användas senare i Keyless container-konfigurationen.|
   | Klient-ID | Klientens ID. Detta ID kommer att användas senare i Keyless container-konfigurationen.|
   | Omfång | OpenID |
   | Svarstyp | id_token |
   | Svars läge | form_post|

3. Välj **OK**.

4. Välj **mappa den här identitets leverantörens anspråk**.

5. Fyll i formuläret för att mappa identitets leverantören:

   |Egenskap | Värde |
   |:-----| :-----------|
   | UserID    | Från prenumeration |
   | Visningsnamn | Från prenumeration |
   | Svars läge | Från prenumeration |

6. Klicka på **Spara** för att slutföra installationen för din nya ID-provider för öppen ID Connect (OIDC).

### <a name="create-a-user-flow-policy"></a>Skapa en princip för användar flöde

Nu bör du se Keyless som en ny OIDC som visas i dina B2C Identity providers.

1. I Azure AD B2C klient väljer du **användar flöden** under **principer**.

2. Välj **nytt** användar flöde.

3. Välj **Registrera dig och logga** in, Välj en **version** och välj sedan **skapa**.

4. Ange ett **namn** för principen.

5. I avsnittet identitets leverantörer väljer du den nyligen skapade Keyless Identity-providern.

6. Konfigurera parametrarna för ditt användar flöde. Infoga ett namn och välj den identitetsprovider som du har skapat. Du kan också lägga till e-postadress. I det här fallet dirigerar Azure inte om inloggnings proceduren direkt till Keyless i stället visas en skärm där användaren kan välja det alternativ som ska användas.

7. Lämna **Multi-Factor Authentication** -fältet som det är.

8. Välj **tvinga villkorliga åtkomst principer**

9. Under **användarattribut och token-anspråk** väljer du **e-postadress** i alternativet samla in attribut. Du kan lägga till alla attribut som Azure Active Directory kan samla in om användaren tillsammans med de anspråk som Azure AD B2C kan returnera till klient programmet.

10. Välj **Skapa**.

11. När du har skapat en lyckad installation väljer du ditt nya **användar flöde**.

12. Välj **program anspråk** i den vänstra panelen. Under alternativ, kryssa i rutan **e-post** och välj **Spara**.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten och under principer väljer du identitets miljö ramverk.

2. Välj din tidigare skapade SignUpSignIn.

3. Välj Kör användar flöde och välj inställningarna:

   a. Program: Välj den registrerade appen (exemplet är JWT)

   b. Svars-URL: Välj omdirigerings-URL

   c. Välj Kör användar flöde.

4. Gå igenom registrerings flödet och skapa ett konto

5. Keyless kommer att anropas under flödet efter att användarattribut har skapats. Om flödet är ofullständigt, kontrollerar du att användaren inte har sparats i katalogen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Kom igång med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
