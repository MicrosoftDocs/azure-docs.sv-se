---
title: 'Självstudie: Konfigurera prioritets mat ris för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till prioritets mat ris.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: bca751c55182df99e87ff1651de534985d9b5e76
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548998"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Självstudie: Konfigurera prioritets mat ris för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i prioritets mat ris och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till prioritets mat ris.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En prioritets mat ris klient](https://appfluence.com/pricing/)
* Ett användar konto i en prioriterad matris med administratörs behörighet.

## <a name="assign-users-to-priority-matrix"></a>Tilldela användare prioritets mat ris

Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till prioritets mat ris. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till prioritets mat ris genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Viktiga tips för att tilldela användare prioritets mat ris

* Vi rekommenderar att en enskild Azure AD-användare tilldelas prioritets mat ris för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare prioritets mat ris måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-priority-matrix-for-provisioning"></a>Konfigurera prioritets mat ris för etablering

Innan du konfigurerar prioritets mat ris för automatisk användar etablering med Azure AD måste du hämta viss etablerings information från prioritets matrisen.

1. Logga in på din [prioriterade mat ris administratörs konsol](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Klicka på **token OAuth-inloggning** för prioritets mat ris

    ![Prioritets mat ris Lägg till SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Klicka på knappen **Hämta ny token** . Kopiera **token-strängen**. Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt prioriterade mat ris program i Azure Portal. 

    ![Skapa token för prioritets mat ris](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Lägg till prioritets mat ris från galleriet

Om du vill konfigurera prioritets mat ris för automatisk användar etablering med Azure AD måste du lägga till prioritets mat ris från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök anger du **prioritets mat ris**, väljer **prioritets mat ris** i resultat panelen. 

    ![Prioritets mat ris i resultat listan](common/search-new-app.png)

5. Välj **mat ris knappen Registrera dig för prioritet** som kommer att omdirigera dig till inloggnings sidan för prioritets mat ris. 

    ![OIDC Lägg till prioritets mat ris](media/priority-matrix-provisioning-tutorial/signup.png)

6. Eftersom matrisen prioritet är en OpenIDConnect-app väljer du att logga in på prioritets mat ris med ditt Microsoft Work-konto.

    ![OIDC-inloggning för prioritets mat ris](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. När autentiseringen är klar godkänner du frågan om medgivande för sidan medgivande. Programmet läggs sedan till automatiskt i din klient organisation och du omdirigeras till ditt prioriterade mat ris konto.

    ![OIDc medgivande för prioritets mat ris](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Konfigurera automatisk användar etablering till prioritets mat ris 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i prioritets mat ris baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!NOTE]
> Mer information om SCIM-slutpunkten för prioritets matrisen finns i [matrisen för användar etablering och prioritet](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Konfigurera automatisk användar etablering för prioritets mat ris i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **prioritets mat ris**.

    ![Länken prioritets mat ris i listan program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://sync.appfluence.com/scim/v2/` i **klient-URL**. Mata in det värde som du hämtade och sparade tidigare från prioritets mat ris i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till prioritets mat ris. Om anslutningen Miss lyckas kontrollerar du att ditt prioriterade mat ris konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till prioritets mat ris**.

    ![Användar mappningar för prioritets mat ris](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till prioritets mat ris i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i prioritets mat ris för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användarattribut för prioritets mat ris](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för prioritets mat ris ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till prioritets mat ris genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på prioritets mat ris.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)


