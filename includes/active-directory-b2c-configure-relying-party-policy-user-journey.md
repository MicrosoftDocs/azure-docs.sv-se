---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674248"
---
## <a name="configure-the-relying-party-policy"></a>Konfigurera principen för förlitande part

Principen för förlitande part, till exempel [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), anger den användar resa som Azure AD B2C ska köras. Hitta **DefaultUserJourney** -elementet i [förlitande part](../articles/active-directory-b2c/relyingparty.md). Uppdatera  **ReferenceId** så att den matchar användar res-ID: t där du lade till identitets leverantören. 

I följande exempel för `CustomSignUpOrSignIn` användar resan är **ReferenceId** inställt på `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Överför den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **Ladda upp anpassad princip** och överför sedan de två principfiler som du ändrade, i följande ordning: tilläggs principen, till exempel `TrustFrameworkExtensions.xml` , sedan den förlitande part principen, till exempel `SignUpSignIn.xml` .

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin`
1. För **program** väljer du ett webb program som du har registrerat tidigare. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

