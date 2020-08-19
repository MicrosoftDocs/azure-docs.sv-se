---
title: 'Självstudie: Konfigurera en livsmedelsingrediens för automatisk användar etablering genom att använda Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till en livsmedelsingrediens.
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
ms.openlocfilehash: 40549fa8880fb4dc4d29046b0037621610f0b130
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544191"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Självstudie: Konfigurera en livsmedelsingrediens för automatisk användar etablering

Den här artikeln visar hur du konfigurerar Azure Active Directory (Azure AD) i en livsmedelsingrediens och Azure AD för att automatiskt etablera eller avetablera användare eller grupper till en livsmedelsingrediens.

> [!NOTE]
> I artikeln beskrivs en anslutning som är byggd ovanpå Azure AD-tjänsten för användar etablering. För att lära dig vad den här tjänsten gör och hur den fungerar och för att få svar på vanliga frågor, se [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande en för hands version. Mer information om användnings funktionen för Azure i för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du uppfyller följande krav:

* En Azure AD-klient
* [En klient organisation](https://www.food.ee/about/)
* Ett användar konto i en livsmedelsingrediens med administratörs behörighet

## <a name="assign-users-to-foodee"></a>Tilldela användare till en livsmedelsingrediens 

Azure AD använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till en livsmedelsingrediens. När du har bestämt dig kan du tilldela dessa användare eller grupper till en livsmedelsingrediens genom att följa instruktionerna i [tilldela en användare eller grupp till en Enterprise-App](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Viktiga tips för att tilldela användare till en livsmedelsingrediens 

Tänk på följande när du tilldelar användare:

* Vi rekommenderar att du bara tilldelar en enda Azure AD-användare till en livsmedelsingrediens för att testa konfigurationen av automatisk användar etablering. Du kan tilldela ytterligare användare eller grupper senare.

* När du tilldelar en användare till en livsmedelsingrediens väljer du en giltig programspecifik roll, om den är tillgänglig, i fönstret **tilldelning** . Användare som har *standard åtkomst* rollen undantas från etablering.

## <a name="set-up-foodee-for-provisioning"></a>Konfigurera en livsmedelsingrediens för etablering

Innan du konfigurerar livsmedelsingrediensen för automatisk användar etablering med hjälp av Azure AD måste du aktivera system för etablering av SCIM (Cross-Domain Identity Management) i en livsmedelsingrediens.

1. Logga in på [livsmedelsingrediensen](https://www.food.ee/login/)och välj sedan ditt klient-ID.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. Under **Enterprise Portal**väljer du **enkel inloggning**.

    ![Livsmedelsingrediensen Enterprise Portal menyn i den vänstra rutan](media/Foodee-provisioning-tutorial/scim.png)

1. Kopiera värdet i rutan **API-token** för senare användning. Du kommer att skriva in den i rutan **hemlig token** på fliken **etablering** i programmet för din näring i Azure Portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Lägg till en livsmedelsingrediens från galleriet

Om du vill konfigurera en livsmedelsingrediens för automatisk användar etablering med hjälp av Azure AD måste du lägga till en livsmedelsingrediens från Azure AD-programgalleriet till listan över hanterade SaaS-program.

Gör så här om du vill lägga till en livsmedelsingrediens från Azure AD-programgalleriet:

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**.

    ![Kommandot Azure Active Directory](common/select-azuread.png)

1. Välj **företags program**  >  **alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **nytt program** längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

1. I sökrutan anger du **livsmedelsingrediens**, väljer en **livsmedelsingrediens** i resultat fönstret och väljer sedan **Lägg** till för att lägga till programmet.

    ![Livsmedelsingrediensen i resultat listan](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Konfigurera automatisk användar etablering för en livsmedelsingrediens 

I det här avsnittet konfigurerar du Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper i en livsmedelsingrediens baserat på användar-eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för livsmedelsingrediensen genom att följa anvisningarna i [självstudien om enkel inloggning i livsmedelsingredienser](Foodee-tutorial.md). Du kan konfigurera enkel inloggning oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

Konfigurera automatisk användar etablering för näring i Azure AD genom att göra följande:

1. I [Azure Portal](https://portal.azure.com)väljer du **företags program**  >  **alla program**.

    ![Fönstret företags program](common/enterprise-applications.png)

1. I listan **program** väljer du **mat**man.

    ![Länken livsmedelsingredienser i program listan](common/all-applications.png)

1. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

1. I list rutan **etablerings läge** väljer du **Automatisk**.

    ![Fliken etablering](common/provisioning-automatic.png)

1. Under **admin-autentiseringsuppgifter**gör du följande:

   a. I rutan **klient webb adress** anger du det **https: \/ /concierge.Food.ee/scim/v2-** värde som du hämtade tidigare.

   b. I rutan **hemlig token** anger du det **API-token** -värde som du hämtade tidigare.
   
   c. För att säkerställa att Azure AD kan ansluta till en livsmedelsingrediens väljer du **Testa anslutning**. Om anslutningen Miss lyckas kontrollerar du att ditt konto för din näring har administratörs behörighet och försöker sedan igen.

    ![Länken testa anslutning](common/provisioning-testconnection-tenanturltoken.png)

1. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel och markerar sedan kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Text rutan e-postavisering](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. Under **mappningar**väljer du **Synkronisera Azure Active Directory användare till en livsmedelsingrediens**.

    ![Användar mappningar för livsmedelsingredienser](media/Foodee-provisioning-tutorial/usermapping.png)

1. Under **attribut mappningar**granskar du de användarattribut som synkroniseras från Azure AD till en livsmedelsingrediens. De attribut som väljs som **matchande** egenskaper används för att matcha *användar kontona* i livsmedelsingrediensen för uppdaterings åtgärder. 

    ![Användar mappningar för livsmedelsingredienser](media/Foodee-provisioning-tutorial/userattribute.png)

1. Om du vill spara ändringarna väljer du **Spara**.
1. Under **mappningar**väljer **du synkronisera Azure Active Directory grupper till en livsmedelsingrediens**.

    ![Användar mappningar för livsmedelsingredienser](media/Foodee-provisioning-tutorial/groupmapping.png)

1. Under **attribut mappningar**granskar du de användarattribut som synkroniseras från Azure AD till en livsmedelsingrediens. De attribut som väljs som **matchande** egenskaper används för att matcha *grupp kontona* i livsmedelsingrediensen för uppdaterings åtgärder.

    ![Användar mappningar för livsmedelsingredienser](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Om du vill spara ändringarna väljer du **Spara**.
1. Konfigurera omfångs filter. Mer information finns i anvisningarna i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för mat i avsnittet **Inställningar** ändrar du **etablerings statusen** till **på**.

    ![Etablerings status växeln](common/provisioning-toggle-on.png)

1. Under **Inställningar**i list rutan **omfattning** definierar du de användare eller grupper som du vill etablera till en livsmedelsingrediens.

    ![List rutan etablerings omfång](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Knappen för att spara etablerings konfiguration](common/provisioning-configuration-save.png)

Föregående åtgärd startar den inledande synkroniseringen av de användare eller grupper som du har definierat i list rutan **omfång** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information finns i [hur lång tid tar det att etablera användare?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten för en livsmedelsingrediens. Mer information finns i [kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
