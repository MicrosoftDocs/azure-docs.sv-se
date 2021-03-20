---
title: Trusona och Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du lägger till Trusona som en identitets leverantör på Azure AD B2C för att aktivera autentisering med lösen ord.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99368da8b4018e93ad537e4722ffefd476e61291
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99573695"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Integrera Trusona med Azure Active Directory B2C

Trusona är en oberoende program varu leverantör (ISV) som hjälper till att säkra inloggning genom att aktivera autentisering med lösen ord, Multi-Factor Authentication och digital License scanning. I den här artikeln får du lära dig hur du lägger till Trusona som en identitets leverantör i Azure AD B2C att aktivera lösen ords lös autentisering.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* [En Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.
* Ett [utvärderings konto](https://www.trusona.com/) på Trusona

## <a name="scenario-description"></a>Scenariobeskrivning

I det här scenariot fungerar Trusona som en identitets leverantör för Azure AD B2C att aktivera lösen ords lös autentisering. Följande komponenter utgör lösningen:

* En Azure AD B2C kombinerad inloggnings-och registrerings princip
* Trusona har lagts till Azure AD B2C som identitets leverantör
* Den nedladdnings bara Trusona-appen

![Diagram över Trusona-arkitektur](media/partner-trusona/trusona-architecture-diagram.png)

| Steg | Beskrivning |
|------|------|
|1     | En användare försöker logga in på eller registrera sig med programmet. Användaren autentiseras via Azure AD B2C registrerings-och inloggnings princip. Under registreringen används användarens tidigare verifierade e-postadress från Trusona-appen.     |
|2     | Azure B2C omdirigerar användaren till identitets leverantören Trusona OpenID Connect (OIDC) med det implicita flödet.     |
|3     | För Station ära PC-baserade inloggningar visar Trusona en unik, tillstånds lös, animerad och dynamisk QR-kod för skanning med Trusona-appen. För Mobile-baserade inloggningar använder Trusona en "djup länk" för att öppna Trusona-appen. Dessa två metoder används för enheten och slutligen användar identifiering.     |
|4     | Användaren genomsöker den visade QR-koden med Trusona-appen.     |
|5     | Användar kontot finns i moln tjänsten Trusona och autentiseringen förbereds.     |
|6     | Moln tjänsten Trusona utfärdar en autentiserings-Challenge till användaren via ett push-meddelande som skickas till Trusona-appen:<br>a. Användaren uppmanas att autentisera utmaningen. <br> b. Användaren väljer att acceptera eller avvisa utmaningen. <br> c. Användaren uppmanas att använda OS-säkerhet (till exempel bio metrisk, lösen ord, PIN-kod eller mönster) för att bekräfta och signera utmaningen med en privat nyckel i säker enklaven/betrodd körnings miljö. <br> d. Trusona-appen genererar en dynamisk anti-omuppspelnings-nyttolast baserat på parametrarna för autentiseringen i real tid. <br> e. Hela svaret signeras (en gång i sekunden) av en privat nyckel i den säkra enklaven/betrodda körnings miljön och returneras till moln tjänsten Trusona för verifiering.      |
|7     |  Moln tjänsten Trusona omdirigerar användaren tillbaka till det initierande programmet med en id_token. Azure AD B2C verifierar id_token med hjälp av Trusona publicerade OpenID-konfigurationen enligt konfigurationen under installationen av identitetsprovider.    |
|  |  |

## <a name="onboard-with-trusona"></a>Publicera med Trusona

1. Fyll i [formuläret](https://www.trusona.com/) för att skapa ett Trusona-konto och kom igång.

2. Ladda ned Trusona-mobilappen från App Store. Installera appen och registrera din e-postadress.

3. Verifiera din e-post via den säkra "magisk-länken" som skickas av program varan.  

4. Gå till [instrument panelen för Trusona-utvecklare](https://dashboard.trusona.com) för självbetjäning.

5. Välj **Jag är redo** och autentisera dig med din Trusona-app.

6. Välj **OIDC-integreringar** i den vänstra navigerings panelen.

7. Välj **skapa OpenID Connect integration**.

8. Ange ett **namn** på ditt val och Använd domän informationen som tidigare angavs (till exempel contoso) i **fältet värddator för klient omdirigering**.  

   > [!NOTE]
   > Azure Active Directoryens första domän namn används som värd för klienten som omdirigeras.

9. Följ anvisningarna i [integrerings guiden för Trusona](https://docs.trusona.com/integrations/aad-b2c-integration/). När du uppmanas använder du det första domän namnet (till exempel contoso) som refereras till i föregående steg.  

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Lägg till en ny identitetsprovider

> [!NOTE]
> Om du inte redan har en, [skapar du en Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.

2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.

4. Gå till **instrument panelen**  >  **Azure Active Directory B2C**  >  **identitets leverantörer**.

3. Välj **identitets leverantörer**.

4. Välj **Lägg till**.

### <a name="configure-an-identity-provider"></a>Konfigurera en identitets leverantör  

1. Välj **typ av identitetsprovider**  >  **OpenID Connect (för hands version)**.

2. Konfigurera identitets leverantören genom att fylla i formuläret:  

   | Egenskap | Värde  |
   | :--- | :--- |
   | Metadata-URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | Klient-ID | Skickas via e-post från Trusona |
   | Omfång | E-OpenID profil |
   | Svarstyp | Id_token |
   | Svars läge  | Form_post |

3. Välj **OK**.  

4. Välj **mappa den här identitets leverantörens anspråk**.  

5. Fyll i formuläret för att mappa identitets leverantören:

   | Egenskap | Värde  |
   | :--- | :--- |
   | UserID | Build  |
   | Visningsnamn | smek namn |
   | Tilltalsnamn | given_name |
   | Efternamn | Family_name |
   | Svars läge | e-post |

6. Klicka på **OK** för att slutföra installationen av den nya OIDC Identity-providern.

### <a name="create-a-user-flow-policy"></a>Skapa en princip för användar flöde

Nu bör du se Trusona som en **ny OpenID Connect** -identitetsprovider som anges i dina B2C Identity providers.

1. I Azure AD B2C klient väljer du **användar flöden** under **principer**.

1. Välj **nytt användar flöde**.

1. Välj **Registrera dig och logga** in, Välj en version och välj sedan **skapa**.

1. Ange ett **namn** för principen.

1. I avsnittet **identitets leverantörer** väljer du den nyligen skapade **Trusona Identity-providern**.

   > [!NOTE]
   > Eftersom Trusona är Multi-Factor, är det bäst att lämna Multi-Factor Authentication inaktiverat.

1. Välj **Skapa**.

1. Under **användarattribut och anspråk** väljer du **Visa fler**. I formuläret väljer du minst ett attribut som du angav under installationen av identitets leverantören i det tidigare avsnittet.

1. Välj **OK**.  

### <a name="test-the-policy"></a>Testa principen

1. Välj din nyligen skapade princip.

2. Välj **Kör användar flöde**.

3. I formuläret anger du svars-URL: en.

4. Välj **Kör användar flöde**. Du bör omdirigeras till Trusona OIDC-gatewayen. På Trusona-gatewayen genomsöker du den visade säkra QR-koden med Trusona-appen eller med en anpassad app med hjälp av Trusona Mobile SDK.

5. När du har skannat den säkra QR-koden bör du omdirigeras till svars-URL: en som du definierade i steg 3.

## <a name="next-steps"></a>Nästa steg  

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](custom-policy-overview.md)

- [Kom igång med anpassade principer i AAD B2C](custom-policy-get-started.md?tabs=applications)
