---
title: 'Självstudie: Konfigurera bonus för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till bonus.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d8c3f64e5cb5269bfe7e555615f874ac3443c6eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357835"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Självstudie: Konfigurera bonus för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som utförs i bonus och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till bonus.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En [bonus klient](https://bonus.ly/pricing)
* Ett användar konto i bonus med administratörs behörighet

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av en [bonus REST-API](https://konghq.com/solutions/gateway/), som är tillgängligt för bonus utvecklare.

## <a name="adding-bonusly-from-the-gallery"></a>Lägga till bonus från galleriet

Innan du konfigurerar bonus för automatisk användar etablering med Azure AD måste du lägga till bonus från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Gör så här om du vill lägga till bonus från Azure AD-programgalleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **bonus** och väljer **bonus** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Bonus i resultat listan](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Tilldela användare till bonus

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD. 

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver ha till gång till bonus. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till bonus genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Viktiga tips för att tilldela användare en bonus

* Vi rekommenderar att en enda Azure AD-användare tilldelas en bonus för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till bonus måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurera automatisk användar etablering till bonus

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i bonus utifrån användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för bonus genom att följa anvisningarna i [självstudien om bonus enkel inloggning](bonus-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Konfigurera automatisk användar etablering för bonus i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**, Välj **alla program** och välj sedan **bonus**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **bonus**.

    ![Bonus länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Skärm bild av fliken bonus etablering. Under hantera är etableringen markerat." border="false":::

4. Ange **Etableringsläge** som **Automatiskt**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Skärm bild som visar List rutan etablerings läge, med automatiskt valt och markerat." border="false":::

5. Under avsnittet **admin credentials** måste du skriva in den **hemliga token** för ditt bonus konto enligt beskrivningen i steg 6.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="Skärm bild av avsnittet admin-autentiseringsuppgifter. Rutan hemligt token är tom, men rutan är markerad." border="false":::

6. Den **hemliga token** för ditt bonus konto finns i **Administratörs > företagets >-integration**. I avsnittet **om du vill koda klickar du** på **API > skapa ny API** -åtkomsttoken för att skapa en ny hemlig token.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Skärm bild av menyn bonus. Under admin är företag markerat. Under företag är integreringar markerade." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Skärm bild av avsnittet om du vill lägga till kod på webbplatsen bonus, med en P I markerat." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Skärm bild av webbplatsen för bonus. Fliken tjänster är öppen. Under ditt P-I-åtkomsttoken, skapar du en ny P I-åtkomsttoken är markerat." border="false":::

7. På följande skärm skriver du ett namn för åtkomsttoken i den angivna text rutan och trycker sedan på **skapa API-nyckel**. Den nya åtkomsttoken visas några sekunder i ett popup-fönster.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Skärm bild av sidan ny åtkomsttoken på webbplatsen för bonus. En ruta som saknar etikett innehåller min token och knappen Skapa en P I nyckel är markerad." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Skärm bild av webbplatsen för bonus. Ett meddelande visas som visar att en ny åtkomsttoken har skapats följt av en indechiffrerad token." border="false":::

8. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till bonus. Om anslutningen Miss lyckas kontrollerar du att ditt bonus konto har administratörs behörighet och försöker igen.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Skärm bild av avsnittet admin credentials i Azure Portal. Knappen text anslutning är markerad." border="false":::

9. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Skärm bild som visar en tom e-postadress. Ett alternativ visas som heter skicka ett e-postmeddelande när ett fel uppstår." border="false":::

10. Klicka på **Spara**.

11. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till bonus**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Skärm bild av avsnittet mappningar. Synkronisera Azure Active Directory användare till bonus är markerat under namn." border="false":::

12. Granska de användarattribut som synkroniseras från Azure AD till bonus i avsnittet mappning av **attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i bonus för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Skärm bild av sidan mappningar för attribut. En tabell visar Azure Active Directory attribut, motsvarande bonus attribut och matchande status." border="false":::

13. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera Azure AD Provisioning-tjänsten för bonus kan du ändra **etablerings statusen** till **på** i avsnittet **Inställningar** .

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Skärm bild av avsnittet Inställningar. Växling av etablerings status är inställt på av." border="false":::

15. Definiera de användare och/eller grupper som du vill etablera bonus genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Skärm bild som visar List rutan omfattning. Synkronisera endast tilldelade användare och grupper är markerat i rutan." border="false":::

16. När du är redo att etablera klickar du på **Spara**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Skärm bild av sidan för bonus etablering med knappen Spara markerad." border="false":::

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på ett bonus sätt.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png