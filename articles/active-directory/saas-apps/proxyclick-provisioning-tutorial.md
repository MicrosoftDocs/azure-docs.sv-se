---
title: 'Självstudie: Konfigurera Proxyclick för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Proxyclick.
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
ms.openlocfilehash: 54a7723e07bd348e31b564707da9e78e96593d3c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553372"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Proxyclick för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Proxyclick och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Proxyclick.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Proxyclick-klient](https://www.proxyclick.com/pricing)
* Ett användar konto i Proxyclick med administratörs behörighet.

## <a name="add-proxyclick-from-the-gallery"></a>Lägg till Proxyclick från galleriet

Innan du konfigurerar Proxyclick för automatisk användar etablering med Azure AD måste du lägga till Proxyclick från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Proxyclick från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Proxyclick**, väljer **Proxyclick** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Proxyclick i resultat listan](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Tilldela användare till Proxyclick

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Proxyclick. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Proxyclick genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Viktiga tips för att tilldela användare till Proxyclick

* Vi rekommenderar att en enda Azure AD-användare tilldelas Proxyclick för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Proxyclick måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurera automatisk användar etablering till Proxyclick 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Proxyclick baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Proxyclick genom att följa anvisningarna i [självstudien om enkel inloggning med Proxyclick](proxyclick-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Konfigurera automatisk användar etablering för Proxyclick i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Proxyclick**.

    ![Proxyclick-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Om du vill hämta **klient-URL: en** och den **hemliga token** för ditt Proxyclick-konto följer du genom gången enligt beskrivningen i steg 6.

6. Logga in på din [Proxyclick-administratörs konsol](https://app.proxyclick.com/login//?destination=%2Fdefault). Navigera till **Inställningar**  >  **integreringar**  >  **Bläddra i Marketplace**.

    ![Proxyclick-inställningar](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick-integreringar](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Välj **Azure AD**. Klicka på **Installera nu**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick-installation](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Välj **användar etablering** och klicka på **Starta integrering**. 

    ![Proxyclick användar etablering](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Konfigurations gränssnittet för rätt inställningar bör nu visas under **Inställningar**  >  **integreringar**. Välj **Inställningar** under **Azure AD (användar etablering)**.

    ![Skapa Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Du hittar klient- **URL** och **hemlig token** här.

    ![Skapa token för Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till Proxyclick. Om anslutningen Miss lyckas kontrollerar du att Proxyclick-kontot har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Proxyclick**.

    ![Proxyclick användar mappningar](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Proxyclick i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Proxyclick för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Proxyclick-användarattribut](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera Azure AD Provisioning-tjänsten för Proxyclick ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

15. Definiera de användare och/eller grupper som du vill etablera till Proxyclick genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

16. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Proxyclick.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Proxyclick kräver att **e-post** och **användar namn** har samma käll värde. Alla uppdateringar av båda attributen kommer att ändra det andra värdet.
* Proxyclick stöder inte etablering för grupper.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

