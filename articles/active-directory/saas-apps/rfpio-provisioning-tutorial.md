---
title: 'Självstudie: Konfigurera RFPIO för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till RFPIO.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ff859e7d77fd19cd006cf45a6faa737297fdb9a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349647"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Självstudie: Konfigurera RFPIO för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i RFPIO och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till RFPIO.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En RFPIO-klient](https://www.rfpio.com/product/).
* Ett användar konto i RFPIO med administratörs behörighet.

## <a name="assigning-users-to-rfpio"></a>Tilldela användare till RFPIO

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till RFPIO. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till RFPIO genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Viktiga tips för att tilldela användare till RFPIO

* Vi rekommenderar att en enda Azure AD-användare tilldelas RFPIO för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till RFPIO måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-rfpio-for-provisioning"></a>Konfigurera RFPIO för etablering

Innan du konfigurerar RFPIO för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på RFPIO.

1.  Logga in på din RFPIO-administratörs konsol. Längst ned till vänster i administratörs konsolen klickar du på **klient organisation**.

    ![RFPIO-administratörskonsolen](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Klicka på **organisations inställningar**.
    
    ![RFPIO-administratör](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Gå till **användar hanterings**  >  **säkerhet**  >  **scim**.

    ![RFPIO Lägg till SCIM](media/rfpio-provisioning-tutorial/scim.png)

4.  Se till att **Automatisk användar etablering** har Aktiver ATS. Klicka på **generera scim-API-token**.

    ![Skärm bild av S C I M-avsnittet med alternativet Skapa S C I M A P-TOKEn som kallas för.](media/rfpio-provisioning-tutorial/generate.png)

5.  Spara **scim API-token** eftersom denna token inte kommer att visas igen av säkerhets skäl. Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt RFPIO-program i Azure Portal.

    ![Skärm bild av avsnittet S C I M med varnings dialog rutan som visas när du har valt skicka.](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>Lägg till RFPIO från galleriet

Om du vill konfigurera RFPIO för automatisk användar etablering med Azure AD måste du lägga till RFPIO från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till RFPIO från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **RFPIO**, väljer **RFPIO** i resultat panelen och klickar sedan på knappen    **Lägg** till för att lägga till programmet.

    ![RFPIO i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Konfigurera automatisk användar etablering till RFPIO 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i RFPIO baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för RFPIO genom att följa anvisningarna i [självstudien om enkel inloggning med RFPIO](rfpio-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Konfigurera automatisk användar etablering för RFPIO i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **RFPIO**.

    ![RFPIO-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` i **klient-URL**. Ett exempel värde är `https://Azure-test1.rfpio.com/rfpserver/scim/v2` . Mata in värdet för **scim API-token** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till RFPIO. Om anslutningen Miss lyckas kontrollerar du att RFPIO-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till RFPIO**.

    ![RFPIO användar mappningar](media/rfpio-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till RFPIO i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i RFPIO för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![RFPIO-användarattribut](media/rfpio-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för RFPIO ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till RFPIO genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på RFPIO.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* RFPIO har inte stöd för etablering av grupper för närvarande.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
