---
title: 'Självstudie: Konfigurera besök för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton för att gå vidare.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 15031a3e139265410179baa13db9ed3f1b41cf17
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531811"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Självstudie: Konfigurera besök för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som du utför i gång och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare eller grupper.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering av SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En besöks klient](https://www.visitly.io/pricing/)
* Ett användar konto i går med administratörs behörighet

## <a name="assign-users-to-visitly"></a>Tilldela användare till gång 

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till. Tilldela sedan dessa användare eller grupper till gång till genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Viktiga tips för att tilldela användare till gång 

* Vi rekommenderar att du tilldelar en enda Azure AD-användare för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare eller grupper kan tilldelas senare.

* När du tilldelar en användare till gång måste du välja en giltig programspecifik roll (om tillgänglig) i dialog rutan tilldelning. Användare med standard åtkomst rollen undantas från etablering.

## <a name="set-up-visitly-for-provisioning"></a>Ställ in för etablering på gång

Innan du konfigurerar för automatisk användar etablering med Azure AD måste du aktivera system för SCIM-etablering (Cross-Domain Identity Management) på plats.

1. Logga in för att [besöka](https://app.visitly.io/login). Välj synkronisering av **integrations**  >  **värden**.

    ![Synkronisering av värd](media/Visitly-provisioning-tutorial/login.png)

2. Välj **Azure AD** -avsnittet.

    ![Avsnittet Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Kopiera **API-nyckeln**. De här värdena anges i rutan **hemlig token** på fliken **etablering** i appen för öppna program i Azure Portal.

    ![API-nyckel](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Lägg till besök från galleriet

Om du vill konfigurera besök för automatisk användar etablering med Azure AD lägger du till besök från Azure AD-programgalleriet i listan över hanterade SaaS-program.

Följ dessa steg om du vill lägga till besök från Azure AD-programgalleriet.

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret väljer du **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan går du till **besök**, väljer **besök** i panelen resultat och väljer sedan **Lägg** till för att lägga till programmet.

    ![Visitly i resultatlistan](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Konfigurera automatisk användar etablering för att gå på gång 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper i besök baserat på användar-eller grupp tilldelningar i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för besök följer du anvisningarna i [självstudien för enkel inloggning med enkel inloggning](Visitly-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Konfigurera automatisk användar etablering för besök i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**.

    ![Alla program](common/enterprise-applications.png)

2. I programlistan väljer du **Visitly**.

    ![Visitly-länk i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Etablerings läge inställt på automatiskt](common/provisioning-automatic.png)

5. Under avsnittet admin credentials måste du skriva `https://api.visitly.io/v1/usersync/SCIM` in **API-nyckelvärdena** som hämtades tidigare i **klient-URL** respektive **hemlig token**. Välj **Testa anslutning** för att säkerställa att Azure AD kan ansluta till den. Om anslutningen Miss lyckas måste du kontrol lera att ditt konto för besöks konto har administratörs behörighet och försök igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare att besöka**.

    ![Användar mappningar på användar Sidan](media/visitly-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD och finns i avsnittet **mappningar för attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i för gång för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    ![Besöks användar-attribut](media/visitly-provisioning-tutorial/userattribute.png)

10. Följ anvisningarna i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)för att konfigurera omfångs filter.

11. Om du vill aktivera Azure AD Provisioning-tjänsten för besök kan du ändra **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare eller grupper som du vill etablera genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera väljer du **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare eller grupper att etablera finns i [hur lång tid tar det att etablera användare?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på besök. Mer information finns i [kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

Det finns inte stöd för hårda borttagningar på sidan. Allt är endast mjuk borttagning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
