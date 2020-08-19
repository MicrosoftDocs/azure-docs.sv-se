---
title: 'Självstudie: Konfigurera Brivo OnAir Identity Connector för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Brivo OnAir Identity Connector.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 305a369733ce0435b5f892efd0d447b0940143fc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547757"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Brivo OnAir Identity Connector för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Brivo OnAir Identity Connector och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Brivo OnAir Identity Connector.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En klient för Brivo OnAir Identity Connector](https://www.brivo.com/lp/quote)
* Ett användar konto i Brivo OnAir Identity Connector med administratörs behörighet för erfarna administratörer.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Tilldela användare till Brivo OnAir Identity Connector

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Brivo OnAir Identity Connector. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Brivo OnAir Identity Connector genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Viktiga tips för att tilldela användare till Brivo OnAir Identity Connector

* Vi rekommenderar att en enda Azure AD-användare tilldelas Brivo OnAir Identity Connector för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Brivo OnAir Identity Connector måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Konfigurera Brivo OnAir Identity Connector för etablering

1. Logga in på [Administratörs konsolen för Brivo OnAir Identity Connector](https://acs.brivo.com/login/). Navigera till **kontots > konto inställningar**.

   ![Administratörs konsol för Brivo OnAir Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. Klicka på fliken **Azure AD** . På informations sidan för **Azure AD** anger du lösen ordet för ditt administratörs konto på högre dator. Klicka på **Skicka**.

   ![Brivo OnAir Identity Connector Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. Klicka på knappen **Kopiera token** och spara den **hemliga token**. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt Brivo OnAir Identity Connector-program i Azure Portal.

   ![Brivo OnAir Identity Connector-token](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Lägg till Brivo OnAir Identity Connector från galleriet

Innan du konfigurerar Brivo OnAir Identity Connector för automatisk användar etablering med Azure AD måste du lägga till Brivo OnAir Identity Connector från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Gör så här om du vill lägga till Brivo OnAir Identity Connector från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Brivo OnAir Identity Connector**i sökrutan, Välj **Brivo OnAir Identity Connector** i panelen resultat och klicka sedan på knappen **Lägg** till för att lägga till programmet.

    ![Brivo OnAir Identity Connector i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Konfigurera automatisk användar etablering till Brivo OnAir Identity Connector 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Brivo OnAir Identity Connector baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Konfigurera automatisk användar etablering för Brivo OnAir Identity Connector i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Brivo OnAir Identity Connector**.

    ![Länken Brivo OnAir Identity Connector i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://scim.brivo.com/ActiveDirectory/v2/` i **klient-URL**. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Brivo OnAir Identity Connector. Om anslutningen Miss lyckas kontrollerar du att Brivo OnAir Identity Connector-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Brivo OnAir Identity Connector**.

    ![Användar mappningar för Brivo OnAir Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Granska de användarattribut som synkroniseras från Azure AD till Brivo OnAir Identity Connector i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Brivo OnAir Identity Connector för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Brivo OnAir Identity Connector-användarattribut](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper för att Brivo OnAir Identity Connector**.

    ![Brivo OnAir Identity Connectors Group-mappningar](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Brivo OnAir Identity Connector i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Brivo OnAir Identity Connector för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Brivo OnAir Identity Connector Group attribut](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Brivo OnAir Identity Connector ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Brivo OnAir Identity Connector genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Brivo OnAir Identity Connector.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

