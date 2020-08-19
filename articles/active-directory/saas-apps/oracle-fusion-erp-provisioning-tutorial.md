---
title: 'Självstudie: Konfigurera Oracle Fusion ERP för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Oracle Fusion ERP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: ad6b24c4bbfc2e117c010b99247cb8eb9925ff3e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543831"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Oracle Fusion ERP för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Oracle Fusion ERP och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Oracle Fusion ERP.

> [!NOTE]
>  I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande en för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) hands versioner

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* En [Oracle Fusion ERP-klient](https://www.oracle.com/applications/erp/).
* Ett användar konto i Oracle Fusion ERP med administratörs behörigheter.

## <a name="assign-users-to-oracle-fusion-erp"></a>Tilldela användare till Oracle Fusion ERP 
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Oracle Fusion ERP. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Oracle Fusion ERP genom att följa anvisningarna här:
 
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Viktiga tips för att tilldela användare till Oracle Fusion ERP 

 * Vi rekommenderar att en enda Azure AD-användare tilldelas till Oracle Fusion ERP för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Oracle Fusion ERP måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med standard åtkomst rollen undantas från etablering.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Konfigurera Oracle Fusion ERP för etablering

Innan du konfigurerar Oracle Fusion ERP för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Oracle Fusion ERP.

1. Logga in på [administrations konsolen för Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Klicka på navigatören i det övre vänstra övre hörnet. Under **verktyg**väljer du **säkerhets konsol**.

    ![Oracle Fusion ERP-Lägg till SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Navigera till **användare**.
    
    ![Oracle Fusion ERP-Lägg till SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Spara användar namnet och lösen ordet för det administratörs användar konto som du ska använda för att logga in på administrations konsolen för Oracle Fusion ERP. Dessa värden måste anges i fälten **admin användar namn** och **lösen ord** på fliken etablering i ditt Oracle Fusion ERP-program i Azure Portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Lägg till Oracle Fusion ERP från galleriet

Om du vill konfigurera Oracle Fusion ERP för automatisk användar etablering med Azure AD måste du lägga till Oracle Fusion ERP från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Gör så här om du vill lägga till Oracle Fusion ERP från Azure AD Application Gallery:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Oracle Fusion ERP**, väljer **Oracle Fusion ERP** i resultat panelen.

    ![Oracle Fusion ERP i resultatlistan](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Konfigurera automatisk användar etablering till Oracle Fusion ERP 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Oracle Fusion ERP baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Oracle Fusion ERP genom att följa anvisningarna i [självstudien för Oracle Fusion ERP enkel inloggning](oracle-fusion-erp-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om SCIM-slutpunkten för Oracle Fusion ERP finns i [REST API för vanliga funktioner i molnet för Oracle-program](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Konfigurera automatisk användar etablering för Fuze i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Oracle Fusion ERP**.

    ![Oracle Fusion ERP-länk i listan med program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` i **klient-URL**. Ange administratörens användar namn och lösen ord som hämtades tidigare till fälten **admin-användarnamn** och **lösen ord** . Klicka på **Testa anslutning** mellan Azure AD och Oracle Fusion ERP. 

    ![Oracle Fusion ERP-Lägg till SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Oracle Fusion ERP**.

    ![Oracle Fusion ERP-Lägg till SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Oracle Fusion ERP i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Oracle Fusion ERP för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Oracle Fusion ERP-Lägg till SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Oracle Fusion ERP**.

    ![Mappningar för Oracle Fusion ERP-grupp](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Oracle Fusion ERP i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Oracle Fusion ERP för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Attribut för Oracle Fusion ERP-grupp](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Oracle Fusion ERP ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Oracle Fusion ERP genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Oracle Fusion ERP.

    Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Oracle Fusion ERP stöder endast grundläggande autentisering för deras SCIM-slutpunkt.
* Oracle Fusion ERP stöder inte grupp etablering.
* Roller i Oracle Fusion ERP mappas till grupper i Azure AD. Om du vill tilldela roller till användare i Oracle Fusion ERP från Azure AD måste du tilldela användare till önskade Azure AD-grupper som har namngetts efter roller i Oracle Fusion ERP.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
