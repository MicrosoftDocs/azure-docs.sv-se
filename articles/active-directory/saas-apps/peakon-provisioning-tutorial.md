---
title: 'Självstudie: Konfigurera Peakon automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Peakon.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: b16ca0c396e2c406677af351cf6b1de2a3499353
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543684"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Peakon för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Peakon och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Peakon.

> [!NOTE]
>  I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande en för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.
## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav

* En Azure AD-klientorganisation.
* [En Peakon-klient](https://peakon.com/us/pricing/).
* Ett användar konto i Peakon med administratörs behörighet.

## <a name="assigning-users-to-peakon"></a>Tilldela användare till Peakon

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Peakon. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Peakon genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Viktiga tips för att tilldela användare till Peakon 

* Vi rekommenderar att en enda Azure AD-användare tilldelas Peakon för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Peakon måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-peakon-for-provisioning"></a>Konfigurera Peakon för etablering

1.  Logga in på din [Peakon-administratörs konsol](https://app.Peakon.com/login). Klicka på **konfiguration**. 

    ![Peakon-administratörskonsolen](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Välj **integreringar**.
    
    ![Peakon – etablering av medarbetare](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Aktivera **medarbetares etablering**.

    ![Peakon – etablering av medarbetare](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Kopiera värdena för **SCIM 2,0-URL: en** och **OAuth Bearer-token**. Dessa värden anges i fältet klient- **URL** och **hemligt token** på fliken etablering i Peakon-programmet i Azure Portal.

    ![Skapa token för Peakon](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Lägg till Peakon från galleriet

Om du vill konfigurera Peakon för automatisk användar etablering med Azure AD måste du lägga till Peakon från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Peakon**, väljer **Peakon** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Peakon i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurera automatisk användar etablering till Peakon 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Peakon baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Peakon genom att följa anvisningarna i [självstudien om enkel inloggning med Peakon](peakon-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Konfigurera automatisk användar etablering för Peakon i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Peakon**.

    ![Peakon-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **scim 2,0-URL: en** och **OAuth Bearer token** -värden som HÄMTAdes tidigare i **klient-URL** respektive **hemlig token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Peakon. Om anslutningen Miss lyckas kontrollerar du att Peakon-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Peakon**.

    ![Peakon användar mappningar](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Granska de användarattribut som synkroniseras från Azure AD till Peakon i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Peakon för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Peakon-användarattribut](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i       [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Peakon.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Alla anpassade användarattribut i Peakon måste utökas från peakons anpassade SCIM User-tillägg `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` .

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)