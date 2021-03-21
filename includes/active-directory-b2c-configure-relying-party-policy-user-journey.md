---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 73216b1b089444c1dc92bbe73ed07895de3711b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98951550"
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



