---
title: 'Självstudie: Konfigurera ett nät för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till att utnyttja.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 56a865de8cb1be079f4935ef2a8f840f10589b26
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550018"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Självstudie: Konfigurera ett nät för automatisk användar etablering

I den här artikeln får du lära dig hur du konfigurerar Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användare eller grupper för att utnyttja.

> [!NOTE]
> Den här artikeln beskriver en anslutning som är byggd ovanpå Azure AD-tjänsten för användar etablering. Viktig information om den här tjänsten och svar på vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande en för hands version. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här artikeln förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En nät klient](https://harness.io/pricing/)
* Ett användar konto i ett nät med *Administratörs* behörighet

## <a name="assign-users-to-harness"></a>Tilldela användare till ett nät

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering ska du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till-nätet. Du kan sedan tilldela dessa användare eller grupper genom att följa anvisningarna i [tilldela en användare eller grupp till en Enterprise-App](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Viktiga tips för att tilldela användare till-nätet

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till nät för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare eller grupper kan tilldelas senare.

* När du tilldelar en användare att använda måste du välja en giltig programspecifik roll (om tillgängligt) i dialog rutan **tilldelning** . Användare med *standard åtkomst* rollen undantas från etablering.

## <a name="set-up-harness-for-provisioning"></a>Konfigurera ett nät för etablering

1. Logga in på din- [administrations konsol](https://app.harness.io/#/login)och gå sedan till **kontinuerlig**  >  **hantering**av säkerhets åtkomst.

    ![Nät administrations konsol](media/harness-provisioning-tutorial/admin.png)

1. Välj **API-nycklar**.

    ![Länk till API-nyckel](media/harness-provisioning-tutorial/apikeys.png)

1. Välj **Lägg till API-nyckel**. 

    ![Lägg till API-nyckel länk](media/harness-provisioning-tutorial/addkey.png)

1. I fönstret **Lägg till API-nyckel** gör du följande:

    ![Lägg till API-nyckel fönster](media/harness-provisioning-tutorial/title.png)
   
   a. I rutan **namn** anger du ett namn för nyckeln.  
   b. I list rutan **behörigheter som ärvs från** väljer du ett alternativ. 
   
1. Välj **Skicka**.

1. Kopiera **nyckeln** för senare användning i den här självstudien.

    ![Skapa token för nätet](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Lägg till ett nät från galleriet

Innan du konfigurerar ett nät för automatisk användar etablering med Azure AD måste du lägga till ett nät från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**.

    ![Knappen "Azure Active Directory"](common/select-azuread.png)

1. Välj **företags program**  >  **alla program**.

    ![Länken alla program](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen "nytt program"](common/add-new-app.png)

1. I sökrutan anger du **nät**, väljer **nät** i resultat listan och väljer sedan knappen **Lägg till** för att lägga till programmet.

    ![Nät i resultat listan](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Konfigurera automatisk användar etablering för att utnyttja 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper i ett nät baserat på användar-eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för ett nät genom att följa anvisningarna i [självstudien för enkel inloggning med enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). Du kan konfigurera enkel inloggning oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om nät SCIM-slutpunkten finns i artikeln om [API-nycklar](https://docs.harness.io/article/smloyragsm-api-keys) .

Gör så här för att konfigurera automatisk användar etablering för nät i Azure AD:

1. I [Azure Portal](https://portal.azure.com)väljer du **företags program**  >  **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. I listan program väljer du **nät**.

    ![Länken nät i listan program](common/all-applications.png)

1. Välj **etablering**.

    ![Etablerings knappen](common/provisioning.png)

1. I list rutan **etablerings läge** väljer du **Automatisk**.

    ![List rutan "etablerings läge"](common/provisioning-automatic.png)

1. Under **admin-autentiseringsuppgifter**gör du följande:

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. I rutan **klient webb adress** anger du **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** .  
   b. I rutan **hemlig token** anger du värdet för scim-autentisering som du sparade i steg 6 i avsnittet "Konfigurera ett nät för etablering".  
   c. Välj **Testa anslutning** för att se till att Azure AD kan ansluta till nätet. Om anslutningen Miss lyckas kontrollerar du att ditt konto för ditt konto har *Administratörs* behörighet och försöker sedan igen.

1. I rutan **aviserings-e** -postadress anger du e-postadressen till en person eller grupp som ska ta emot etablerings fel meddelanden och markerar sedan kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Rutan "e-postavisering"](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. Under **mappningar**väljer **du synkronisera Azure Active Directory användare att använda**.

    !["" Synkronisera Azure Active Directory användare till nät "-länken](media/harness-provisioning-tutorial/usermappings.png)

1. Under **attribut mappningar**granskar du de användarattribut som synkroniseras från Azure AD till att utnyttja. De attribut som väljs som *matchning* används för att matcha användar konton i drift för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    ![Fönster för att koppla användar-attribut](media/harness-provisioning-tutorial/userattributes.png)

1. Under **mappningar**väljer **du synkronisera Azure Active Directory grupper att**använda.

    ![Använd länken "synkronisera Azure Active Directory grupper till nätet"](media/harness-provisioning-tutorial/groupmappings.png)

1. Under **attribut mappningar**granskar du de Gruppattribut som synkroniseras från Azure AD till att utnyttja. Attributen som väljs som *matchande* egenskaper används för att matcha grupperna i-funktioner för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    ![Fönster för grupp mappningar](media/harness-provisioning-tutorial/groupattributes.png)

1. Information om hur du konfigurerar omfångs filter finns i [attribut-baserad program etablering med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aktivera Azure AD Provisioning-tjänsten för nät genom att växla **etablerings status** växeln **på på**under **Inställningar**.

    ![Etablerings status växlar växlat till "på"](common/provisioning-toggle-on.png)

1. Under **Inställningar**i list rutan **omfattning** väljer du hur du vill synkronisera de användare eller grupper som du håller på att tillhandahålla.

    ![Etablerings omfång](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Knappen för att spara etablering](common/provisioning-configuration-save.png)

Den här åtgärden startar den inledande synkroniseringen av de användare eller grupper som du håller på att konfigurera. Den inledande synkroniseringen tar längre tid att utföra än senare. Synkroniseringar sker ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. Gå till avsnittet **synkroniseringsinformation om** du vill övervaka förloppet. Du kan också följa länkar till en etablerings aktivitets rapport, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på nätet.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapporten om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
