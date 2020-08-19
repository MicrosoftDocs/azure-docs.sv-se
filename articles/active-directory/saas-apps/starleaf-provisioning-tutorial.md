---
title: 'Självstudie: Konfigurera StarLeaf för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till StarLeaf.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 68484d2047e09dc82d44e15e86b13c4c54c49b03
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533834"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Självstudie: Konfigurera StarLeaf för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i StarLeaf och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till StarLeaf.

> [!NOTE]
>  I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande en för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En StarLeaf-klient](https://starleaf.com/).
* Ett användar konto i StarLeaf med administratörs behörighet.

## <a name="assign-users-to-starleaf"></a>Tilldela användare till StarLeaf
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och grupper i Azure AD som behöver åtkomst till StarLeaf. Sedan kan du tilldela användare och grupper till StarLeaf genom att följa [dessa anvisningar](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Viktiga tips för att tilldela användare till StarLeaf

* Vi rekommenderar att en enda Azure AD-användare tilldelas StarLeaf för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och grupper kan tilldelas senare.

* När du tilldelar en användare till StarLeaf måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med standard åtkomst rollen undantas från etablering.

## <a name="set-up-starleaf-for-provisioning"></a>Konfigurera StarLeaf för etablering

Innan du konfigurerar StarLeaf för automatisk användar etablering med Azure AD måste du konfigurera SCIM-etablering i StarLeaf:

1. Logga in på din [StarLeaf-administratörs konsol](https://portal.starleaf.com/#page=login). Navigera till **integreringar**  >  **Lägg till integrering**.

    ![StarLeaf Lägg till SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Välj den **typ** som ska Microsoft Azure Active Directory. Ange ett lämpligt namn i **namn**. Klicka på **Applicera**.

    ![StarLeaf Lägg till SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Värdena för **bas-URL: en för scim** och **åtkomsttoken** visas sedan. Dessa värden anges i fälten klient- **URL** och **hemligt token** på fliken etablering i StarLeaf-programmet i Azure Portal. 

    ![Skapa token för StarLeaf](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Lägg till StarLeaf från galleriet

Om du vill konfigurera StarLeaf för automatisk användar etablering med Azure AD måste du lägga till StarLeaf från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till StarLeaf från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **StarLeaf**och väljer **StarLeaf** i panelen resultat.
    ![StarLeaf i resultat listan](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Konfigurera automatisk användar etablering till StarLeaf

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i StarLeaf baserat på användar-och/eller grupp tilldelningar i Azure AD.

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **StarLeaf**.

    ![StarLeaf-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet admin credentials, mata in **scim grundläggande URL** och **åtkomsttoken** som hämtades tidigare i **klient-URL** respektive **hemlig token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till StarLeaf. Om anslutningen Miss lyckas kontrollerar du att StarLeaf-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar rutan **Skicka ett e-postmeddelande när ett fel uppstår** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till StarLeaf**.

    ![Skapa token för StarLeaf](media/starleaf-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till StarLeaf i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i StarLeaf för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Skapa token för StarLeaf](media/starleaf-provisioning-tutorial/userattribute.png)


10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Om du vill aktivera Azure AD Provisioning-tjänsten för StarLeaf ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till StarLeaf genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på StarLeaf.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Kopplings begränsningar

* StarLeaf stöder för närvarande inte grupp etablering. 
* StarLeaf kräver att värdena för **e-post** och **användar namn** har samma käll värde.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md).
