---
title: 'Självstudie: Konfigurera en federerad katalog för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till federerade kataloger.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: c083b0ca7fa506bb951a9ef27d2af04d61482eda
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555175"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Självstudie: Konfigurera en federerad katalog för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i den federerade katalogen och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till federerade kataloger.

> [!NOTE]
>  I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En federerad katalog](https://www.federated.directory/pricing).
* Ett användar konto i en federerad katalog med administratörs behörighet.

## <a name="assign-users-to-federated-directory"></a>Tilldela användare till federerad katalog
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till den federerade katalogen. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till en federerad katalog genom att följa anvisningarna här:

 * [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Viktiga tips för att tilldela användare till federerade kataloger
 * Vi rekommenderar att en enda Azure AD-användare tilldelas en federerad katalog för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till en federerad katalog måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med standard åtkomst rollen undantas från etablering.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Konfigurera en federerad katalog för etablering

Innan du konfigurerar en federerad katalog för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering i federerade kataloger.

1. Logga in i den [federerade katalogens administratörs konsol](https://federated.directory/of)

    ![Själv studie kurs om federerade kataloger](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navigera till **kataloger > användar kataloger** och välj din klient. 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Om du vill skapa en permanent Bearer-token navigerar du till **Katalog nycklar > skapa ny nyckel.** 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/federated01.png)

4. Skapa en katalog nyckel. 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Kopiera värdet **för åtkomsttoken.** Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt federerade katalog program i Azure Portal. 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Lägg till federerad katalog från galleriet

Om du vill konfigurera en federerad katalog för automatisk användar etablering med Azure AD måste du lägga till en federerad katalog från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Gör så här om du vill lägga till en federerad katalog från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök anger du **federerad katalog**och väljer **federerad katalog** i resultat panelen.

    ![Federerad katalog i resultat listan](common/search-new-app.png)

5. Navigera till **URL:** en som marker ATS nedan i en separat webbläsare. 

    ![federerad katalog](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Klicka på **Logga**in.

    ![federerad katalog](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Eftersom den federerade katalogen är en OpenIDConnect-app väljer du att logga in på en federerad katalog med ditt Microsoft Work-konto.
    
    ![federerad katalog](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. När autentiseringen är klar godkänner du frågan om medgivande för sidan medgivande. Programmet läggs sedan till automatiskt i din klient organisation och du omdirigeras till ditt federerade katalog konto.

    ![federerad katalog Lägg till SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurera automatisk användar etablering till federerad katalog 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i federerade kataloger baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Konfigurera automatisk användar etablering för federerad katalog i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **federerad katalog**.

    ![Länken federerad katalog i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://api.federated.directory/v2/` i klient-URL. Mata in det värde som du hämtade och sparade tidigare från den externa katalogen i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till den federerade katalogen. Om anslutningen Miss lyckas kontrollerar du att ditt federerade katalog konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till federerad katalog**.

    ![Själv studie kurs om federerade kataloger](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Granska de användarattribut som synkroniseras från Azure AD till den federerade katalogen i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i den federerade katalogen för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Själv studie kurs om federerade kataloger](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för federerad katalog ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till den federerade katalogen genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på den federerade katalogen.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
