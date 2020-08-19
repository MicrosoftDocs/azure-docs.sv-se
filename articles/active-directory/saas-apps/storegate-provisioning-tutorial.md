---
title: 'Självstudie: Konfigurera Storegate för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c889b2c7b11c5e649045e34cdac9e50a3a242b6a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524462"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Storegate för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Storegate och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Storegate.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Storegate-klient](https://www.storegate.com)
* Ett användar konto på en Storegate med administratörs behörighet.

## <a name="assign-users-to-storegate"></a>Tilldela användare till Storegate

Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Storegate. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Storegate genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Viktiga tips för att tilldela användare till Storegate

* Vi rekommenderar att en enda Azure AD-användare tilldelas Storegate för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Storegate måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-storegate-for-provisioning"></a>Konfigurera Storegate för etablering

Innan du konfigurerar Storegate för automatisk användar etablering med Azure AD måste du hämta viss etablerings information från Storegate.

1. Logga in på [Storegate-administratörskonsolen](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) och navigera till inställningarna genom att klicka på användar ikonen i det övre högra hörnet och välj **konto inställningar**.

    ![Storegate Lägg till SCIM](media/storegate-provisioning-tutorial/admin.png)

2. I inställningar navigerar du till **Team > inställningar** och kontrollerar att växlings växeln är aktive ras i avsnittet **enkel inloggning** .

    ![Storegate-inställningar](media/storegate-provisioning-tutorial/team.png)

    ![Växlings knapp för Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Kopiera **klient-URL** och **token**. Dessa värden anges i fälten klient- **URL** och **hemlig token** på fliken etablering i Storegate-programmet i Azure Portal. 

    ![Skapa token för Storegate](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Lägg till Storegate från galleriet

Om du vill konfigurera Storegate för automatisk användar etablering med Azure AD måste du lägga till Storegate från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Storegate**och väljer **Storegate** i panelen resultat. 

    ![Storegate i resultat listan](common/search-new-app.png)

5. Välj knappen **Registrera dig för Storegate** som kommer att omdirigera dig till inloggnings sidan för Storegate. 

    ![Storegate OIDC Lägg till](media/storegate-provisioning-tutorial/signup.png)

6.  Logga in på [Storegate-administratörskonsolen](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) och navigera till inställningarna genom att klicka på användar ikonen i det övre högra hörnet och välj **konto inställningar**.

    ![Storegate-inloggning](media/storegate-provisioning-tutorial/admin.png)

7. I inställningar navigerar du till **Team > inställningar** och klickar på Växla växel i avsnittet enkel inloggning. då startas medgivande flödet. Klicka på **Aktivera**.

    ![Storegate-team](media/storegate-provisioning-tutorial/team.png)

    ![Storegate SSO](media/storegate-provisioning-tutorial/sso.png)

    ![Aktivera Storegate](media/storegate-provisioning-tutorial/activate.png)

8. Som Storegate är en OpenIDConnect-app väljer du att logga in på Storegate med ditt Microsoft Work-konto.

    ![Storegate OIDC-inloggning](media/storegate-provisioning-tutorial/login.png)

9. När autentiseringen är klar godkänner du frågan om medgivande för sidan medgivande. Programmet läggs sedan till automatiskt i din klient organisation och du omdirigeras till ditt Storegate-konto.

    ![Storegate OIDc-medgivande](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Konfigurera automatisk användar etablering till Storegate 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Storegate baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!NOTE]
> Läs mer om Storegate SCIM-slutpunkten i [detta](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Konfigurera automatisk användar etablering för Storegate i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Storegate**.

    ![Storegate-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://dialpad.com/scim` i **klient-URL**. Mata in det värde som du hämtade och sparade tidigare från Storegate i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Storegate. Om anslutningen Miss lyckas kontrollerar du att Storegate-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Storegate**.

    ![Storegate användar mappningar](media/storegate-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Storegate i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Storegate för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Storegate-användarattribut](media/storegate-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Storegate ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Storegate genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Storegate.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
