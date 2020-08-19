---
title: 'Självstudie: Konfigurera Tableau online för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Tableau online.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: da89bf6cb336087f97341ca57a9b2545c966a053
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533465"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Tableau online för automatisk användar etablering

Den här självstudien visar de steg som du utför i Tableau online och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och grupper till Tableau online.

> [!NOTE]
> I den här självstudien beskrivs en koppling som är byggd ovanpå Azure AD-tjänsten för användar etablering. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering av SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du har:

*   En Azure AD-klientorganisation.
*   En [Tableau online-klient](https://www.tableau.com/).
*   Ett användar konto i Tableau online med administratörs behörighet.

> [!NOTE]
> Azure AD Provisioning-integreringen är beroende av [Tableau Online REST API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Detta API är tillgängligt för Tableau online-utvecklare.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Lägg till Tableau online från Azure Marketplace
Innan du konfigurerar Tableau online för automatisk användar etablering med Azure AD lägger du till Tableau online från Azure Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg om du vill lägga till Tableau online från Marketplace.

1. I navigerings fönstret till vänster i [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**.

    ![Ikonen Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Tableau online** och väljer **Tableau online** i resultat panelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Tableau online i resultat listan](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Tilldela användare till Tableau online

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bestämmer du vilka användare eller grupper i Azure AD som behöver åtkomst till Tableau online. Om du vill tilldela dessa användare eller grupper till Tableau online följer du anvisningarna i [tilldela en användare eller grupp till en Enterprise-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Viktiga tips för att tilldela användare till Tableau online

*   Vi rekommenderar att du tilldelar en enda Azure AD-användare till Tableau online för att testa den automatiska konfigurationen av användar etablering. Du kan tilldela ytterligare användare eller grupper senare.

*   När du tilldelar en användare till Tableau online väljer du en giltig programspecifik roll, om sådan finns, i dialog rutan tilldelning. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurera automatisk användar etablering till Tableau online

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Tableau online baserat på användar-eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Tableau online. Följ anvisningarna i [självstudien om enkel inloggning med Tableau online](tableauonline-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurera automatisk användar etablering för Tableau online i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**  >  **Tableau online**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Tableau online**.

    ![Länken Tableau online i listan program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Tableau online-etablering](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Tableau online etablerings läge](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Under avsnittet **admin credentials** måste du skriva in domän, admin användar namn, administratörs lösen ord och INNEHÅLLS-URL för ditt Tableau online-konto:

   * I rutan **domän** fyller du i under domänen baserat på steg 6.

   * I rutan **Administratörs användar namn** fyller du i användar namnet för administratörs kontot på din Clarizen-klient. Ett exempel är admin@contoso.com.

   * I rutan **Administratörs lösen ord** fyller du i lösen ordet för det administratörs konto som motsvarar administratörens användar namn.

   * I rutan **innehålls-URL** fyller du i under domänen baserat på steg 6.

6. När du har loggat in på administratörs kontot för Tableau online kan du hämta värdena för **domän** -och **innehålls-URL** : en från URL: en för administratörs sidan.

    * **Domänen** för ditt Tableau online-konto kan kopieras från den här delen av URL: en:

        ![Tableau online-domän](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Det går att kopiera **innehålls-URL:** en för ditt Tableau online-konto från det här avsnittet. Det är ett värde som definieras vid konto installationen. I det här exemplet är värdet "contoso":

        ![Tableau för innehålls adress online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Din **domän** kan skilja sig från den som visas här.

7. När du har fyllt i rutorna som visas i steg 5 väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till Tableau online. Om anslutningen Miss lyckas kontrollerar du att ditt Tableau online-konto har administratörs behörighet och försöker igen.

    ![Tableau online – test anslutning](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot etablerings fel meddelanden. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postavisering om Tableau online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Välj **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Tableau**.

    ![Synkronisering av Tableau online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Tableau online i avsnittet **mappningar av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i Tableau online för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Tableau som matchar användar attribut online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Tableau**.

    ![Synkronisering av Tableau online-grupp](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Tableau online i avsnittet **mappningar av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i Tableau online för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Tableau för matchande grupp](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Följ anvisningarna i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)för att konfigurera omfångs filter.

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Tableau online går du till avsnittet **Inställningar** och ändrar **etablerings statusen** till **på**.

    ![Tableau online etablerings status](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definiera de användare eller grupper som du vill etablera för Tableau online. I avsnittet **Inställningar** väljer du de värden som du vill ha i **omfånget**.

    ![Tableau online-omfång](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. När du är redo att etablera väljer du **Spara**.

    ![Tableau online-Spara](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. 

Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Tableau online.

Information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
