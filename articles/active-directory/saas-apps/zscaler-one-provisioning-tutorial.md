---
title: 'Självstudie: Konfigurera Zscaler en för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Zscaler ett.
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
ms.openlocfilehash: 978f8ff5e57a5a9a1df10152713c8c42901b3b6f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545870"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Zscaler en för automatisk användar etablering

Den här självstudien visar de steg som du utför i Zscaler en och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och grupper till Zscaler en.

> [!NOTE]
> I den här självstudien beskrivs en koppling som är byggd ovanpå Azure AD-tjänsten för användar etablering. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering av SaaS-program (Software-as-a-Service) med Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En Zscaler en klient.
* Ett användar konto i Zscaler ett med administratörs behörighet.

> [!NOTE]
> Azure AD Provisioning-integrationen är beroende av Zscaler ett SCIM-API. Detta API är tillgängligt för att Zscaler en utvecklare för konton med Enterprise-paketet.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Lägg till Zscaler från Azure Marketplace

Innan du konfigurerar Zscaler en för automatisk användar etablering med Azure AD lägger du till Zscaler en från Azure Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg om du vill lägga till Zscaler från Marketplace.

1. I navigerings fönstret till vänster i [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**.

    ![Ikonen Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zscaler ett** och väljer **Zscaler en** i resultat panelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Zscaler en i resultat listan](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Tilldela användare till Zscaler en

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering ska du bestämma vilka användare eller grupper i Azure AD som behöver ha åtkomst till Zscaler One. Om du vill tilldela dessa användare eller grupper till Zscaler en, följer du instruktionerna i [tilldela en användare eller grupp till en Enterprise-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Viktiga tips för att tilldela användare till Zscaler en

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Zscaler för att testa konfigurationen för automatisk etablering av användare. Du kan tilldela ytterligare användare eller grupper senare.

* När du tilldelar en användare till Zscaler en väljer du en giltig programspecifik roll, om sådan finns, i dialog rutan tilldelning. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Konfigurera automatisk användar etablering för att Zscaler en

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Zscaler ett baserat på användar-eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Zscaler. Följ anvisningarna i [självstudien om att Zscaler en enkel inloggning](zscaler-One-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Konfigurera automatisk användar etablering för Zscaler en i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**  >  **Zscaler One**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler ett**.

    ![Zscaler en länk i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Zscaler en etablering](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Zscaler ett etablerings läge](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Under avsnittet **admin-autentiseringsuppgifter** fyller du i rutorna **klient-URL** och **hemlig token** med inställningarna för ditt Zscaler-konto enligt beskrivningen i steg 6.

6. Om du vill hämta klient-URL: en och den hemliga token går du till **Administration**  >  **autentiseringsinställningar** i Zscaler ett Portal gränssnitt. Under **Autentiseringstyp**väljer du **SAML**.

    ![Zscaler en autentiseringsinställningar](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Välj **Konfigurera SAML** för att öppna **Konfigurera SAML** -alternativ.

    ![Zscaler en konfigurera SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Välj **Aktivera scim-baserad etablering** för att hämta inställningarna i bas- **URL** och **Bearer-token**. Spara inställningarna. Kopiera **bas-URL** -inställningen till **klient webb adressen** i Azure Portal. Kopiera inställningen för **Bearer-token** till **hemlig token** i Azure Portal.

7. När du har fyllt i rutorna som visas i steg 5 väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till Zscaler en. Om anslutningen Miss lyckas kontrollerar du att Zscaler ett konto har administratörs behörighet och försöker igen.

    ![Zscaler en test anslutning](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot etablerings fel meddelanden. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Zscaler ett e-postmeddelande](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Välj **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare att Zscaler ett**.

    ![Zscaler en användar-synkronisering](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Zscaler ett i avsnittet **mappningar av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Zscaler ett för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Zscaler ett matchande användarattribut](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att Zscaler ett**.

    ![Zscaler en grupp-synkronisering](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zscaler ett i avsnittet **mappningar av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Zscaler en för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Zscaler en matchande grupps attribut](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Följ anvisningarna i [kursen omfångs filter](./../active-directory-saas-scoping-filters.md)för att konfigurera omfångs filter.

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Zscaler, ändrar du **etablerings statusen** till **på**i avsnittet **Inställningar** .

    ![Zscaler en etablerings status](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare eller grupper som du vill etablera för att Zscaler en. I avsnittet **Inställningar** väljer du de värden som du vill ha i **omfånget**.

    ![Zscaler ett omfång](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. När du är redo att etablera väljer du **Spara**.

    ![Zscaler ett Spara](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. 

Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Zscaler.

Information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
