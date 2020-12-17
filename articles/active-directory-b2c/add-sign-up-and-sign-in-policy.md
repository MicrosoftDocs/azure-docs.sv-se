---
title: Konfigurera ett registrerings-och inloggnings flöde
titleSuffix: Azure AD B2C
description: Lär dig hur du konfigurerar ett registrerings-och inloggnings flöde i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 29dd67e9e6e15aaafec0cc47d89da32cbf369938
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618826"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Konfigurera ett registrerings-och inloggnings flöde i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Registrering och inloggnings flöde

Med registrerings-och inloggnings principer kan användare: 

* Registrera dig med ett lokalt konto
* Logga in med lokalt konto
* Registrera dig eller logga in med ett socialt konto
* Lösenordsåterställning

![Profil redigerings flöde](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Krav

[Registrera ett webb program i Azure Active Directory B2C](tutorial-register-applications.md)om du inte redan gjort det.

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Skapa ett användar flöde för registrering och inloggning

Användar flödet för registrering och inloggning hanterar både registrering och inloggnings upplevelser med en enda konfiguration. Användare av ditt program är i rätt sökväg beroende på kontexten.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden** och väljer sedan **nytt användar flöde**.

    ![Sidan användar flöden i portalen med knappen nytt användar flöde markerat](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. På sidan **skapa ett användar flöde** väljer du användar flödet **Registrera och logga** in.

    ![Välj sidan för användar flöde med registrering och inloggnings flöde markerat](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**. ([Läs mer](user-flow-versions.md) om användar flödes versioner.)

    ![Sidan skapa användar flöde i Azure Portal med egenskaper markerat](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
1. För **identitets leverantörer** väljer du **e-postregistrering**.
1. För användarattribut **och anspråk** väljer du de anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj till exempel **Visa mer** och välj sedan attribut och anspråk för **land/region**, **visnings namn** och **post nummer**. Klicka på **OK**.

    ![Sidan för attribut och val av anspråk med tre anspråk valda](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* anpassningsprefix automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde** och välj sedan **Registrera dig nu**.

    ![Sidan kör användar flöde i portalen med knappen Kör användar flöde markerat](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.PNG)

1. Ange en giltig e-postadress, klicka på **Skicka verifierings kod**, ange den verifierings kod som du får och välj sedan **verifiera kod**.
1. Ange ett nytt lösen ord och bekräfta lösen ordet.
1. Välj land och region, ange det namn som du vill visa, ange ett post nummer och klicka sedan på **skapa**. Token returneras till `https://jwt.ms` och ska visas för dig.
1. Nu kan du köra användar flödet igen och du bör kunna logga in med det konto som du skapade. Den returnerade token innehåller de anspråk som du har valt land/region, namn och post nummer.

> [!NOTE]
> Upplevelsen "kör användar flöde" är för närvarande inte kompatibel med URL-typen SPA-svar med hjälp av Authorization Code Flow. Om du vill använda upplevelsen "kör användar flöde" med dessa typer av appar registrerar du en svars-URL av typen "Web" och aktiverar det implicita flödet enligt beskrivningen [här](tutorial-register-spa.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Skapa en registrerings-och inloggnings princip

Anpassade principer är en uppsättning XML-filer som du överför till din Azure AD B2C-klient för att definiera användar resor. Vi tillhandahåller start paket med flera fördefinierade principer, inklusive: registrering och inloggning, lösen ords återställning och profil redigerings princip. Mer information finns i [Kom igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Nästa steg

* Lägg till en [inloggning med social identitetsprovider](add-identity-provider.md).
* Konfigurera ett [flöde för återställning av lösen ord](add-password-reset-policy.md).
