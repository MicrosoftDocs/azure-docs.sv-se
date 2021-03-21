---
title: SAML enkel inloggning för lokala appar med Azure AD App proxy
description: Lär dig att tillhandahålla enkel inloggning för lokala program som skyddas med SAML-autentisering. Ge fjärråtkomst till lokala appar med Application Proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03c688952f37bf9fc91e9dd25e09d9c31cd980d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257076"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Enkel inloggning med SAML för lokala program med programproxy

Du kan tillhandahålla enkel inloggning (SSO) till lokala program som skyddas med SAML-autentisering och ge fjärråtkomst till dessa program via programproxyn. Med enkel inloggning med SAML kan Azure Active Directory (Azure AD) autentiseras för programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggnings informationen till programmet via ett anslutnings protokoll. Du kan också mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk. Genom att aktivera Application Proxy förutom SAML SSO får användarna extern åtkomst till programmet och en sömlös SSO-upplevelse.

Programmen måste kunna använda SAML-token som utfärdats av **Azure Active Directory**. Den här konfigurationen gäller inte för program som använder en lokal identitetsprovider. I dessa scenarier rekommenderar vi att du visar [resurser för att migrera program till Azure AD](migration-resources.md).

SAML SSO med Application Proxy fungerar också med krypterings funktionen för SAML-token. Mer information finns i [Konfigurera Azure AD SAML-token-kryptering](howto-saml-token-encryption.md).

Protokoll diagrammen nedan beskriver den enkla inloggnings ordningen för både ett SP-initierat flöde av en tjänst leverantör och ett initierat IdP-flöde (Identity Provider – initierat). Application Proxy fungerar med SAML SSO genom att cachelagring av SAML-begäranden och-svar till och från det lokala programmet cachelagras.

  ![Diagram visar interaktioner för program, programproxy, klient och Azure A D för S P-initierad enkel inloggning.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Diagram visar interaktioner för program, programproxy, klient och Azure A D för I d-initierad enkel inloggning.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Skapa ett program och konfigurera SAML SSO

1. I Azure Portal väljer du **Azure Active Directory > företags program** och väljer **nytt program**.

2. Ange visnings namnet för det nya programmet, Välj **integrera andra program som du inte hittar i galleriet** och välj sedan **skapa**.

3. På sidan **Översikt** för appen väljer du **enkel inloggning**.

4. Välj **SAML** som metoden för enkel inloggning.

5. Börja med att konfigurera SAML SSO för att fungera samtidigt som i företags nätverket, se avsnittet grundläggande SAML-konfiguration i [Konfigurera SAML-baserad enkel inloggning](configure-saml-single-sign-on.md) för att konfigurera SAML-baserad autentisering för programmet.

6. Lägg till minst en användare i programmet och kontrol lera att test kontot har åtkomst till programmet. När du är ansluten till företags nätverket använder du test kontot för att se om du har enkel inloggning till programmet. 

   > [!NOTE]
   > När du har konfigurerat Application Proxy kommer du tillbaka och uppdaterar **URL: en** för SAML-svar.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publicera det lokala programmet med Application Proxy

Innan du kan ange SSO för lokala program måste du aktivera programproxyn och installera en anslutning. Se självstudien [Lägg till ett lokalt program för fjärråtkomst via programproxy i Azure AD](application-proxy-add-on-premises-application.md) för att lära dig hur du förbereder din lokala miljö, installerar och registrerar en anslutning och testar anslutningen. Följ sedan de här stegen för att publicera ditt nya program med Application Proxy. Information om andra inställningar som inte nämns nedan finns i avsnittet [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) i självstudien.

1. När programmet fortfarande är öppet i Azure Portal väljer du **programproxy**. Ange den **interna URL:** en för programmet. Om du använder en anpassad domän måste du också ladda upp TLS/SSL-certifikatet för ditt program. 
   > [!NOTE]
   > Vi rekommenderar att du använder anpassade domäner när det är möjligt för en optimerad användar upplevelse. Lär dig mer om att [arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).

2. Välj **Azure Active Directory** som metod för för **autentisering** för ditt program.

3. Kopiera den **externa URL:** en för programmet. Du behöver den här URL: en för att slutföra SAML-konfigurationen.

4. Försök att öppna programmet med den **externa URL:** en för att verifiera att programproxyn har kon figurer ATS korrekt genom att använda test kontot. Om det finns problem, se [Felsök problem med programproxy och fel meddelanden](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Uppdatera SAML-konfigurationen

1. När programmet fortfarande öppnas i Azure Portal väljer du **enkel inloggning**. 

2. På sidan **Konfigurera enkla Sign-On med SAML** går du till rubriken för den **grundläggande SAML-konfigurationen** och väljer dess **redigerings** ikon (en penna). Se till att den **externa URL:** en som du har konfigurerat i programproxyn är ifylld i fälten **identifierare**, **svars-URL** och **utloggnings-URL** . Dessa URL: er krävs för att Application Proxy ska fungera korrekt. 

3. Redigera **svars-URL: en** som kon figurer ATS tidigare så att dess domän kan bli tillgänglig på Internet via programproxyn. Om din **externa URL** till exempel är `https://contosotravel-f128.msappproxy.net` och den ursprungliga **svars-URL: en** var `https://contosotravel.com/acs` , måste du uppdatera den ursprungliga **svars-URL: en** till `https://contosotravel-f128.msappproxy.net/acs` .

    ![Ange grundläggande konfigurations data för SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Markera kryss rutan bredvid den uppdaterade **svars-URL: en** för att markera den som standard.

   * När du har markerat den obligatoriska **svars-URL: en** som standard kan du också ta bort den tidigare konfigurerade **svars-URL: en** som använde den interna URL

   * För ett SP-initierat flöde kontrollerar du att Server dels programmet anger rätt **svars-URL** eller URL-adress för intygs mottagare för att ta emot autentiseringstoken.

    > [!NOTE]
    > Om backend-appen förväntar sig att **svars-URL: en** ska vara den interna URL: en måste du antingen använda [anpassade domäner](application-proxy-configure-custom-domain.md) för att matcha interna och externa URL: er eller installera det säkra inloggnings tillägget Mina appar på användarnas enheter. Det här tillägget omdirigeras automatiskt till rätt Application Proxy-tjänst. Information om hur du installerar tillägget finns i avsnittet [Mina appar säkra inloggnings tillägg](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testa appen

När du har slutfört alla dessa steg ska appen vara igång. Så här testar du appen:

1. Öppna en webbläsare och navigera till den **externa URL** som du skapade när du publicerade appen. 
1. Logga in med det test konto som du har tilldelat till appen. Du bör kunna läsa in programmet och ha SSO i programmet.

## <a name="next-steps"></a>Nästa steg

- [Hur ger Azure AD-programproxy enkel inloggning?](./what-is-single-sign-on.md)
- [Felsöka programproxyn](application-proxy-troubleshoot.md)