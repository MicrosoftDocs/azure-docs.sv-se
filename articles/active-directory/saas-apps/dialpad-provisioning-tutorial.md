---
title: 'Självstudie: Konfigurera dialpad för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till dialpad.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: b88e618da3f8a23c0517aaeb251e54bf559fc468
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014524"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Självstudie: Konfigurera dialpad för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i dialpad och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till dialpad.

> [!NOTE]
>  I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

> Den här kopplingen är för närvarande en för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En dialpad-klient](https://www.dialpad.com/pricing/).
* Ett användar konto i dialpad med administratörs behörighet.

## <a name="assign-users-to-dialpad"></a>Tilldela användare till dialpad
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till dialpad. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till dialpad genom att följa anvisningarna här:
 
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Viktiga tips för att tilldela användare till dialpad

 * Vi rekommenderar att en enda Azure AD-användare tilldelas dialpad för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till dialpad måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med standard åtkomst rollen undantas från etablering.

## <a name="setup-dialpad-for-provisioning"></a>Konfigurera dialpad för etablering

Innan du konfigurerar dialpad för automatisk användar etablering med Azure AD måste du hämta viss etablerings information från dialpad.

1. Logga in på [dialpad-administratörskonsolen](https://dialpadbeta.com/login) och välj **Administratörs inställningar**. Se till att **mitt företag** är markerat i list rutan. Navigera till **autentisering > API-nycklar**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="Skärm bild av dialpad-administratörskonsolen med inställnings ikonen, mitt företags-, autentiserings-och P I-nycklar markerade och mitt företag har valt." border="false":::

2. Generera en ny nyckel genom att klicka på **Lägg till en nyckel** och konfigurera egenskaperna för din hemliga token.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="Skärm bild av sidan för P-I-nycklar i dialpad-administratörskonsolen. Lägg till en nyckel markeras." border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="Skärm bild av sidan Redigera en P-i-nyckel i dialpad-administratörskonsolen. Knappen Spara är markerad." border="false":::

3. Klicka på knappen **Klicka för att Visa värde** för din nyligen skapade API-nyckel och kopiera värdet som visas. Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt dialpad-program i Azure Portal. 

    ![Skapa token för dialpad](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Lägg till dialpad från galleriet

Om du vill konfigurera dialpad för automatisk användar etablering med Azure AD måste du lägga till dialpad från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till dialpad från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **dialpad** och väljer **dialpad** i panelen resultat.
    ![Dialpad i resultat listan](common/search-new-app.png)

5. Navigera till **URL:** en som marker ATS nedan i en separat webbläsare. 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="Skärm bild av en sida som visar information om dialpad-appen. Under U R L visas en adress i listan och den är markerad." border="false":::

6. I det övre högra hörnet väljer du logga in **> använder dialpad online**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Skärm bild av dialpad-webbplatsen. Logga in är markerat och fliken Logga in är öppen. Använd dialpad online är också markerat." border="false":::

7. Som dialpad är en OpenIDConnect-app väljer du att logga in på dialpad med ditt Microsoft Work-konto.

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Skärm bild av sidan för att skapa samtal på dialpad-webbplatsen. Knappen Logga in med Office 365 är markerad." border="false":::

8. När autentiseringen är klar godkänner du frågan om medgivande för sidan medgivande. Programmet läggs sedan till automatiskt i din klient organisation och du omdirigeras till ditt dialpad-konto.

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="Skärm bild som visar en Microsoft-autentiseringsnyckel som säger att dialpad-appen har begärt åtkomst till vissa data. Knappen acceptera är markerad." border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurera automatisk användar etablering till dialpad

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i dialpad baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Konfigurera automatisk användar etablering för dialpad i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **dialpad**.

    ![Dialpad-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://dialpad.com/scim` i **klient-URL**. Mata in det värde som du hämtade och sparade tidigare från dialpad i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till dialpad. Om anslutningen Miss lyckas kontrollerar du att dialpad-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till dialpad**.

    ![Dialpad användar mappningar](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Granska de användarattribut som synkroniseras från Azure AD till dialpad i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i dialpad för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Dialpad-användarattribut](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för dialpad ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till dialpad genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på dialpad.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Kopplings begränsningar
* Dialpad stöder inte gruppens namn redan idag. Det innebär att alla ändringar av **DisplayName** för en grupp i Azure AD inte kommer att uppdateras och avspeglas i dialpad.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
