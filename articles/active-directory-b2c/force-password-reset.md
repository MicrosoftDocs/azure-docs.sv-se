---
title: Konfigurera ett återställnings flöde för återställning av lösen ord i Azure AD B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du konfigurerar ett tvingande lösen flöde för återställning av lösen ord i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b6aae76b0b35f8195fb52b7fb11de43d8fa511ba
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028442"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Konfigurera ett lösen ord för tvångs återställning av lösen ord i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Som administratör kan du [återställa en användares lösen ord](manage-users-portal.md#reset-a-users-password) om användaren glömmer bort sitt lösen ord. Eller om du vill tvinga dem att återställa lösen ordet. I den här artikeln får du lära dig hur du tvingar fram en återställning av lösen ord i dessa scenarier.

## <a name="overview"></a>Översikt

När en administratör återställer en användares lösen ord via Azure Portal anges värdet för attributet [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) `true` .

[Inloggnings-och registrerings resan](add-sign-up-and-sign-in-policy.md) kontrollerar värdet för det här attributet. När användaren har slutfört inloggningen, om attributet är inställt på `true` , måste användaren återställa sitt lösen ord. Värdet för attributet anges till tillbaka `false` .

![Framtvinga lösen ords återställnings flöde](./media/force-password-reset/force-password-reset-flow.png)

Flödet för lösen ords återställning gäller för lokala konton i Azure AD B2C som använder en [e-postadress](identity-provider-local.md#email-sign-in) eller ett [användar namn](identity-provider-local.md#username-sign-in) med ett lösen ord för inloggning.

### <a name="force-a-password-reset-after-90-days"></a>Framtvinga ett lösen ords återställning efter 90 dagar

Som administratör kan du ange att användarens lösen ord upphör att gälla 90 dagar med [MS Graph](microsoft-graph-operations.md). Efter 90 dagar anges värdet för attributet [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) automatiskt till `true` . Mer information om hur du anger en användares förfallo princip för lösen ord finns i [attribut för lösen ords princip](user-profile-attributes.md#password-policy-attribute).

När du har angett en princip för lösen ordets giltighets tid, måste du också konfigurera flödet för lösen ords återställning enligt beskrivningen i den här artikeln.  

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>Konfigurera principen

::: zone pivot="b2c-user-flow"

Så här aktiverar du inställningen för **Tvingad återställning av lösen ord** i ett användar flöde för registrering eller inloggning:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **användar flöden**.
1. Välj registrerings-och inloggnings-eller inloggnings flödet (av typen **rekommenderas**) som du vill anpassa.
1. På den vänstra menyn under **Inställningar** väljer du **Egenskaper**.
1. Under **lösen ords komplexitet** väljer du **Tvingad återställning av lösen ord**.
1. Välj **Spara**.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Logga in på [Azure Portal](https://portal.azure.com) som användar administratör eller administratör för lösen ord. Mer information om tillgängliga roller finns [i tilldela administratörs roller i Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **Användare**. Sök efter och välj den användare som du ska använda för att testa lösen ords återställningen och välj sedan **Återställ lösen ord**.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **användar flöden**.
1. Välj ett användar flöde för registrering eller inloggning (av typen **rekommenderas**) som du vill testa.
1. Välj **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj **Kör användar flöde**.
1. Logga in med det användar konto som du vill återställa lösen ordet för.
1. Du måste nu ändra lösen ordet för användaren. Ändra lösen ordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

::: zone-end

::: zone pivot="b2c-custom-policy"

1. Hämta exemplet på en tvingande lösen ords återställning på [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset).
1. Ersätt strängen `yourtenant` med namnet på din Azure AD B2C-klient i varje fil. Om namnet på din B2C-klient till exempel är *contosob2c*, blir alla instanser av `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. Överför principfiler i följande ordning: tilläggs principen `TrustFrameworkExtensionsCustomForcePasswordReset.xml` , sedan den förlitande part principen `SignUpOrSigninCustomForcePasswordReset.xml` .

### <a name="test-the-policy"></a>Testa principen

1. Logga in på [Azure Portal](https://portal.azure.com) som användar administratör eller administratör för lösen ord. Mer information om tillgängliga roller finns [i tilldela administratörs roller i Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Välj **Användare**. Sök efter och välj den användare som du ska använda för att testa lösen ords återställningen och välj sedan **Återställ lösen ord**.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj den `B2C_1A_signup_signin_Custom_ForcePasswordReset` princip som du vill öppna. 
1. För **program** väljer du ett webb program som du [har registrerat tidigare](tutorial-register-applications.md). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. Logga in med det användar konto som du vill återställa lösen ordet för.
1. Du måste nu ändra lösen ordet för användaren. Ändra lösen ordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

::: zone-end

## <a name="next-steps"></a>Nästa steg

Konfigurera en [återställning av lösen ord för självbetjäning](add-password-reset-policy.md).
