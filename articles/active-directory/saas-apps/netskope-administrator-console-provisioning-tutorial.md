---
title: 'Självstudie: Konfigurera Netskope Administratörskonsol för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Netskope Administratörskonsol.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: cef356619d437d53d94c8f5d3be67cf6ac95501c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554564"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Netskope Administratörskonsol för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Netskope Administratörskonsol och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Netskope Administratörskonsol.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Netskope Administratörskonsol klient](https://www.netskope.com/)
* Ett användar konto i Netskope Administratörskonsol med administratörs behörighet.

## <a name="assigning-users-to-netskope-administrator-console"></a>Tilldela användare till Netskope Administratörskonsol

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Netskope Administratörskonsol. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Netskope Administratörskonsol genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Viktiga tips för att tilldela användare till Netskope Administratörskonsol

* Vi rekommenderar att en enda Azure AD-användare tilldelas Netskope Administratörskonsol för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Netskope Administratörskonsol måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Konfigurera Netskope Administratörskonsol för etablering

1. Logga in på din [Netskope-administratörskonsol-administratörs konsol](https://netskope.goskope.com/). Navigera till **start > inställningar**.

    ![Netskope Administratörskonsol administratörs konsol](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigera till **verktyg**. Gå till **verktyg** -menyn och navigera till **katalog verktyg > scim-integrering**.

    ![Netskope Administratörskonsol-verktyg](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Administratörskonsol Lägg till SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Rulla nedåt och klicka på knappen **Lägg till token** . Ange ett **klient namn** i dialog rutan **Lägg till OAuth-klientcertifikat** och klicka på knappen **Spara** .

    ![Netskope Administratörskonsol Lägg till token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Administratörskonsol klient namn](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Kopiera **scim-serverns URL** och **token**. De här värdena anges i fälten klient-URL och hemlig token på fliken etablering i Netskope Administratörskonsol program i Azure Portal.

    ![Netskope Administratörskonsol skapa token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Lägg till Netskope Administratörskonsol från galleriet

Innan du konfigurerar Netskope Administratörskonsol för automatisk användar etablering med Azure AD måste du lägga till Netskope Administratörskonsol från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Gör så här om du vill lägga till Netskope Administratörskonsol från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Netskope administratörskonsol**, väljer **Netskope administratörskonsol** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Netskope Administratörskonsol i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Konfigurera automatisk användar etablering till Netskope Administratörskonsol 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Netskope Administratörskonsol baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Netskope Administratörskonsol genom att följa anvisningarna i [självstudien om Netskope administratörskonsol enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om Netskope Administratörskonsol SCIM-slutpunkten finns [här](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Konfigurera automatisk användar etablering för Netskope Administratörskonsol i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Netskope administratörskonsol**.

    ![Netskope-Administratörskonsol-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** har **InSCIM-serverns URL** -värde hämtats tidigare i **klient-URL: en**. Mata in **token** -värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Netskope administratörskonsol. Om anslutningen Miss lyckas kontrollerar du att Netskope Administratörskonsol-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Netskope administratörskonsol**.

    ![Netskope Administratörskonsol användar mappningar](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Netskope Administratörskonsol i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Netskope-administratörskonsol för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Netskope Administratörskonsol användarattribut](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att Netskope administratörskonsol**.

    ![Netskope Administratörskonsol grupp mappningar](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Netskope Administratörskonsol i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Netskope-administratörskonsol för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Netskope Administratörskonsol gruppens attribut](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Netskope Administratörskonsol ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Netskope Administratörskonsol genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Netskope administratörskonsol.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

