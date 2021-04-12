---
title: Konfigurera den lokala konto identitets leverantören
titleSuffix: Azure AD B2C
description: Definiera de identitets typer som du kan använda (e-post, användar namn, telefonnummer) för autentisering med lokalt konto när du konfigurerar användar flöden i Azure Active Directory B2C klient organisationen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd21c1dca0dd54331780ba98f9c53d5b99d6b4e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557232"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Konfigurera telefonin loggning och inloggning för användar flöden (förhands granskning)

> [!NOTE]
> Telefonin loggning och inloggning och återställnings-och återställnings funktioner för användar flöden finns i offentlig för hands version.

Förutom e-post och användar namn kan du aktivera telefonnumret som ett registrerings alternativ för hela klienten genom att lägga till telefonin loggning och inloggning i din lokala konto identitets leverantör. När du har aktiverat telefon registrering och inloggning för lokala konton kan du lägga till telefonin loggning till dina användar flöden.

Att konfigurera telefon registrering och inloggning i ett användar flöde omfattar följande steg:

- [Konfigurera telefon registrering och inloggnings klient – bred](#configure-phone-sign-up-and-sign-in-tenant-wide) i din lokala konto identitets leverantör för att acceptera ett telefonnummer som användarens identitet. 

- [Lägg till telefon registrering i ditt användar flöde](#add-phone-sign-up-to-a-user-flow) så att användarna kan registrera sig för ditt program med hjälp av deras telefonnummer.

- [Aktivera prompten för återställnings-e-post (för hands version)](#enable-the-recovery-email-prompt-preview) om du vill att användarna ska kunna ange ett e-postmeddelande som kan användas för att återställa kontot när de inte har

Multi-Factor Authentication (MFA) inaktive ras som standard när du konfigurerar ett användar flöde med telefon registrering. Du kan aktivera MFA i användar flöden med telefon registrering, men eftersom ett telefonnummer används som primär identifierare är email eng ång slö sen ord det enda alternativet som är tillgängligt för den andra autentiserings faktorn.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Konfigurera telefon registrering och inloggnings klient-wide

E-postregistrering är aktiverat som standard i inställningarna för den lokala kontots identitetsprovider. Du kan ändra de identitets typer som du har stöd för i din klient genom att markera eller avmarkera e-postregistrering, användar namn eller telefonnummer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.

4. Under **Hantera** väljer du **identitets leverantörer**.

5. I listan identitetsprovider väljer du **lokalt konto**.

   ![Identitets leverantörer Välj lokalt konto](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. På sidan **Konfigurera lokala IDP** ser du till att **telefon** är valt som en av de tillåtna identitets typer som användare kan använda för att skapa sina lokala konton i din Azure AD B2C klient.

   ![Välj tillåtna identitets typer](media/phone-authentication-user-flows/configure-local-idp.png)

1. Välj **Spara**.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Lägg till telefon för registrering i ett användar flöde

När du har lagt till telefon registrering som ett identitets alternativ för lokala konton kan du lägga till det i användar flöden så länge de är de senaste **rekommenderade** användar flödes versionerna. Följande är ett exempel som visar hur du lägger till telefon registrering till nya användar flöden. Men du kan också lägga till telefon registrering till befintliga rekommenderade versions användar flöden (Välj **användarflöden**  >  *användar flödets namn*  >  **identitet leverantörer**  >  **lokal konto telefon registrering**). 

Här är ett exempel som visar hur du lägger till telefon registrering i ett nytt användar flöde.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

    ![B2C-klient, katalog och prenumerations fönstret Azure Portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. I Azure Portal söker du efter och väljer **Azure AD B2C**.
4. Under **principer** väljer du **användar flöden** och väljer sedan **nytt användar flöde**.

    ![Sidan användar flöden i portalen med knappen nytt användar flöde markerat](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. På sidan **skapa ett användar flöde** väljer du användar flödet **Registrera och logga** in.

    ![Välj en användar flödes sida med registrering och inloggnings flöde markerat](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**. ([Läs mer](user-flow-versions.md) om användar flödes versioner.)

    ![Knappen Skapa användar flöde](./media/phone-authentication-user-flows/select-version.png)

7. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
8. I avsnittet **identitets leverantörer** under **lokala konton** väljer du **telefon registrering**.

   ![Alternativ för registrering av användar flöde-telefon valt](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. Under **sociala identitets leverantörer** väljer du andra identitets leverantörer som du vill tillåta för det här användar flödet.

   > [!NOTE]
   > Multi-Factor Authentication (MFA) är inaktiverat som standard för registrering av användar flöden. Du kan aktivera MFA för ett telefon flöde för registrering, men eftersom ett telefonnummer används som primär identifierare är email eng ång slö sen ord det enda alternativet som är tillgängligt för den andra autentiserings faktorn.

1. I avsnittet **användarattribut och token-anspråk** väljer du de anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj till exempel **Visa mer** och välj sedan attribut och anspråk för **land/region**, **visnings namn** och **post nummer**. Välj **OK**.

1. Välj **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* anpassningsprefix automatiskt till namnet.

## <a name="enable-the-recovery-email-prompt-preview"></a>Aktivera meddelandet återställnings-e-post (för hands version)

När du aktiverar telefon registrering och inloggning för dina användar flöden, är det också en bra idé att aktivera funktionen för återställning av e-post. Med den här funktionen kan en användare ange en e-postadress som kan användas för att återställa sitt konto när de inte har sin telefon. Den här e-postadressen används endast för konto återställning. Den kan inte användas för att logga in.

- När återställnings-e-postmeddelandet är **på** uppmanas en användare som registrerar sig för första gången att verifiera ett e-postmeddelande om säkerhets kopiering. En användare som inte har tillhandahållit ett återställnings-e-postmeddelande innan uppmanas att verifiera en e-postkopia under nästa inloggning.

- När återställnings-e-post är **inaktive rad** visas inte en användare som loggar in eller loggar in på återställnings meddelandet.
 
Du kan aktivera meddelandet återställnings-e-post i egenskaperna för användar flödet.

> [!NOTE]
> Innan du börjar ska du kontrol lera att du har [lagt till telefon registrering till ditt användar flöde](#add-phone-sign-up-to-a-user-flow) enligt beskrivningen ovan.

### <a name="to-enable-the-recovery-email-prompt"></a>Aktivera prompten för återställnings-e-post

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
3. I Azure Portal söker du efter och väljer **Azure AD B2C**.
4. I Azure AD B2C väljer du **användar flöden** under **principer**.
5. Välj användar flödet i listan.
6. Under **Inställningar** väljer du **Egenskaper**.
7. Bredvid **Aktivera återställnings-e-postprompt för telefonnummer registrering och inloggning (för hands version)** väljer du:

   - **På** för att visa en varning om återställnings-e-post under både registrering och inloggning.
   - **Off** för att dölja återställnings-e-postprompten.

    ![Användaren flödar egenskaper med aktivera e-poståterställning aktiverat](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Välj **Spara**.

### <a name="to-test-the-recovery-email-prompt"></a>Testa återställnings-e-postmeddelandet

När du har aktiverat telefon registrering och inloggning och återställnings-e-postmeddelandet i ditt användar flöde kan du använda **Kör användar flöde** för att testa användar upplevelsen.

1. Välj **principer**  >  **användar flöden** och välj sedan det användar flöde som du skapade. På sidan Översikt över användar flöde väljer du **Kör användar flöde**.

2. För **program** väljer du det webb program som du registrerade i steg 1. **Svars-URL: en** ska visas `https://jwt.ms` .

3. Välj **Kör användar flöde** och kontrol lera följande beteende:

   - En användare som registrerar sig för första gången uppmanas att ange ett återställnings-e-postmeddelande. 
   - En användare som redan har registrerat sig men inte tillhandahållit ett återställnings meddelande uppmanas att ange ett vid inloggning.

4. Ange en e-postadress och välj sedan **Skicka verifierings kod**. Kontrol lera att en kod skickas till Inkorgen för e-post som du har angett. Hämta koden och ange den i rutan **verifierings kod** . Välj sedan **verifiera kod**.

## <a name="next-steps"></a>Nästa steg

- [Lägg till externa identitets leverantörer](add-identity-provider.md)
- [Skapa ett användarflöde](tutorial-create-user-flows.md)