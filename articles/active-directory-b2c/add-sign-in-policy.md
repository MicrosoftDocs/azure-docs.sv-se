---
title: Konfigurera ett inloggnings flöde
titleSuffix: Azure Active Directory B2C
description: Lär dig hur du konfigurerar ett inloggnings flöde i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/12/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c4db7bf989a0062f87a5c3e6303ba64224996554
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224493"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Konfigurera ett inloggnings flöde i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Översikt över inloggnings flödet

Med inloggnings principen kan användare: 

* Användare kan logga in med ett Azure AD B2C lokalt konto
* Registrera dig eller logga in med ett socialt konto
* Lösenordsåterställning
* Användare kan inte registrera sig för ett Azure AD B2C lokalt konto – en administratör kan använda [MS Graph API](manage-user-accounts-graph-api.md)för att skapa ett konto.

![Profil redigerings flöde](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Förutsättningar

[Registrera ett webb program i Azure Active Directory B2C](tutorial-register-applications.md)om du inte redan gjort det.

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Skapa ett inloggnings användar flöde

Så här lägger du till inloggnings princip:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden** och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du **inloggnings** användar flödet.
1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**. ([Läs mer](user-flow-versions.md) om användar flödes versioner.)
1. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
1. För **identitets leverantörer** väljer du **e-postinloggning**.
1. För **program anspråk** väljer du de anspråk och attribut som du vill skicka till ditt program. Välj till exempel **Visa mer** och välj sedan attribut och anspråk för **visnings namn**, **förnamn**  **, efter namn och** **användarens objekt-ID**. Klicka på **OK**.
1. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* anpassningsprefix automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**.
1. Du bör kunna logga in med det konto som du skapade (med MS Graph API), utan registrerings länken. Den returnerade token innehåller de anspråk som du har valt.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Ta bort registrerings länken

Den tekniska profilen **SelfAsserted-LocalAccountSignin-email** är en [självkontrollerad](self-asserted-technical-profile.md), som anropas under registrerings-eller inloggnings flödet. Om du vill ta bort registrerings länken ställer du in `setting.showSignupLink` metadata på `false` . Åsidosätt de tekniska profilerna för SelfAsserted-LocalAccountSignin-email i tilläggs filen. 

1. Öppna tilläggs filen för principen. Till exempel _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_ .
1. Hitta `ClaimsProviders` elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråks leverantör i- `ClaimsProviders` elementet:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
          <Metadata>
            <Item Key="setting.showSignupLink">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. I `<BuildingBlocks>` -elementet lägger du till följande [ContentDefinition](contentdefinitions.md) för att referera till version 1.2.0 eller nyare data-URI:

    ```XML
    <ContentDefinitions>
     <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
      </ContentDefinition>
    </ContentDefinitions>
    ```

## <a name="update-and-test-your-policy"></a>Uppdatera och testa principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ramverk för identitets upplevelse**.
1. Välj **överför anpassad princip** och ladda upp de två principfiler som du har ändrat.
1. Välj den inloggnings princip som du laddade upp och klicka på knappen **Kör nu** .
1. Du bör kunna logga in med det konto som du skapade (med MS Graph API), utan registrerings länken.

::: zone-end

## <a name="next-steps"></a>Nästa steg

* Lägg till en [inloggning med social identitetsprovider](add-identity-provider.md).
* Konfigurera ett [flöde för återställning av lösen ord](add-password-reset-policy.md).
