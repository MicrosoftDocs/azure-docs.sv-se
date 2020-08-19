---
title: 'Självstudie: Konfigurera Figma automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: f0f0d61788d7ac9b242dbc176a125b45753d2578
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555132"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Figma för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Figma och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Figma.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Figma-klient](https://www.figma.com/pricing/).
* Ett användar konto i Figma med administratörs behörighet.

## <a name="assign-users-to-figma"></a>Tilldela användare till Figma.
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Figma. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Figma genom att följa anvisningarna här:
 
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Viktiga tips för att tilldela användare till Figma

 * Vi rekommenderar att en enda Azure AD-användare tilldelas Figma för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Figma måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med standard åtkomst rollen undantas från etablering.

## <a name="set-up-figma-for-provisioning"></a>Konfigurera Figma för etablering

Innan du konfigurerar Figma för automatisk användar etablering med Azure AD måste du hämta viss etablerings information från Figma.

1. Logga in på din [Figma-administratörs konsol](https://www.Figma.com/). Klicka på kugg hjuls ikonen bredvid din klient.

    ![FigmaFigma – etablering av medarbetare](media/Figma-provisioning-tutorial/image0.png)

2. Gå till **Inställningar för allmän > uppdaterings inloggning**.

    ![FigmaFigma – etablering av medarbetare](media/Figma-provisioning-tutorial/figma03.png)

3. Kopiera **klient-ID: t**. Det här värdet används för att skapa slut punkts-URL: en för SCIM som ska anges i fältet **klient-URL** på fliken etablering i ditt Figma-program i Azure Portal.

    ![Skapa token för Figma](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Rulla nedåt och klicka på **generera API-token**.

    ![Skapa token för Figma](media/Figma-provisioning-tutorial/token.png)

5. Kopiera värdet för  **API-token** . Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt Figma-program i Azure Portal. 

    ![Skapa token för Figma](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Lägg till Figma från galleriet

Om du vill konfigurera Figma för automatisk användar etablering med Azure AD måste du lägga till Figma från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Figma**, väljer **Figma** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Figma i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurera automatisk användar etablering till Figma 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Figma baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Figma genom att följa anvisningarna i [självstudien om enkel inloggning med Figma](figma-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Konfigurera automatisk användar etablering för Figma i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Figma**.

    ![Figma-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://www.figma.com/scim/v2/<TenantID>` i **klient-URL** där **TenantID** är värdet som du hämtade från Figma tidigare. Mata in värdet för **API-token** i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Figma. Om anslutningen Miss lyckas kontrollerar du att Figma-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Figma**.

    ![Figma användar mappningar](media/Figma-provisioning-tutorial/figma05.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Figma i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Figma för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Figma-användarattribut](media/Figma-provisioning-tutorial/figma06.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Figma ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Figma genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Figma.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)