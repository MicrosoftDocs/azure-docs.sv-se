---
title: Konfigurera ett flöde för profil redigering
titleSuffix: Azure AD B2C
description: Lär dig hur du konfigurerar ett profil redigerings flöde i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d84756a2ae4f8897c42e1846e3a91dbb9f7ad7e1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257049"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Konfigurera en profil redigerings flöde i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Profil redigerings flöde

Med profil redigerings princip kan användarna hantera sina profilmappar, t. ex. visnings namn, efter namn, tilldelat namn, stad och andra. Profil redigerings flödet omfattar följande steg: 

1. Registrera dig eller logga in med lokalt eller socialt konto. Om sessionen fortfarande är aktiv, Azure AD B2C auktorisera användaren och gå vidare till nästa steg.
1. Azure AD B2C läser användar profilen från katalogen och låter användaren redigera attributen.

![Profil redigerings flöde](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Förutsättningar

[Registrera ett webb program i Azure Active Directory B2C](tutorial-register-applications.md)om du inte redan gjort det.

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna ska kunna redigera sin profil i ditt program använder du en profil som redigerar användar flöde.

1. På menyn på sidan för Azure AD B2C klient översikt väljer du **användar flöden** och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du **profil redigering** användar flöde. 
1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**.
1. Ange ett **Namn** för användarflödet. Till exempel *profileediting1*.
1. För **identitets leverantörer** väljer du **e-postinloggning**.
1. För **användarattribut** väljer du de attribut som du vill att kunden ska kunna redigera i profilen. Välj till exempel **Visa mer** och välj sedan både attribut och anspråk för **visnings namn** och **befattning**. Klicka på **OK**.
1. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* läggs automatiskt till i namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde** och logga sedan in med det konto som du skapade tidigare.
1. Nu har du möjlighet att ändra visnings namn och befattning för användaren. Klicka på **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Skapa en profilredigeringsprincip

Anpassade principer är en uppsättning XML-filer som du överför till din Azure AD B2C-klient för att definiera användar resor. Vi tillhandahåller start paket med flera fördefinierade principer, inklusive: registrering och inloggning, lösen ords återställning och profil redigerings princip. Mer information finns i [Kom igång med anpassade principer i Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Nästa steg

* Lägg till en [inloggning med social identitetsprovider](add-identity-provider.md).