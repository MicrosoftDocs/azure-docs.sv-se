---
title: 'Självstudie: Konfigurera BlueJeans för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0b90389f6ae88a073f3961cc2f0957d22e190fe0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545463"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Självstudie: Konfigurera BlueJeans för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i BlueJeans och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till BlueJeans.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En BlueJeans-klient med [min företags](https://www.BlueJeans.com/pricing) plan eller bättre aktive rad
* Ett användar konto i BlueJeans med administratörs behörighet

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av [BlueJeans-API: et](https://BlueJeans.github.io/developer), som är tillgängligt för BlueJeans-team i standard planen eller bättre.

## <a name="adding-bluejeans-from-the-gallery"></a>Lägga till BlueJeans från galleriet

Innan du konfigurerar BlueJeans för automatisk användar etablering med Azure AD måste du lägga till BlueJeans från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till BlueJeans från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **BlueJeans**, väljer **BlueJeans** i panelen resultat och väljer sedan knappen **Lägg till** för att lägga till programmet.

    ![BlueJeans i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Tilldela användare till BlueJeans

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till BlueJeans. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till BlueJeans genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Viktiga tips för att tilldela användare till BlueJeans

* Vi rekommenderar att en enda Azure AD-användare tilldelas BlueJeans för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till BlueJeans måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurera automatisk användar etablering till BlueJeans

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i BlueJeans baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för BlueJeans genom att följa anvisningarna i [självstudien om enkel inloggning med BlueJeans](bluejeans-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurera automatisk användar etablering för BlueJeans i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**, Välj **alla program**och välj sedan **BlueJeans**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **BlueJeans**.

    ![Länken BlueJeans i listan med program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Under avsnittet **admin credentials** måste du skriva in **administratörens användar namn**och **Administratörs lösen ord** för ditt BlueJeans-konto. Exempel på dessa värden är:

   * I fältet **admin username** , fyller du i användar namnet för administratörs kontot på din BlueJeans-klient. Exempel: admin@contoso.com.

   * I fältet **Administratörs lösen ord** fyller du i lösen ordet som motsvarar administratörens användar namn.

6. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till BlueJeans. Om anslutningen Miss lyckas kontrollerar du att BlueJeans-kontot har administratörs behörighet och försöker igen.

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klicka på **Spara**.

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till BlueJeans**.

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Granska de användarattribut som synkroniseras från Azure AD till BlueJeans i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i BlueJeans för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD Provisioning-tjänsten för BlueJeans ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definiera de användare och/eller grupper som du vill etablera till BlueJeans genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![BlueJeans-etablering](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på BlueJeans.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* BlueJeans tillåter inte användar namn som överstiger 30 tecken.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
