---
title: Skapa ett användar flöde – Azure Active Directory B2C
description: Lär dig hur du skapar användar flöden i Azure Portal för att aktivera registrering, inloggning och användar profil redigering för dina program i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bd436b972dfb1549232831b1f07c3726ff459dd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556499"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Skapa ett användar flöde i Azure Active Directory B2C

Du kan skapa [användar flöden](user-flow-overview.md) av olika typer i din Azure Active Directory B2C-klient (Azure AD B2C) och använda dem i dina program efter behov. Användar flöden kan återanvändas i olika program.

> [!IMPORTANT]
> Vi har ändrat hur vi refererar till användarflödesversioner. Tidigare erbjöd vi V1-versioner (produktionsklara), V1.1 och V2 (förhandsversion). Nu har vi konsoliderat användar flöden till **Rekommenderad** (nästa generations för hands version) och **standard** versioner (allmänt tillgängliga). Alla tidigare förhands gransknings användar flöden för V 1.1 och v2 finns på en sökväg till utfasningen den **1 augusti 2021**. Mer information finns [i användar flödes versioner i Azure AD B2C](user-flow-versions.md).

## <a name="before-you-begin"></a>Innan du börjar

- **Registrera det program** som du vill använda för att testa det nya användar flödet. Ett exempel finns i [självstudien: registrera ett webb program i Azure AD B2C](tutorial-register-applications.md).
- **Lägg till externa identitets leverantörer** om du vill aktivera användar inloggning med leverantörer som Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft eller Twitter. Se [lägga till identitets leverantörer i dina program i Azure AD B2C](add-identity-provider.md).
- **Konfigurera den lokala konto identitets leverantören** för att ange de identitets typer (e-post, användar namn, telefonnummer) som du vill stödja för lokala konton i din klient organisation. Sedan kan du välja bland dessa identitets typer som stöds när du skapar enskilda användar flöden. När en användare slutför användar flödet skapas ett lokalt konto i din Azure AD B2C katalog och din **lokala konto** identitets leverantör autentiserar användarens information. Konfigurera klient organisationens lokala konto identitets leverantör med följande steg:

   1. Logga in på [Azure-portalen](https://portal.azure.com/). 
   2. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
   3. Sök efter och välj **Azure AD B2C** i Sök fältet överst i Azure Portal.
   4. Under **Hantera** väljer du **identitets leverantörer**.
   5. I listan identitetsprovider väljer du **lokalt konto**.
   6. På sidan **Konfigurera lokala IDP** väljer du alla identitets typer som du vill stödja. Om du väljer alternativ här blir de bara tillgängliga för de användar flöden du skapar senare:
      - **Telefon** (för hands version): gör att en användare kan ange ett telefonnummer som verifieras vid registreringen och blir sitt användar-ID.
      - **E-post** (standard): gör att en användare kan ange en e-postadress som verifieras vid registreringen och blir sitt användar-ID.
      - **Användar namn**: låter en användare skapa sitt eget unika användar-ID. En e-postadress samlas in från användaren och verifieras.
    7. Välj **Spara**.

## <a name="create-a-user-flow"></a>Skapa ett användarflöde

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

    ![B2C-klient, katalog och prenumerations fönstret Azure Portal](./media/create-user-flow/directory-subscription-pane.png)

3. I Azure Portal söker du efter och väljer **Azure AD B2C**.
4. Under **principer** väljer du **användar flöden** och väljer sedan **nytt användar flöde**.

    ![Sidan användar flöden i portalen med knappen nytt användar flöde markerat](./media/create-user-flow/signup-signin-user-flow.png)

5. På sidan **skapa ett användar flöde** väljer du den typ av användar flöde som du vill skapa (se [användar flöden i Azure AD B2C](user-flow-overview.md) för en översikt).

    ![Välj en användar flödes sida med registrering och inloggnings flöde markerat](./media/create-user-flow/select-user-flow-type.png)

6. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**. ([Läs mer](user-flow-versions.md) om användar flödes versioner.)

    ![Sidan skapa användar flöde i Azure Portal med egenskaper markerat](./media/create-user-flow/select-version.png)

7. Ange ett **namn** för användar flödet (till exempel *signupsignin1*, *profileediting1*, *passwordreset1*).
8. Under **identitets leverantörer** väljer du alternativ beroende på vilken typ av användar flöde som du skapar:

   - **Lokalt konto**. Om du vill tillåta att användare skapar lokala konton i Azure AD B2C klient organisationen väljer du den typ av identifierare som du vill att de ska använda (till exempel e-post, användar-ID eller telefon). Endast de identitets typer som har kon figurer ATS i inställningarna för den [lokala kontots identitetsprovider](#before-you-begin) visas.

   - **Leverantörer av sociala identiteter**. Om du vill tillåta användar inloggning med [sociala identitets leverantörer som du har lagt till](add-identity-provider.md), t. ex. Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft eller Twitter, väljer du leverantörer i listan.

9. För användarattribut **och anspråk** väljer du de anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj **Visa fler**. Välj attribut och anspråk och välj sedan **OK**.

    ![Sidan för attribut och val av anspråk med tre anspråk valda](./media/create-user-flow/signup-signin-attributes.png)

10. Välj **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* anpassningsprefix automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj **principer**  >  **användar flöden** och välj sedan det användar flöde som du skapade. På sidan Översikt över användar flöde väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som du registrerade i steg 1. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj **Kör användar flöde**.
2. Beroende på vilken typ av användar flöde du testar kan du antingen registrera dig med en giltig e-postadress och följa registrerings flödet eller logga in med ett konto som du skapade tidigare.

    ![Sidan kör användar flöde i portalen med knappen Kör användar flöde markerat](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Följ anvisningarna för användar flödet. När du har slutfört användar flödet returneras token till `https://jwt.ms` och visas för dig.

> [!NOTE]
> Upplevelsen "kör användar flöde" är för närvarande inte kompatibel med URL-typen SPA-svar med hjälp av Authorization Code Flow. Om du vill använda upplevelsen "kör användar flöde" med dessa typer av appar registrerar du en svars-URL av typen "Web" och aktiverar det implicita flödet enligt beskrivningen [här](tutorial-register-spa.md).

## <a name="next-steps"></a>Nästa steg

- [Lägga till villkorlig åtkomst till Azure AD B2C användar flöden](conditional-access-user-flow.md)
- [Anpassa användar gränssnittet i ett Azure AD B2C användar flöde](customize-ui-with-html.md)
