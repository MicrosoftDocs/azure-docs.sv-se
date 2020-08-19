---
title: 'Självstudie: Konfigurera Soloinsight-CloudGate SSO för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Soloinsight-CloudGate SSO.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 32fd40ec4827c2995d156b0e238b48ff8cc24e65
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545122"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Soloinsight-CloudGate SSO för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Soloinsight-CloudGate SSO och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Soloinsight-CloudGate SSO.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Soloinsight-CloudGate SSO-klient](https://www.soloinsight.com/)
* Ett användar konto i Soloinsight-CloudGate SSO med administratörs behörighet.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Tilldela användare till Soloinsight-CloudGate SSO

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Soloinsight-CloudGate SSO. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Soloinsight-CloudGate SSO genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Viktiga tips för att tilldela användare till Soloinsight-CloudGate SSO

* Vi rekommenderar att en enda Azure AD-användare tilldelas Soloinsight-CloudGate SSO för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Soloinsight-CloudGate SSO måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Konfigurera Soloinsight-CloudGate SSO för etablering

1. Logga in på din [Soloinsight-CLOUDGATE SSO-administratörskonsolen](https://soloinsight.sigateway.com/login). Gå till **Administration > Systeminställningar**.

    ![Soloinsight – CloudGate SSO-administratörskonsolen](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Navigera till **Allmänt**.

    ![Soloinsight – CloudGate SSO Lägg till SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Rulla ned till slutet av sidan för att hämta klient- **URL** och **hemlig token**. Kopiera den **hemliga token**. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt Soloinsight-CloudGate SSO-program i Azure Portal.

    ![Soloinsight – CloudGate SSO Create token](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Lägg till Soloinsight-CloudGate SSO från galleriet

Innan du konfigurerar Soloinsight-CloudGate SSO för automatisk användar etablering med Azure AD måste du lägga till Soloinsight-CloudGate SSO från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Soloinsight-CloudGate SSO från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Soloinsight-CLOUDGATE SSO**, väljer **SOLOINSIGHT-CloudGate SSO** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Soloinsight-CloudGate SSO i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Konfigurera automatisk användar etablering till Soloinsight-CloudGate SSO 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Soloinsight-CloudGate SSO baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Soloinsight-CloudGate SSO genom att följa anvisningarna i [självstudien Soloinsight-CLOUDGATE enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om de här två funktionerna är på varandra

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Konfigurera automatisk användar etablering för Soloinsight-CloudGate SSO i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Soloinsight-CloudGate SSO** i programlistan.

    ![Soloinsight-CloudGate SSO-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://sigateway.com/scim/v2/sync/serviceproviderconfig` i **klient-URL**. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Soloinsight-CloudGate SSO. Om anslutningen Miss lyckas kontrollerar du att ditt Soloinsight-CloudGate SSO-konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till SOLOINSIGHT-CloudGate SSO**.

    ![Användar mappningar för Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Soloinsight-CloudGate SSO i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Soloinsight-CloudGate SSO för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Soloinsight – CloudGate SSO-användarattribut](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till SOLOINSIGHT-CloudGate SSO**.

    ![Soloinsight – CloudGate SSO Group-mappningar](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Soloinsight-CloudGate SSO i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Soloinsight-CloudGate SSO för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Soloinsight – CloudGate SSO-Gruppattribut](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Soloinsight-CloudGate SSO ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Soloinsight-CloudGate SSO genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Soloinsight-CloudGate SSO.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

