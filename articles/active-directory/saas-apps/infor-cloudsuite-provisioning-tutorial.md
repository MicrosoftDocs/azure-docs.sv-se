---
title: 'Självstudie: Konfigurera information CloudSuite för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till information CloudSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: b96166b789471e746e2a6434c5e0aa9b9a945ab3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550562"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Självstudie: Konfigurera information CloudSuite för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i infor CloudSuite och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till information CloudSuite.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En infor CloudSuite-klient](https://www.infor.com/products/infor-os)
* Ett användar konto i information CloudSuite med administratörs behörighet.

## <a name="assigning-users-to-infor-cloudsuite"></a>Tilldela användare till information CloudSuite

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till information CloudSuite. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till information CloudSuite genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Viktiga tips för att tilldela användare till information CloudSuite

* Vi rekommenderar att en enskild Azure AD-användare tilldelas till information CloudSuite för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till information CloudSuite måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Konfigurera information CloudSuite för etablering

1. Logga in på din [infor-CloudSuite-administratörs konsol](https://www.infor.com/customer-center). Klicka på användar ikonen och gå sedan till **användar hantering**.

    ![Information CloudSuite-administratörs konsol](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Klicka på Meny ikonen i det vänstra övre hörnet på skärmen. Klicka på **Hantera**.

    ![Information CloudSuite Lägg till SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigera till **scim-konton**.

    ![Information CloudSuite SCIM-konto](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Lägg till en administratörs användare genom att klicka på plus ikonen. Ange ett **scim lösen ord** och Skriv samma lösen ord under **Bekräfta lösen ord**. Klicka på mappikonen för att spara lösen ordet. Sedan visas en **användar identifierare** som skapats för administratörs användaren.

    ![Information CloudSuite admin-användare](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Information om CloudSuite-lösenord](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![CloudSuite-ID för information](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Om du vill generera Bearer-token kopierar du **användar identifieraren** och **scim lösen ord**. Klistra in dem i anteckningar + + avgränsade med kolon. Koda strängvärdet genom att navigera till plugin-program **> MIME-verktyg > Basic64-koda**. 

    ![CloudSuite-ID för information](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Kopiera Bearer-token. Det här värdet anges i fältet Hemlig token på fliken etablering i CloudSuite-programmet i informationens program i Azure Portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Lägg till information CloudSuite från galleriet

Innan du konfigurerar information CloudSuite för automatisk användar etablering med Azure AD måste du lägga till information CloudSuite från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till information CloudSuite från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **information CloudSuite**, väljer **infor CloudSuite** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Information CloudSuite i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Konfigurera automatisk användar etablering till information CloudSuite 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i information CloudSuite baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för information CloudSuite, enligt anvisningarna i [självstudien om information CloudSuite för enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

> [!NOTE]
> Om du vill veta mer om SCIM-slutpunkten för infor CloudSuite, se [detta](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Konfigurera automatisk användar etablering för information CloudSuite i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **information CloudSuite**.

    ![Infor-CloudSuite-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` i **klient-URL**. Mata in värdet för Bearer-token som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till information CloudSuite. Om anslutningen Miss lyckas kontrollerar du att ditt information CloudSuite-konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till information CloudSuite**.

    ![Information CloudSuite användar mappningar](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till information CloudSuite i avsnittet mappning av **attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i information CloudSuite för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Information CloudSuite-användarattribut](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till information CloudSuite**.

    ![Information CloudSuite Group-mappningar](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till information CloudSuite i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i infor-CloudSuite för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Information CloudSuite Group attributes](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för information CloudSuite ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till information-CloudSuite genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på INFOER-CloudSuite.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)