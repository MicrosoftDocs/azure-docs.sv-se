---
title: 'Självstudie: Konfigurera 4me för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till 4me.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: beb580a02e1db80cf2d74f8167a98c9ead170810
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538781"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Självstudie: Konfigurera 4me för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i 4me och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till 4me.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En 4me-klient](https://www.4me.com/trial/)
* Ett användar konto i 4me med administratörs behörighet.

## <a name="add-4me-from-the-gallery"></a>Lägg till 4me från galleriet

Innan du konfigurerar 4me för automatisk användar etablering med Azure AD måste du lägga till 4me från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till 4me från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **4me**, väljer **4me** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![4me i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Tilldela användare till 4me

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till 4me. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till 4me genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Viktiga tips för att tilldela användare till 4me

* Vi rekommenderar att en enda Azure AD-användare tilldelas 4me för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till 4me måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurera automatisk användar etablering till 4me 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i 4me baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för 4me genom att följa anvisningarna i [självstudien om enkel inloggning med 4me](4me-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Konfigurera automatisk användar etablering för 4me i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **4me** i listan med program.

    ![4me-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Om du vill hämta **klient-URL: en** och den **hemliga token** för ditt 4me-konto följer du genom gången enligt beskrivningen i steg 6.

6. Logga in på din 4me-administratörs konsol. Navigera till **Inställningar**.

    ![4me-inställningar](media/4me-provisioning-tutorial/4me01.png)

    Skriv in **appar** i Sök fältet.

    ![4me-appar](media/4me-provisioning-tutorial/4me02.png)

    Öppna List rutan **scim** för att hämta den hemliga token och scim-slutpunkten.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till 4me. Om anslutningen Miss lyckas kontrollerar du att 4me-kontot har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till 4me**.

    ![4me användar mappningar](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Granska de användarattribut som synkroniseras från Azure AD till 4me i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i 4me för uppdaterings åtgärder. Kontrol lera att [4me stöder filtrering](https://developer.4me.com/v1/scim/users/) av matchande attribut som du har valt. Välj knappen **Spara** för att spara ändringarna.

    ![4me användar mappningar](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till 4me**.

    ![4me användar mappningar](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Granska gruppattributen som synkroniseras från Azure AD till 4me i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i 4me för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![4me grupp mappningar](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för 4me ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till 4me genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på 4me.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* 4me har olika SCIM slut punkts-URL: er för test-och produktions miljöer. Den tidigare slutar med **. frågor och svar** när den senare slutar med **. com**
* 4me-genererade hemliga token har ett utgångs datum på en månad från generation.
* 4me stöder inte **borttagnings** åtgärder

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
