---
title: Aktivera ålders hantera i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om att identifiera minderåriga med ditt program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522466"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Aktivera ålders hantera i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ålders hantera i Azure Active Directory B2C (Azure AD B2C) gör att du kan identifiera minderåriga som vill använda ditt program, med eller utan föräldra medgivande. Du kan välja att blockera den underordnade från att logga in i programmet. Eller Tillåt användning för att slutföra inloggningen och ange den lägre status för programmet. 

>[!IMPORTANT]
>Den här funktionen är en allmänt tillgänglig förhandsversion. Använd inte funktionen för produktions program.
>

När ålders hantera har Aktiver ATS för ett användar flöde uppmanas användarna att ange sitt födelse datum och bosättnings landet. Om en användare loggar in som inte tidigare har angett informationen måste han eller hon ange den nästa gången de loggar in. Reglerna tillämpas varje gång en användare loggar in.

![Skärm bild av hantera information samla in flöde](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C använder den information som användaren anger för att identifiera om de är mindre. Fältet **ageGroup** uppdateras sedan i sitt konto. Värdet kan vara,,, `null` `Undefined` `Minor` `Adult` och `NotAdult` .  Fälten **ageGroup** och **consentProvidedForMinor** används sedan för att beräkna värdet för **legalAgeGroupClassification**.


## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Konfigurera din klient för ålders hantera

Om du vill använda ålders hantera i ett användar flöde måste du konfigurera klienten så att den har extra egenskaper.

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn. Välj den katalog som innehåller din klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal, Sök efter och välj **Azure AD B2C**.
1. Välj **Egenskaper** för din klient på menyn till vänster.
1. Under **ålders hantera** väljer du **Konfigurera**.
1. Vänta tills åtgärden har slutförts och klienten kommer att konfigureras för ålders hantera.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Aktivera ålders hantera i ditt användar flöde

När din klient organisation har kon figurer ATS för att använda ålders hantera kan du använda den här funktionen i [användar flöden](user-flow-versions.md) där den är aktive rad. Du aktiverar ålders hantera med följande steg:

1. Skapa ett användar flöde som har ålders hantera aktiverat.
1. När du har skapat användar flödet väljer du **Egenskaper** på menyn.
1. I avsnittet **ålders hantera** väljer du **aktive rad**.
1. För **registrering eller inloggning** väljer du hur du vill hantera användare:
    - Tillåt minderåriga att få åtkomst till ditt program.
    - Blockera endast minderåriga nedan för medgivande från åtkomst till ditt program.
    - Blockera alla minor från åtkomst till ditt program.
1. Välj något av följande alternativ för **vid blockering**:
    - **Skicka en JSON tillbaka till programmet** – med det här alternativet skickas ett svar tillbaka till programmet som en mindre blockerades.
    - **Visa en felsida** – användaren visas en sida som informerar dem om att de inte har åtkomst till programmet.

## <a name="test-your-user-flow"></a>Testa ditt användar flöde

1. Om du vill testa principen väljer du **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *testapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör användar flöde** .
1. Logga in med ett lokalt eller socialt konto. Välj sedan ditt hemland och födelse datum som simulerar ett mindre. 
1. Upprepa testet och välj ett födelse datum som simulerar en vuxen.  

När du loggar in som en del av bör du se följande fel meddelande: *tyvärr är inloggningen blockerad. Sekretess-och säkerhets lagstiftningen i ditt land förhindrar åtkomst till konton som tillhör barn.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Aktivera ålders hantera i din anpassade princip

1. Hämta exemplet på en ålders hantera princip på [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. Ersätt strängen `yourtenant` med namnet på din Azure AD B2C-klient i varje fil. Om namnet på din B2C-klient till exempel är *contosob2c*, blir alla instanser av `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. Ladda upp principfiler.

::: zone-end

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användar åtkomst i Azure AD B2C](manage-user-access.md).

