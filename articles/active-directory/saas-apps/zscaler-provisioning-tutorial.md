---
title: 'Självstudie: Konfigurera Zscaler för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Zscaler.
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
ms.openlocfilehash: a3424a42e86b47d4103dd41ca143b5bc7be3796a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545938"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Zscaler för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Zscaler och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Zscaler.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klientorganisation.
* En Zscaler-klient.
* Ett användar konto i Zscaler med administratörs behörighet.

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av Zscaler SCIM-API: et, som är tillgängligt för Zscaler-utvecklare för konton med Enterprise-paketet.

## <a name="adding-zscaler-from-the-gallery"></a>Lägga till Zscaler från galleriet

Innan du konfigurerar Zscaler för automatisk användar etablering med Azure AD måste du lägga till Zscaler från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Zscaler från Azure AD-programgalleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Zscaler**, väljer **Zscaler** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Zscaler i resultat listan](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Tilldela användare till Zscaler

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Zscaler genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Viktiga tips för att tilldela användare till Zscaler

* Vi rekommenderar att en enda Azure AD-användare tilldelas Zscaler för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Zscaler måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurera automatisk användar etablering till Zscaler

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Zscaler baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Zscaler genom att följa anvisningarna i [självstudien om enkel inloggning med Zscaler](zscaler-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Konfigurera automatisk användar etablering för Zscaler i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**, Välj **alla program**och välj sedan **Zscaler**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler**.

    ![Zscaler-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Under avsnittet **admin credentials** måste du skriva in **klient-URL: en** och den **hemliga token** för ditt Zscaler-konto enligt beskrivningen i steg 6.

6. Hämta klient- **URL** och **hemlig token**genom att gå till **Administration > autentiseringsinställningar** i användar gränssnittet för Zscaler-portalen och klicka på **SAML** under **Autentiseringstyp**.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klicka på **Konfigurera SAML** för att öppna **KONFIGURATIONs-SAML** -alternativ.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Välj **Aktivera scim-baserad etablering** för att hämta **bas-URL** och **Bearer-token**och spara sedan inställningarna. Kopiera **bas-URL** : en till klient- **URL: en**och **ha token token**  till **hemlig token** i Azure Portal.

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till Zscaler. Om anslutningen Miss lyckas kontrollerar du att Zscaler-kontot har administratörs behörighet och försöker igen.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/test-connection.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/notification.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Zscaler**.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Zscaler i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Zscaler för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Zscaler**.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zscaler i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Zscaler för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Zscaler ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare och/eller grupper som du vill etablera till Zscaler genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/scoping.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Zscaler-etablering](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Zscaler.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
