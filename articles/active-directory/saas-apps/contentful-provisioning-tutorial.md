---
title: 'Självstudie: Konfigurera innehåll för automatisk användar etablering med Azure Active Directory'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure Active Directory (Azure AD) till innehåll.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516344"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Självstudie: Konfigurera innehåll för automatisk användar etablering

I den här artikeln beskrivs de steg som du måste utföra i innehåll och i Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS etablerar och avetablerar Azure AD automatiskt användare och grupper till [innehåll](https://www.contentful.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör och hur den fungerar och för vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funktioner som stöds

> [!div class="checklist"]
> * Skapa användare med innehåll
> * Ta bort användare i innehåll utan att ha åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och innehåll
> * Etablera grupper och grupp medlemskap i innehåll
> * [Enkel inloggning](contentful-tutorial.md) till innehåll (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md). 
* Ett användar konto i Azure AD som har [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* Ett administrerat organisations konto som har en prenumeration som stöder system för SCIM-etablering (Cross-Domain Identity Management). Kontakta [innehålls support](mailto:support@contentful.com)om du har frågor om din organisations prenumeration.
 
## <a name="plan-your-provisioning-deployment"></a>Planera etablering av distributionen

1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och contentable](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Konfigurera innehåll som stöder etablering med Azure AD

1. Skapa ett användar konto för **tjänsten** i innehåll. Alla etablerings behörigheter för Azure tillhandahålls via det här kontot. Vi rekommenderar att du väljer **ägare** som organisations roll för det här kontot.

2. Logga in till innehåll som **tjänst användare**.

3. På den vänstra menyn väljer du **organisations inställningar**  >  **åtkomst verktyg**  >  **användar etablering**.

   ![Skärm bild av menyn organisations inställningar med innehåll med användar etablering markerad under åtkomst verktyg.](media/contentful-provisioning-tutorial/access.png)

4. Kopiera och spara **scim-URL: en**. Du anger det här värdet i Azure Portal på fliken **etablering** i ditt innehålls bara program.

5. Välj **skapa personlig** åtkomsttoken.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. I det modala fönstret anger du ett namn för din personliga åtkomsttoken och väljer sedan **generera**.

7. SCIM-URL: en och den hemliga token genereras. Kopiera och spara dessa värden. Du anger dessa värden på fliken **etablering** i ditt innehålls bara program i Azure Portal.

    ![Skärm bild av rutan personlig åtkomsttoken med C F P A T och namnet på plats hållaren för token markerat.](media/contentful-provisioning-tutorial/token.png)


Kontakta [innehålls stöds](mailto:support@contentful.com)om du har frågor när du konfigurerar etableringen i den innehållsbaserade administrations konsolen.

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Lägg till innehåll med hjälp av Azure AD-programgalleriet

Om du vill hantera etablering till innehåll kan du lägga till innehåll som finns i program galleriet för Azure AD. Om du tidigare har konfigurerat innehåll för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app för att först testa integreringen. Lär dig hur du [lägger till ett program i galleriet](../manage-apps/add-application-portal.md). 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Definiera vem som ska finnas i etableringsomfånget 

Du kan använda Azure AD Provisioning-tjänsten för det omfång som ska tillhandahållas baserat på tilldelning till programmet eller baserat på attribut för användaren eller gruppen. 

Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning slutför du stegen för att [tilldela användare och grupper till programmet](../manage-apps/assign-user-or-group-access-portal.md).

Om du väljer att omfånget som endast ska tillhandahållas baserat på användarens eller gruppens attribut använder du ett omfångs filter för att [definiera villkorliga regler för etablering av användar konton](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till innehåll måste du välja en annan roll än **standard åtkomst**. Användare som har standard åtkomst rollen undantas från etablering och anges i etablerings loggarna som inte faktiskt berättigade. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller. 
* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etablerings omfång är inställt på tilldelade användare och grupper kan du kontrol lera omfattningen genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attribut-baserat omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Konfigurera automatisk användar etablering till innehåll 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper i en testapp baserat på användar-eller grupp tilldelningar i Azure AD.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Konfigurera automatisk användar etablering för innehåll i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program** och välj sedan **alla program**.

   ![Skärm bild som visar menyn företags program i Azure Portal, där alla program är markerade.](common/enterprise-applications.png)

2. Välj **innehållet** i listan program.

   ![Skärm bild som visar de första 20 resultaten som returneras i listan program.](common/all-applications.png)

3. Välj fliken **Etablering**.

   ![Skärm bild av fliken etablering markerad i avsnittet hantera på den vänstra menyn.](common/provisioning.png)

4. Ange **etablerings läget** till **Automatisk**.

   ![Skärm bild som visar alternativen för etablerings läge, med automatiskt markerat.](common/provisioning-automatic.png)

5. I avsnittet **admin-autentiseringsuppgifter** anger du din innehålls innehavares URL och den hemliga token. Välj **Testa anslutning** för att säkerställa att Azure AD kan ansluta till innehåll. Om anslutningen Miss lyckas kontrollerar du att ditt innehålls begränsade konto har administratörs behörighet och försöker sedan igen.

   ![Skärm bild som visar text rutorna för klient U R L och hemlig token med knappen Testa anslutning markerad.](common/provisioning-testconnection-tenanturltoken.png)

6. I **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelandena och markerar sedan kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

   ![Skärm bild som visar text rutan e-postavisering.](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory användare till innehåll**.

9. I avsnittet **attribut-mappning** granskar du de användarattribut som synkroniseras från Azure AD till innehåll. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i innehåll som kan åtgärdas. Om du väljer att ändra det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att innehålls förhindrad API stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |name.givenName|Sträng|
   |name.familyName|Sträng|

10. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till innehåll**.

11. I avsnittet **attribut-mappning** granskar du de Gruppattribut som synkroniseras från Azure AD till innehåll. Attributen som väljs som **matchande** egenskaper används för att matcha grupper med innehåll som kan användas för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    |Attribut|Typ|Stöds för filtrering|
    |---|---|---|
    |displayName|Sträng|&check;|
    |medlemmar|Referens|

12. Om du vill ställa in omfångs filter slutför du stegen som beskrivs i [själv studie kursen för omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för innehåll kan du i avsnittet **Inställningar** för **etablerings status** välja **på**.

    ![Skärm bild som visar etablerings statusen på och av växlingen.](common/provisioning-toggle-on.png)

14. Om du vill definiera de användare eller grupper som du vill etablera till innehåll går du till avsnittet **Inställningar** , för **omfång**, och väljer önskat alternativ.

    ![Skärm bild som visar alternativ som du kan välja i fönstret omfång.](common/provisioning-scope.png)

15. När du är redo att etablera väljer du **Spara**.

    ![Skärm bild som visar knappen Spara och knappen Avbryt.](common/provisioning-configuration-save.png)

Den här åtgärden startar den inledande synkroniseringen av alla användare och grupper som definierats i **omfånget** under **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="monitor-your-deployment"></a>Övervaka distributionen

När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Visa [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md)för att avgöra vilka användare som har etablerats eller har misslyckats.
* Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)för att se status för etablerings cykeln och hur nära den är att slutföra.
* Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, placeras programmet i karantän. Läs mer om [karantäns tillstånd](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
