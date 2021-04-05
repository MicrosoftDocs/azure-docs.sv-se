---
title: Inaktivera e-postverifiering under kund registrering
titleSuffix: Azure AD B2C
description: Lär dig hur du inaktiverar e-postverifiering under kund registrering i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585028"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Inaktivera e-postverifiering under kund registrering i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Som standard verifierar Azure Active Directory B2C (Azure AD B2C) din kunds e-postadress för lokala konton (konton för användare som registrerar sig för e-postadress eller användar namn). Azure AD B2C garanterar giltiga e-postadresser genom att kräva att kunderna verifierar dem under registrerings processen. Det förhindrar också skadliga aktörer från att använda automatiserade processer för att generera bedrägliga konton i dina program.

Vissa programutvecklare föredrar att hoppa över e-postverifiering under registrerings processen och får i stället kunder att verifiera sin e-postadress senare. Azure AD B2C kan konfigureras för att inaktivera e-postverifiering för att stödja detta. Detta skapar en smidigare registrerings process och ger utvecklare flexibiliteten att särskilja kunder som har verifierat sin e-postadress från kunder som inte har det.

> [!WARNING]
> Att inaktivera e-postverifiering i registrerings processen kan leda till skräp post. Om du inaktiverar standard Azure AD B2C e-postverifieringen från e-post, rekommenderar vi att du implementerar ett system för ersättnings verifiering.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Inaktivera e-postverifiering

::: zone pivot="b2c-user-flow"

Följ de här stegen för att inaktivera e-postverifiering:

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Använd filtret för **katalog + prenumeration** på den översta menyn för att välja den katalog som innehåller Azure AD B2C klient.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden**.
1. Välj det användar flöde som du vill inaktivera e-postverifiering för. Till exempel *B2C_1_signinsignup*.
1. Välj **sidlayouter**.
1. Välj **inloggnings sida för lokalt konto**.
1. Under **användarattribut** väljer du **e-postadress**.
1. I list rutan **kräver verifiering** väljer du **Nej**.
1. Välj **Spara**. E-postverifiering är nu inaktiverat för det här användar flödet.

::: zone-end

::: zone pivot="b2c-custom-policy"
**LocalAccountSignUpWithLogonEmail** Technical Profile är en [självkontrollerad](self-asserted-technical-profile.md), som anropas under registrerings flödet. Om du vill inaktivera e-postverifieringen ställer du in `EnforceEmailVerification` metadata på falskt. Åsidosätt de tekniska profilerna för LocalAccountSignUpWithLogonEmail i tilläggs filen. 

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Hitta `ClaimsProviders` elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråks leverantör i- `ClaimsProviders` elementet:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Testa principen 

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Använd filtret för **katalog + prenumeration** på den översta menyn för att välja den katalog som innehåller Azure AD B2C klient.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden**.
1. Välj det användar flöde som du vill inaktivera e-postverifiering för. Till exempel *B2C_1_signinsignup*.
1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**
1. Du bör kunna registrera dig med en e-postadress utan verifieringen.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD-klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
1. Välj **ramverk för identitets upplevelse**.
1. Välj **överför anpassad princip** och ladda upp de två principfiler som du har ändrat.
1. Välj den registrerings-eller inloggnings princip som du laddade upp och klicka på knappen **Kör nu** .
1. Du bör kunna registrera dig med en e-postadress utan verifieringen.

::: zone-end


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [anpassar användar gränssnittet i Azure Active Directory B2C](customize-ui-with-html.md)

