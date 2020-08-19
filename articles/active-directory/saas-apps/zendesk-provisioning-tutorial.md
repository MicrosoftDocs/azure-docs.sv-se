---
title: 'Självstudie: Konfigurera Zendesk för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Zendesk.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 5c038cc309d8f1f13a04de01ed83e04afa70ba6d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546177"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Zendesk för automatisk användar etablering

Den här självstudien visar de steg som du utför i Zendesk och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och grupper till Zendesk.

> [!NOTE]
> I den här självstudien beskrivs en koppling som är byggd ovanpå Azure AD-tjänsten för användar etablering. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering av SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En Zendesk-klient med professionell plan eller bättre aktive rad.
* Ett användar konto i Zendesk med administratörs behörighet.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Lägg till Zendesk från Azure Marketplace

Innan du konfigurerar Zendesk för automatisk användar etablering med Azure AD lägger du till Zendesk från Azure Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg om du vill lägga till Zendesk från Marketplace.

1. I navigerings fönstret till vänster i [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**.

    ![Ikonen Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Zendesk** och väljer **Zendesk** i resultat panelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Zendesk i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Tilldela användare till Zendesk

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering ska du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till Zendesk. Om du vill tilldela dessa användare eller grupper till Zendesk följer du instruktionerna i [tilldela en användare eller grupp till en Enterprise-App](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Viktiga tips för att tilldela användare till Zendesk

* Idag fylls Zendesk-roller i automatiskt och dynamiskt i Azure Portal gränssnittet. Innan du tilldelar Zendesk roller till användare ser du till att en inledande synkronisering har slutförts mot Zendesk för att hämta de senaste rollerna i Zendesk-klienten.

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till Zendesk för att testa den första konfigurationen för automatisk användar etablering. Du kan tilldela ytterligare användare eller grupper senare efter att testerna har slutförts.

* När du tilldelar en användare till Zendesk väljer du en giltig programspecifik roll, om sådan finns, i dialog rutan tilldelning. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurera automatisk användar etablering till Zendesk 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i Zendesk baserat på användar-eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också aktivera SAML-baserad enkel inloggning för Zendesk. Följ anvisningarna i [självstudien om Zendesk enkel inloggning](zendesk-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurera automatisk användar etablering för Zendesk i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**  >  **Zendesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zendesk**.

    ![Zendesk-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Zendesk-etablering](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Etablerings läge för Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Under avsnittet **admin credentials** måste du skriva in administratörs användar namnet, den hemliga token och domänen för ditt Zendesk-konto. Exempel på dessa värden är:

   * I rutan **Administratörs användar namn** fyller du i användar namnet för administratörs kontot på din Zendesk-klient. Ett exempel är admin@contoso.com.

   * I rutan **hemlig token** fyller du i den hemliga token enligt beskrivningen i steg 6.

   * I rutan **domän** fyller du i under domänen för din Zendesk-klient. Till exempel för ett konto med en klient-URL för `https://my-tenant.zendesk.com` är din under domän **min-klient**.

6. Den hemliga token för ditt Zendesk-konto finns i **Administration**  >  **API**-  >  **Inställningar**. Kontrol lera att **token-åtkomst** är **aktive rad**.

    ![Zendesk administratörs inställningar](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk hemlig token](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. När du har fyllt i rutorna som visas i steg 5 väljer du **Testa anslutning** för att kontrol lera att Azure AD kan ansluta till Zendesk. Om anslutningen Miss lyckas kontrollerar du att Zendesk-kontot har administratörs behörighet och försöker igen.

    ![Zendesk test anslutning](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot etablerings fel meddelanden. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-Zendesk e-postmeddelande](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Välj **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Zendesk**.

    ![Zendesk-synkronisering av användare](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Zendesk i avsnittet **mappningar för attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Zendesk för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Zendesk matchning av användarattribut](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Zendesk**.

    ![Synkronisering av Zendesk-grupp](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zendesk i avsnittet **mappningar för attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Zendesk för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Zendesk matchning av Gruppattribut](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Följ anvisningarna i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)för att konfigurera omfångs filter.

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Zendesk går du till avsnittet **Inställningar** och ändrar **etablerings statusen** till **på**.

    ![Etablerings status för Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definiera de användare eller grupper som du vill etablera till Zendesk. I avsnittet **Inställningar** väljer du de värden som du vill ha i **omfånget**.

    ![Zendesk-omfång](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. När du är redo att etablera väljer du **Spara**.

    ![Spara Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. 

Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Zendesk.

Information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Zendesk stöder endast grupper för användare med **agent** roller. Mer information finns i Zendesk- [dokumentationen](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* När en användare eller grupp tilldelas en anpassad roll tilldelar Azure AD-tjänsten automatisk användar etablering även standard roll **agenten**. Endast agenter kan tilldelas en anpassad roll. Mer information finns i [ZENDESK API-dokumentationen](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
