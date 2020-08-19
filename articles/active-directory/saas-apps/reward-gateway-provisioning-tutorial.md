---
title: 'Självstudie: Konfigurera belönings-Gateway för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till belöna Gateway.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 298ca69a659bec2d5262b344c274fac7371390f1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88534378"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Självstudie: Konfigurera belönings-Gateway för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i belönings-gateway och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper för att ta med Gateway.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* En [belöna Gateway-klient](https://www.rewardgateway.com/).
* Ett användar konto i belöna Gateway med administratörs behörighet.

## <a name="assigning-users-to-reward-gateway"></a>Tilldela användare till belönad Gateway 

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till belöna Gateway. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till belöna Gateway genom att följa anvisningarna i [tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md).


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Viktiga tips för att tilldela användare till belönad Gateway 

* Vi rekommenderar att en enda Azure AD-användare tilldelas en belönings-Gateway för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till belöna Gateway måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-reward-gateway--for-provisioning"></a>Konfigurera belönings-Gateway för etablering
Innan du konfigurerar belönings-Gateway för automatisk användar etablering med Azure AD måste du aktivera SCIM etablering på Belönad Gateway.

1. Logga in på [administrations konsolen för din belönings Gateway](https://rewardgateway.photoshelter.com/login/). Klicka på **integrationer**.

    ![Administratörs konsol för belönings Gateway](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Välj **min integrering**.

    ![Administratörs konsol för belönings Gateway](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Kopiera värdena för **scim-URL (v2)** och **OAuth Bearer-token**. Dessa värden anges i fältet klient-URL och hemligt token på fliken etablering i ditt belöna Gateway-program i Azure Portal.

    ![Administratörs konsol för belönings Gateway](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Lägg till belöna Gateway från galleriet

Om du vill konfigurera belönings-Gateway för automatisk användar etablering med Azure AD måste du lägga till belöna Gateway från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Gör så här om du vill lägga till en belönings-Gateway från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **belönings-Gateway**, väljer **belöna Gateway** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Belönings-gateway i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Konfigurera automatisk användar etablering till belöna Gateway  

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i belönings-gatewayen utifrån användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för belönings-gatewayen genom att följa anvisningarna i [själv studie kursen om belönings-Gateway](reward-gateway-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Konfigurera automatisk användar etablering för belönings-gateway i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **belönings-Gateway**.

    ![Länken belöna gateway i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **scim-URL: en (v2)** och **OAuth Bearer-token** som HÄMTAdes tidigare i **klient-URL** respektive **hemlig token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till belöna Gateway. Om anslutningen Miss lyckas kontrollerar du att ditt konto för belönings-Gateway har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till belöna Gateway**.

    ![Administratörs konsol för belönings Gateway](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till belöna gateway i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i belönings-Gateway för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Administratörs konsol för belönings Gateway](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för belöna Gateway ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till belönings-gatewayen genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på belönings-Gateway.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

Belönings-gatewayen stöder för närvarande inte grupp etablering.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
