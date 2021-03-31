---
title: Konfigurera registrering och inloggning med ett Facebook-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med Facebook-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7e7a99daa169c994a0b9656786926f0715fa17a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580070"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett Facebook-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Skapa ett Facebook-program

Om du vill aktivera inloggning för användare med ett Facebook-konto i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i [Facebook-appens instrument panel](https://developers.facebook.com/). Mer information finns i [utveckling av appar](https://developers.facebook.com/docs/development). Om du inte redan har ett Facebook-konto kan du registrera dig på [https://www.facebook.com/](https://www.facebook.com/) .

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina Facebook-kontoautentiseringsuppgifter.
1. Om du inte redan har gjort det måste du registrera dig som en Facebook-utvecklare. Om du vill göra det väljer du **Kom igång** i det övre högra hörnet på sidan, accepterar Facebook-principer och slutför registrerings stegen.
1. Välj **Mina appar** och sedan **skapa app**.
1. Välj **Bygg anslutna upplevelser**.
1. Ange ett **visnings namn** och en giltig **kontakt-e-postadress**.
1. Välj **skapa app-ID**. Detta kan kräva att du accepterar Facebook-plattforms principer och slutför en säkerhets kontroll online.
1. Välj **Inställningar**  >  **Basic**.
    1. Välj en **kategori**, till exempel `Business and Pages` . Det här värdet krävs av Facebook, men används inte för Azure AD B2C.
    1. Ange en URL för URL: en för **tjänst villkoren**, till exempel `http://www.contoso.com/tos` . Princip-URL: en är en sida som du upprätthåller för att tillhandahålla villkor för ditt program.
    1. Ange en URL för **Sekretess policyns URL**, till exempel `http://www.contoso.com/privacy` . Princip-URL: en är en sida som du upprätthåller för att tillhandahålla sekretess information för ditt program.
1. Längst ned på sidan väljer du **Lägg till plattform** och väljer sedan **webbplats**.
1. I **webbplats-URL** anger du adressen till din webbplats, till exempel `https://contoso.com` . 
1. Välj **Spara ändringar**.
1. Kopiera värdet för **app-ID** överst på sidan.
1. Välj **Visa** och kopiera värdet för **appens hemlighet**. Du använder båda alternativen för att konfigurera Facebook som en identitets leverantör i din klient organisation. **App Secret** är en viktig säkerhets autentiseringsuppgift.
1. Från menyn väljer du **plus** tecknet bredvid **produkter**. Under **Lägg till produkter till din app** väljer du **Konfigurera** under **Facebook-inloggning**.
1. I menyn väljer du **Facebook-inloggning** och sedan **Inställningar**.
1. I **giltiga OAuth-omdirigerings-URI: er** anger du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Om du använder en [anpassad domän](custom-domain.md)anger du `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ersätt `your-tenant-name` med namnet på din klient och `your-domain-name` med din anpassade domän. 
1. Välj **Spara ändringar** längst ned på sidan.
1. Om du vill göra ditt Facebook-program tillgängligt för Azure AD B2C väljer du status väljaren längst upp till höger på sidan och **aktiverar det för att göra** programmet offentligt och väljer sedan **Växla läge**.  I det här läget bör statusen ändras från **utveckling** till **Live**.

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Konfigurera Facebook som identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer** och välj sedan **Facebook**.
1. Ange ett **namn**. Till exempel *Facebook*.
1. För **klient-ID** anger du app-ID: t för det Facebook-program som du skapade tidigare.
1. För **klient hemligheten** anger du appens hemlighet som du har spelat in.
1. Välj **Spara**.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Lägg till Facebook Identity Provider i ett användar flöde 

Facebook-identitetsprovider har nu kon figurer ATS, men den är inte tillgänglig ännu på någon av inloggnings sidorna. Så här lägger du till Facebook Identity Provider i ett användar flöde:

1. Välj **användar flöden** i Azure AD B2C klient.
1. Klicka på det användar flöde som du vill lägga till Facebook Identity Provider.
1. Under **leverantörer av sociala identitet** väljer du **Facebook**.
1. Välj **Spara**.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör användar flöde** .
1. På sidan registrering eller inloggning väljer du **Facebook** för att logga in med Facebook-kontot.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra appens hemlighet som du tidigare har spelat in i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ** väljer du `Manual` .
7. Ange ett **namn** för princip nyckeln. Till exempel `FacebookSecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. I **hemlighet** anger du appens hemlighet som du tidigare har spelat in.
9. För **nyckel användning** väljer du `Signature` .
10. Klicka på **Skapa**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurera ett Facebook-konto som en identitets leverantör

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** Ersätt värdet för `client_id` med Facebook-programmets ID i filen:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>Ladda upp och testa principen

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Ladda upp *TrustFrameworkExtensions.xml* -filen till din klient organisation.
1. Under **anpassade principer** väljer du **B2C_1A_signup_signin**.
1. För **Välj program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. På sidan registrering eller inloggning väljer du **Facebook** för att logga in med Facebook-kontot.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skickar Facebook-token till ditt program](idp-pass-through-user-flow.md).
