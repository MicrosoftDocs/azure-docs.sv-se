---
title: Konfigurera Azure AD B2C lokal konto identitets leverantör
titleSuffix: Azure AD B2C
description: Definiera identitets typer som används kan användas för att registrera dig eller logga in (e-post, användar namn, telefonnummer) i Azure Active Directory B2C klient organisationen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2baff33d9e91e1b5259d79eca0a22535c00f419
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555238"
---
# <a name="set-up-the-local-account-identity-provider"></a>Konfigurera den lokala konto identitets leverantören

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C tillhandahåller olika sätt för användare att autentisera en användare. Användare kan logga in på ett lokalt konto genom att använda användar namn och lösen ord, telefon verifiering (även kallat lösen ord för mindre autentisering) eller sociala identitets leverantörer. E-postregistrering är aktiverat som standard i inställningarna för den lokala kontots identitetsprovider. 

I den här artikeln beskrivs hur användare skapar sina konton lokalt för den här Azure AD B2C klienten. För sociala eller företags identiteter, där identiteten för användaren hanteras av en federerad identitets leverantör som Facebook och Google, se [lägga till en identitets leverantör](add-identity-provider.md).

## <a name="email-sign-in"></a>Inloggning för e-post

Med alternativet e-post kan användarna logga in/upp med sina e-postadresser och lösen ord:

- **Inloggning** uppmanas användarna att ange sina e-postmeddelanden och lösen ord.
- **Registrera** dig, användarna uppmanas att ange en e-postadress som verifieras vid registreringen (valfritt) och blir deras inloggnings-ID. Användaren anger sedan all annan information som begärs på registrerings sidan, till exempel visnings namn, tilldelat namn och efter namn. Välj sedan Fortsätt för att skapa kontot.
- **Lösen ords återställning**, användare måste ange och verifiera sin e-post, och användaren kan återställa lösen ordet

![Registrering av e-post eller inloggnings upplevelse](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Inloggning av användar namn

Med alternativet användare kan användarna logga in/upp med ett användar namn och lösen ord:

- **Inloggning**: användarna uppmanas att ange sina användar namn och lösen ord.
- **Registrering**: användarna uppmanas att ange ett användar namn, vilket kommer att bli sitt inloggnings-ID. Användarna uppmanas också att ange en e-postadress som kommer att verifieras vid registreringen. E-postadressen kommer att användas under ett flöde för lösen ords återställning. Användaren anger en annan information som har begärts på registrerings sidan, till exempel visnings namn, tilldelat namn och efter namn. Användaren väljer sedan Fortsätt för att skapa kontot.
- **Lösen ords återställning**: användare måste ange sitt användar namn och tillhör ande e-postadress. E-postadressen måste verifieras, och användaren kan återställa lösen ordet.

![Användar gränssnitt för registrering eller inloggning](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Telefonin loggning (förhands granskning)

Lösenordsbaserad autentisering är en typ av autentisering där en användare inte behöver logga in med sitt lösen ord. Med telefonin loggning och inloggning kan användaren registrera sig för appen med hjälp av ett telefonnummer som primär inloggnings identifierare. Användaren får följande erfarenhet vid registrering och inloggning:

- **Inloggning**: om användaren har ett befintligt konto med telefonnummer som identifierare anger användaren deras telefonnummer och väljer *Logga* in. De bekräftar land och telefonnummer genom att välja *Fortsätt* och en verifierings kod för eng ång slö tiden skickas till sin telefon. Användaren anger verifierings koden och väljer *Fortsätt* för att logga in.
- **Registrera** dig: om användaren inte redan har ett konto för ditt program kan de skapa ett genom att klicka på länken *Registrera dig nu* . 
    1. En registrerings sida visas där användaren väljer *land*, anger deras telefonnummer och väljer *Skicka kod*. 
    1. En verifierings kod vid enstaka tidpunkt skickas till användarens telefonnummer. Användaren anger *verifierings koden* på registrerings sidan och väljer sedan *verifiera kod*. (Om användaren inte kan hämta koden kan han eller hon välja *Skicka ny kod*). 
    1. Användaren anger en annan information som har begärts på registrerings sidan, till exempel visnings namn, tilldelat namn och efter namn. Välj sedan Fortsätt.
    1. Sedan uppmanas användaren att ange ett **återställnings-e-postmeddelande**. Användaren anger sin e-postadress och väljer sedan *Skicka verifierings kod*. En kod skickas till användarens inkorg för e-post, som de kan hämta och ange i rutan verifierings kod. Sedan väljer användaren verifiera kod.
    1. När koden har verifierats väljer användaren *skapa* för att skapa sitt konto. 

![Registrering av telefon eller inloggning](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Priser

Eng ång slö sen ord skickas till användarna med SMS-textmeddelanden. Beroende på din mobil nätverks operatör kan du debiteras för varje meddelande som skickas. Information om priser finns i avsnittet **separata avgifter** i [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> Multi-Factor Authentication (MFA) inaktive ras som standard när du konfigurerar ett användar flöde med telefon registrering. Du kan aktivera MFA i användar flöden med telefon registrering, men eftersom ett telefonnummer används som primär identifierare är email eng ång slö sen ord det enda alternativet som är tillgängligt för den andra autentiserings faktorn.

### <a name="phone-recovery"></a>Telefon återställning

När du aktiverar telefon registrering och inloggning för dina användar flöden, är det också en bra idé att aktivera funktionen för återställning av e-post. Med den här funktionen kan en användare ange en e-postadress som kan användas för att återställa sitt konto när de inte har sin telefon. Den här e-postadressen används endast för konto återställning. Den kan inte användas för att logga in.

- När återställnings-e-postmeddelandet är **på** uppmanas en användare som registrerar sig för första gången att verifiera ett e-postmeddelande om säkerhets kopiering. En användare som inte har tillhandahållit ett återställnings-e-postmeddelande innan uppmanas att verifiera en e-postkopia under nästa inloggning.

- När återställnings-e-post är **inaktive rad** visas inte en användare som loggar in eller loggar in på återställnings meddelandet.
 
Följande skärm dum par visar telefon återställnings flödet:

![Användar flöde för telefon återställning](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Inloggning via telefon eller e-post (för hands version)

Du kan välja att kombinera [telefonin loggning](#phone-sign-in-preview)och [e-postinloggningen](#email-sign-in). På sidan för registrering eller inloggning kan användaren ange ett telefonnummer eller en e-postadress. Azure AD B2C tar användaren till motsvarande flöde baserat på användarindata. 

![Telefon-eller e-postregistrering eller inloggnings upplevelse](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Konfigurera inställningar för identitets leverantör för lokalt konto

Du kan konfigurera de lokala identitets leverantörer som är tillgängliga för användning i ett användar flöde genom att aktivera eller inaktivera providers (e-post, användar namn eller telefonnummer).  Du kan ha mer än en lokal identitetsprovider aktive rad på klient nivå.

Ett användar flöde kan bara konfigureras för att använda en av de lokala kontots identitets leverantörer vid ett och samma tillfälle. Varje användar flöde kan ha en annan identitets uppsättning för lokala konton, om mer än en har Aktiver ATS på klient nivå.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **Hantera** väljer du **identitets leverantörer**.
1. I listan identitetsprovider väljer du **lokalt konto**.
1. På sidan **Konfigurera lokala IDP** väljer du minst en av de tillåtna identitets typer som användare kan använda för att skapa sina lokala konton i Azure AD B2C klient organisationen.
1. Välj **Spara**.

## <a name="configure-your-user-flow"></a>Konfigurera ditt användar flöde

1. På den vänstra menyn i Azure Portal väljer du **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden (principer)**.
1. Välj det användar flöde som du vill konfigurera registrerings-och inloggnings upplevelsen för.
1. Välj **identitets leverantörer**
1. Under **lokala konton** väljer du något av följande: **e-postregistrering**,  **användar-ID-registrering**, **telefon registrering**, **telefon-/e-postregistrering** eller **ingen**.

### <a name="enable-the-recovery-email-prompt"></a>Aktivera meddelande om återställnings-e-post

Om du väljer alternativet för **registrering av telefon** **/e-post** , aktiverar du meddelandet återställnings-e-post.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. I Azure AD B2C väljer du **användar flöden** under **principer**.
1. Välj användar flödet i listan.
1. Under **Inställningar** väljer du **Egenskaper**.
1. Bredvid **Aktivera återställnings-e-postprompt för telefonnummer registrering och inloggning (för hands version)** väljer du:
   - **På** för att visa en varning om återställnings-e-post under både registrering och inloggning.
   - **Off** för att dölja återställnings-e-postprompten.
1. Välj **Spara**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Hämta start paketet

Anpassade principer är en uppsättning XML-filer som du överför till din Azure AD B2C-klient för att definiera användar resor. Vi tillhandahåller start paket med flera i förväg utformade principer. Hämta relevant start paket: 

- [Inloggning för e-post](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Inloggning av användar namn](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Telefonin loggning](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Välj principen för [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) förlitande part. 
- [Logga in via telefon eller e-post](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Välj principen för [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) förlitande part.

När du har hämtat start paketet.

1. Ersätt strängen `yourtenant` med namnet på din Azure AD B2C-klient i varje fil. Om namnet på din B2C-klient till exempel är *contosob2c*, blir alla instanser av `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .

1. Slutför stegen i avsnittet [Lägg till program-ID: n i avsnittet anpassad princip](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) i [komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md). Du kan till exempel uppdatera `/phone-number-passwordless/` **`Phone_Email_Base.xml`** med **program-ID: n** för de två program som du registrerade när du slutfört kraven, *IdentityExperienceFramework* och *ProxyIdentityExperienceFramework*.
1. Ladda upp principfiler

::: zone-end

## <a name="next-steps"></a>Nästa steg

- [Lägg till externa identitets leverantörer](add-identity-provider.md)
- [Skapa ett användarflöde](tutorial-create-user-flows.md)
