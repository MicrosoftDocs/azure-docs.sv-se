---
title: Multi-Factor Authentication i Azure Active Directory B2C | Microsoft Docs
description: Så här aktiverar du Multi-Factor Authentication i konsumentbaserade program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b7b7f1c5fb0a7991707a26b4a7f54fb3ffaf7bab
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033528"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Aktivera multifaktorautentisering i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) integreras direkt med [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) så att du kan lägga till ett andra säkerhets lager för att registrera dig och logga in i dina program. Du aktiverar multifaktorautentisering utan att skriva en enda rad kod. Om du redan har skapat inloggnings-och inloggnings användar flöden kan du fortfarande aktivera Multi-Factor Authentication.

Den här funktionen hjälper program att hantera scenarier som:

- Du behöver inte Multi-Factor Authentication för att komma åt ett program, men du behöver det för att få åtkomst till ett annat. Kunden kan till exempel Logga in på ett program för automatisk försäkring med ett socialt eller lokalt konto, men måste kontrol lera telefonnumret innan du får åtkomst till programmet Home Insurance som är registrerat i samma katalog.
- Du behöver inte Multi-Factor Authentication för att få åtkomst till ett program i allmänhet, men du behöver det för att få åtkomst till de känsliga delarna i det. Kunden kan till exempel Logga in på ett bank program med ett socialt eller lokalt konto och kontrol lera kontots saldo, men det måste kontrol lera telefonnumret innan du försöker utföra en överföring.

## <a name="set-multi-factor-authentication"></a>Ange Multi-Factor Authentication

::: zone pivot="b2c-user-flow"

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Använd filtret för **katalog + prenumeration** på den översta menyn för att välja den katalog som innehåller Azure AD B2C klient.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden**.
1. Välj det användar flöde som du vill aktivera MFA för. Till exempel *B2C_1_signinsignup*.
1. Välj **Egenskaper**.
1. I avsnittet **multifaktorautentisering** väljer du önskad **MFA-metod** och under **MFA tvång** väljer du **Always on**, eller **villkorlig (rekommenderas)**.
   > [!NOTE]
   >
   > - Om du väljer **villkorlig (rekommenderas)** måste du också [lägga till villkorlig åtkomst till användar flöden](conditional-access-user-flow.md)och ange de appar som du vill att principen ska tillämpas på.
   > - Multi-Factor Authentication (MFA) är inaktiverat som standard för registrering av användar flöden. Du kan aktivera MFA i användar flöden med telefon registrering, men eftersom ett telefonnummer används som primär identifierare är email eng ång slö sen ord det enda alternativet som är tillgängligt för den andra autentiserings faktorn.

1. Välj **Spara**. MFA har nu Aktiver ATS för det här användar flödet.

Du kan använda **Kör användar flöde** för att verifiera upplevelsen. Bekräfta följande scenario:

Ett kund konto skapas i din klient organisation innan Multi-Factor Authentication-steget inträffar. Under steget uppmanas kunden att ange ett telefonnummer och kontrol lera det. Om verifieringen lyckas är telefonnumret kopplat till kontot för senare användning. Även om kunden avbryter eller släpper ut, kan kunden uppmanas att verifiera ett telefonnummer igen vid nästa inloggning med Multi-Factor Authentication aktiverat.

::: zone-end

::: zone pivot="b2c-custom-policy"

Om du vill aktivera Multi-Factor Authentication Hämta start paketen för anpassad princip från GitHub uppdaterar du XML-filerna i **SocialAndLocalAccountsWithMFA** -startpaketet med ditt Azure AD B2C klient namn. **SocialAndLocalAccountsWithMFA** möjliggör alternativ för sociala, lokala och Multi-Factor Authentication. Mer information finns i [Kom igång med anpassade principer i Active Directory B2C](custom-policy-get-started.md). 

::: zone-end
