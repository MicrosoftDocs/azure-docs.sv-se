---
title: 'Självstudie: Konfigurera Netskope-användarautentisering för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Netskope-användarautentisering.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 46766a7439185714648572f3f1b9d51ef96abba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357486"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Netskope-användarautentisering för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Netskope och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Netskope användarautentisering.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Netskope-klient för användarautentisering](https://www.netskope.com/)
* Ett användar konto i Netskope-användarautentisering med administratörs behörighet.

## <a name="assigning-users-to-netskope-user-authentication"></a>Tilldela användare till Netskope-användarautentisering

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Netskope-användarautentisering. När du har bestämt dig kan du tilldela dessa användare och/eller grupper för att Netskope användarautentisering genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>Viktiga tips för att tilldela användare Netskope användarautentisering

* Vi rekommenderar att en enda Azure AD-användare tilldelas Netskope för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare behörighet att Netskope, måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>Konfigurera Netskope användarautentisering för etablering

1. Logga in på [administrations konsolen för Netskope User Authentication](https://netskope.goskope.com/). Navigera till **start > inställningar**.

    ![Netskope-administratörs konsol för användarautentisering](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigera till **verktyg**. Gå till **verktyg** -menyn och navigera till **katalog verktyg > scim-integrering**.

    ![Verktyg för Netskope-användarautentisering](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope för användarautentisering Lägg till SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Rulla nedåt och klicka på knappen **Lägg till token** . Ange ett **klient namn** i dialog rutan **Lägg till OAuth-klientcertifikat** och klicka på knappen **Spara** .

    ![Netskope Lägg till token för användarautentisering](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope klient namn för användarautentisering](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Kopiera **scim-serverns URL** och **token**. Dessa värden anges i fälten klient-URL och hemlig token på fliken etablering i Netskope för användar autentisering i Azure Portal.

    ![Skapa token för Netskope User Authentication](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Lägg till Netskope användarautentisering från galleriet

Innan du konfigurerar Netskope användarautentisering för automatisk användar etablering med Azure AD måste du lägga till Netskope-användarautentisering från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Netskope-användarautentisering från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Netskope användarautentisering**, väljer **Netskope användarautentisering** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Netskope användarautentisering i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Konfigurera automatisk användar etablering för Netskope-användarautentisering 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Netskope användarautentisering baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Netskope-användarautentisering genom att följa anvisningarna i [självstudien om enkel inloggning i Netskope User Authentication](./netskope-cloud-security-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Läs mer om SCIM-slutpunkten för användarautentisering i Netskope [.](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>Konfigurera automatisk användar etablering för Netskope-användarautentisering i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Netskope User Authentication**.

    ![Länken Netskope User Authentication i listan program](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** har **InSCIM-serverns URL** -värde hämtats tidigare i **klient-URL: en**. Mata in **token** -värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Netskope-användarautentisering. Om anslutningen Miss lyckas kontrollerar du att kontot för Netskope-autentisering har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare för att Netskope användarautentisering**.

    ![Användar mappningar för Netskope-användarautentisering](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Netskope-användarautentisering i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Netskope användarautentisering för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användarattribut för användarautentisering med Netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att Netskope autentisering av användare**.

    ![Netskope-mappningar för användarautentisering](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Netskope-användarautentisering i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Netskope användarautentisering för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Netskope för autentisering av användar grupp](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Netskope-användarautentisering ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera för att Netskope genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Netskope-användarautentisering.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)