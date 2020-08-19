---
title: 'Självstudie: Konfigurera Zscaler beta för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Zscaler beta.
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
ms.openlocfilehash: 71b40fe903e5a837046b9b29f62ef4875e3139e5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545926"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Zscaler beta för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Zscaler beta och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Zscaler beta.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).
>


## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En Zscaler beta-klient
* Ett användar konto i Zscaler beta med administratörs behörighet

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av Zscaler beta SCIM API, som är tillgängligt för Zscaler beta-utvecklare för konton med Enterprise-paketet.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Lägga till Zscaler Beta från galleriet

Innan du konfigurerar Zscaler beta för automatisk användar etablering med Azure AD måste du lägga till Zscaler beta från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Gör så här om du vill lägga till Zscaler beta från Azure AD-programgalleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zscaler Beta**, väljer **Zscaler Beta** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Zscaler Beta i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Tilldela användare till Zscaler beta

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler beta. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Zscaler Beta genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Viktiga tips för att tilldela användare till Zscaler beta

* Vi rekommenderar att en enda Azure AD-användare tilldelas Zscaler beta för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Zscaler beta måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurera automatisk användar etablering till Zscaler beta

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Zscaler beta utifrån användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Zscaler beta, genom att följa anvisningarna i [självstudien om enkel inloggning med Zscaler beta](zscaler-beta-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Konfigurera automatisk användar etablering för Zscaler beta i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**, Välj **alla program**och välj sedan **Zscaler beta**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler beta**.

    ![Zscaler Beta-länk i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Under avsnittet **admin credentials** måste du skriva in **klient-URL: en** och den **hemliga token** för ditt Zscaler beta-konto enligt beskrivningen i steg 6.

6. Hämta klient- **URL: en** och den **hemliga token**genom att gå till **Administration > autentiseringsinställningar** i användar gränssnittet för Zscaler beta Portal och klicka på **SAML** under **Autentiseringstyp**.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Klicka på **Konfigurera SAML** för att öppna **konfigurationens SAML** -alternativ.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Välj **Aktivera scim-baserad etablering** för att hämta **bas-URL** och **Bearer-token**och spara sedan inställningarna. Kopiera **bas-URL** : en till klient- **URL: en**och **ha token token**  till **hemlig token** i Azure Portal.

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till Zscaler beta. Om anslutningen Miss lyckas kontrollerar du att Zscaler beta-kontot har administratörs behörighet och försöker igen.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Zscaler beta**.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Zscaler beta i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Zscaler beta för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper för att Zscaler beta**.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zscaler beta i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha grupperna i Zscaler beta för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Zscaler beta, ändra **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare och/eller grupper som du vill etablera till Zscaler-Beta genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Zscaler beta-etablering](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Zscaler beta.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
