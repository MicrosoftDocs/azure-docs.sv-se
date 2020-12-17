---
title: Konfigurera ett flöde för återställning av lösen ord
titleSuffix: Azure AD B2C
description: Lär dig hur du konfigurerar ett flöde för återställning av lösen ord i Azure Active Directory B2C.
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
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618827"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Konfigurera ett flöde för återställning av lösen ord i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Lösen ord rest-flöde

Med principen för lösen ords återställning kan användarna återställa sina egna bortglömt lösen ord. Flödet för lösen ords återställning omfattar följande steg: 
1. På sidan för registrering och inloggning klickar du på användaren "glömt lösen ordet?" . Azure AD B2C returnerar fel koden för AADB2C90118 till din app. Appen hanterar denna felkod genom att anropa principen för lösen ords återställning. 
1. Användarna anger och verifierar sin e-post med en tids gräns för en tidpunkt.
1. Ange ett nytt lösen ord.

![Flöde för lösen ords återställning](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Krav

[Registrera ett webb program i Azure Active Directory B2C](tutorial-register-applications.md)om du inte redan gjort det.

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

Om du vill göra det möjligt för användare av ditt program att återställa sitt lösen ord använder du ett användar flöde för återställning av lösen ord.

1. I menyn Azure AD B2C klient översikt väljer du **användar flöden** och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du **lösen ords återställning** användar flöde. 
1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**.
1. Ange ett **Namn** för användarflödet. Till exempel *passwordreset1*.
1. För **identitets leverantörer** aktiverar du **Återställ lösen ord med e-postadress**.
2. Under program anspråk klickar du på **Visa fler** och väljer de anspråk som du vill ska returneras i de autentiseringstoken som skickas tillbaka till programmet. Välj till exempel **Användarobjekt-id**.
3. Klicka på **OK**.
4. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* läggs automatiskt till i namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**, verifiera e-postadressen för det konto som du skapade tidigare och välj **Fortsätt**.
1. Nu har du möjlighet att ändra lösen ordet för användaren. Ändra lösen ordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Skapa en princip för återställning av lösenord

Anpassade principer är en uppsättning XML-filer som du överför till din Azure AD B2C-klient för att definiera användar resor. Vi tillhandahåller start paket med flera fördefinierade principer, inklusive: registrering och inloggning, lösen ords återställning och profil redigerings princip. Mer information finns i [Kom igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Nästa steg

Konfigurera ett [profil redigerings flöde](add-profile-editing-policy.md).