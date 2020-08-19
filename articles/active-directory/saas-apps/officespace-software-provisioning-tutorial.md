---
title: 'Självstudie: Konfigurera OfficeSpace-programvara för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till OfficeSpace-programvara.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: ab2fd7f78373f9fee349e59c374088f67f165339
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554213"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Självstudie: Konfigurera OfficeSpace-programvara för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i OfficeSpace-programvaran och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till OfficeSpace-programvaran.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* En [OfficeSpace program varu klient](https://www.officespacesoftware.com/)
* Ett användar konto i OfficeSpace-programvara med administratörs behörighet.

## <a name="assigning-users-to-officespace-software"></a>Tilldela användare till OfficeSpace-programvara

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till OfficeSpace-programvaran. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till OfficeSpace program vara genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Viktiga tips för att tilldela användare till OfficeSpace-programvara

* Vi rekommenderar att en enda Azure AD-användare tilldelas till OfficeSpace-programvaran för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till OfficeSpace-programvara måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-officespace-software-for-provisioning"></a>Konfigurera OfficeSpace-programvara för etablering

1. Logga in på [OfficeSpace-program administratörs konsolen](https://support.officespacesoftware.com/hc). Navigera till **inställningar > kopplingar**.

    ![OfficeSpace program administratörs konsol](media/officespace-software-provisioning-tutorial/settings.png)

2.  Navigera till **Directory-synkronisering > scim**.

    ![OfficeSpace program vara Lägg till SCIM](media/officespace-software-provisioning-tutorial/scim.png)

3.  Kopiera **scim-autentiseringstoken**. Det här värdet anges i fältet Hemlig token på fliken etablering i OfficeSpace-programmet i Azure Portal.

    ![OfficeSpace program vara skapa token](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>Lägg till OfficeSpace-programvara från galleriet

Innan du konfigurerar OfficeSpace-programvaran för automatisk användar etablering med Azure AD måste du lägga till OfficeSpace-program från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Gör så här om du vill lägga till OfficeSpace-programvara från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **OfficeSpace program vara**, väljer **OfficeSpace program vara** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![OfficeSpace program vara i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Konfigurera automatisk användar etablering för OfficeSpace-programvara 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i OfficeSpace program vara utifrån användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för OfficeSpace-program genom att följa anvisningarna i [självstudien om OfficeSpace program vara för enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Konfigurera automatisk användar etablering för OfficeSpace-program i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **OfficeSpace-programvara**.

    ![OfficeSpace program varu länk i listan program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://<subdomain>.officespacesoftware.com/api/scim/v2/` URL-format i **klient-URL**. Till exempel `https://contoso.officespacesoftware.com/api/scim/v2/`. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till OfficeSpace-programvaran. Om anslutningen Miss lyckas kontrollerar du att OfficeSpace-programvaran har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till OfficeSpace-programvaran**.

    ![Användar mappningar för OfficeSpace-program](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till OfficeSpace-programvaran i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i OfficeSpace-programvaran för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användarattribut för OfficeSpace-programvara](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD Provisioning-tjänsten för OfficeSpace-programvara ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera för att OfficeSpace program vara genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på OfficeSpace-programvaran.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

