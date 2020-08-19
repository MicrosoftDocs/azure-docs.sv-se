---
title: 'Självstudie: Konfigurera Miro för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Miro.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/21/2019
ms.author: Zhchia
ms.openlocfilehash: 99ddbd7a197a6f9a4cb8c50b0643b159dd2d1a1e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518628"
---
# <a name="tutorial-configure-miro-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Miro för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Miro och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Miro.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Miro-klient](https://miro.com/pricing/)
* Ett användar konto i Miro med administratörs behörighet.

## <a name="assigning-users-to-miro"></a>Tilldela användare till Miro

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Miro. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Miro genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-miro"></a>Viktiga tips för att tilldela användare till Miro

* Vi rekommenderar att en enda Azure AD-användare tilldelas Miro för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Miro måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-miro-for-provisioning"></a>Konfigurera Miro för etablering

1.  Om du vill hämta **nödvändig kontakt** Miro support team på support@miro.com . Det här värdet anges i fältet Hemlig token på fliken etablering i ditt Miro-program i Azure Portal.

## <a name="add-miro-from-the-gallery"></a>Lägg till Miro från galleriet

Innan du konfigurerar Miro för automatisk användar etablering med Azure AD måste du lägga till Miro från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Miro från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Miro**, väljer **Miro** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Miro i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-miro"></a>Konfigurera automatisk användar etablering till Miro 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Miro baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Miro genom att följa anvisningarna i [självstudien om enkel inloggning med Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

> [!NOTE]
> Läs mer om Miro SCIM-slutpunkten i [detta](https://help.miro.com/hc/en-us/articles/360036777814).

### <a name="to-configure-automatic-user-provisioning-for-miro-in-azure-ad"></a>Konfigurera automatisk användar etablering för Miro i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Miro**.

    ![Miro-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://miro.com/api/v1/scim` i **klient-URL**. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Miro. Om anslutningen Miss lyckas kontrollerar du att Miro-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Miro**.

    ![Miro användar mappningar](media/miro-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Miro i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Miro för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Miro-användarattribut](media/miro-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Miro**.

    ![Miro grupp mappningar](media/miro-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Miro i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Miro för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna. Avmarkera **skapa** och **ta bort** under **mål objekt åtgärder** eftersom Miro scim API inte har stöd för att skapa och ta bort grupper.

    ![Miro grupp-attribut](media/miro-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Miro ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Miro genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Miro.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Miro SCIM-slutpunkt tillåter inte åtgärder för att **skapa** och **ta bort** grupper. Den stöder bara grupp **uppdaterings** åtgärder.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

