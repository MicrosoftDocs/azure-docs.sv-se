---
title: 'Självstudie: Konfigurera TheOrgWiki för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till TheOrgWiki.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 5959ccd2699ef1162779a4a1d90ab6bb8af1bbbf
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544858"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Självstudie: Konfigurera TheOrgWiki för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i TheOrgWiki och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till TheOrgWiki.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En OrgWiki-klient](https://www.theorgwiki.com/welcome/).
* Ett användar konto i TheOrgWiki med administratörs behörighet.

## <a name="assign-users-to-theorgwiki"></a>Tilldela användare till TheOrgWiki

Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till TheOrgWiki. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till TheOrgWiki genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Viktiga tips för att tilldela användare till TheOrgWiki

* Vi rekommenderar att en enda Azure AD-användare tilldelas TheOrgWiki för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till TheOrgWiki måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-theorgwiki-for-provisioning"></a>Konfigurera TheOrgWiki för etablering

Innan du konfigurerar TheOrgWiki för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på TheOrgWiki.

1. Logga in på din [TheOrgWiki-administratörs konsol](https://www.theorgwiki.com/login/). Klicka på **Administratörs konsolen**.

    ![TheOrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. I administratörs konsolen klickar du på **fliken Inställningar**. 

    ![TheOrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Gå till **tjänst konton**.

    ![TheOrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klicka på **+ tjänst konto**. Välj **token-baserad**under **typ av tjänst konto**. Klicka på **Spara**.

    ![TheOrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Kopiera de **aktiva tokens**. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt TheOrgWiki-program i Azure Portal.
     
    ![TheOrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Lägg till TheOrgWiki från galleriet

Om du vill konfigurera TheOrgWiki för automatisk användar etablering med Azure AD måste du lägga till TheOrgWiki från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **TheOrgWiki**och väljer **TheOrgWiki** i panelen resultat. 

    ![TheOrgWiki i resultat listan](common/search-new-app.png)

5. Välj knappen **Registrera dig för TheOrgWiki** som kommer att omdirigera dig till inloggnings sidan för TheOrgWiki. 

    ![TheOrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  I det övre högra hörnet väljer du **Logga in**.

    ![TheOrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Som TheOrgWiki är en OpenIDConnect-app väljer du att logga in på OrgWiki med ditt Microsoft Work-konto.

    ![TheOrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Efter en lyckad autentisering läggs programmet automatiskt till i din klient organisation och du omdirigeras till ditt TheOrgWiki-konto.

    ![OrgWiki Lägg till SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Konfigurera automatisk användar etablering till TheOrgWiki 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TheOrgWiki baserat på användar-och/eller grupp tilldelningar i Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Konfigurera automatisk användar etablering för TheOrgWiki i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TheOrgWiki**.

    ![OrgWiki-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` i **klient-URL**. 

    Exempel: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> Det går bara att ange värdet för under **domänen** under den inledande registrerings processen för TheOrgWiki.
 
6. Mata in token-värdet i fältet **hemligt token** som du hämtade tidigare från TheOrgWiki. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till TheOrgWiki. Om anslutningen Miss lyckas kontrollerar du att TheOrgWiki-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till TheOrgWiki**.

    ![TheOrgWiki användar mappningar](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Granska de användarattribut som synkroniseras från Azure AD till TheOrgWiki i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i TheOrgWiki för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![TheOrgWiki-användarattribut](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD Provisioning-tjänsten för TheOrgWiki ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till OrgWiki genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [hur lång tid det tar att etablera användare](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på TheOrgWiki. Mer information finns i [kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md).