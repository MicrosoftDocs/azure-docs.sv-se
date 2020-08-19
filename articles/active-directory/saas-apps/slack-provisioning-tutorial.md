---
title: 'Självstudie: användar etablering för slack – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till slack.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 368d75ecffda49f688a7a5ce11b60693650014c6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527833"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Självstudie: Konfigurera slack för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i slack och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till slack. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i slack
> * Ta bort användare i slack när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och slack
> * Etablera grupper och grupp medlemskap i slack
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) till slack (rekommenderas)


## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör).
* En slack-klient med [plus](https://aadsyncfabric.slack.com/pricing) -eller bättre aktiverat.
* Ett användar konto i slack med team administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och slack](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Steg 2. Lägg till slack från Azure AD Application Gallery

Lägg till slack från Azure AD-programgalleriet för att börja hantera etablering till slack. Om du tidigare har konfigurerat slack för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Steg 3. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till slacket måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Steg 4. Konfigurera automatisk användar etablering till slack 

I det här avsnittet får du hjälp med att ansluta Azure AD till slack-API: et för användar konto etablering och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i slack baserat på användar-och grupp tilldelning i Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurera automatisk användar konto etablering till slack i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Slack** i programlistan.

    ![Slack-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** klickar du på **auktorisera**. Då öppnas en dialog ruta för slack-auktorisering i ett nytt webbläsarfönster.

    ![Auktorisering](media/slack-provisioning-tutorial/authorization.png)


6. I det nya fönstret loggar du in på slack med ditt team administratörs konto. i dialog rutan resulterande auktorisering väljer du det slack-team som du vill aktivera etablering för och väljer sedan **auktorisera**. När du är klar återgår du till Azure Portal för att slutföra etablerings konfigurationen.

    ![Dialog rutan auktorisering](./media/slack-provisioning-tutorial/slackauthorize.png)

7. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din slack-app. Om anslutningen Miss lyckas kontrollerar du att ditt slack-konto har team administratörs behörighet och försöker sedan igen.

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Välj **Spara**.

10. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till slack**.

11. I avsnittet **mappningar för attribut** granskar du de användarattribut som kommer att synkroniseras från Azure AD till slack. Observera att attributen som har valts som **matchande** egenskaper kommer att användas för att matcha användar kontona i slack för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |aktiv|Boolesk|
   |externalId|Sträng|
   |displayName|Sträng|
   |Name. familyName|Sträng|
   |Name. givenName|Sträng|
   |rubrik|Sträng|
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|
   |userName|Sträng|
   |Smek namn|Sträng|
   |adresser [Type EQ "har inte angets]. streetAddress|Sträng|
   |adresser [Type EQ "har inte angets]. plats|Sträng|
   |adresser [Type EQ "har inte angets]. region|Sträng|
   |adresser [Type EQ "untyped]. Postnr|Sträng|
   |adresser [Type EQ "har inte angets]. land|Sträng|
   |phoneNumbers [Type EQ "Mobile"]. värde|Sträng|
   |phoneNumbers [typ EQ "Work"]. värde|Sträng|
   |roller [Primary EQ "true"]. värde|Sträng|
   |locale|Sträng|
   |Name. honorificPrefix|Sträng|
   |Foton [Type EQ "Foto"]. värde|Sträng|
   |profileUrl|Sträng|
   |informationen|Sträng|
   |userType|Sträng|
   |urn: scim: schemas: tillägg: Enterprise: 1.0. Department|Sträng|
   |urn: scim: schemas: tillägg: Enterprise: 1.0. Manager|Referens|
   |urn: scim: schemas: tillägg: Enterprise: 1.0. employeeNumber|Sträng|
   |urn: scim: schemas: tillägg: Enterprise: 1.0. costCenter|Sträng|
   |urn: scim: schemas: tillägg: Enterprise: 1.0. Organization|Sträng|
   |urn: scim: schemas: tillägg: Enterprise: 1.0. Division|Sträng|

12. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till slack**.

13. I avsnittet **mappningar för attribut** granskar du de Gruppattribut som ska synkroniseras från Azure AD till slack. Observera att attributen som har valts som **matchande** egenskaper kommer att användas för att matcha grupperna i slack för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |medlemmar|Referens|

14. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för slack ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till slack genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-5-monitor-your-deployment"></a>Steg 5. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
2. Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
3. Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Felsökningstips

* När du konfigurerar Slackets **DisplayName** -attribut bör du vara medveten om följande beteenden:

  * Värdena är inte helt unika (t. ex. 2 användare kan ha samma visnings namn)

  * Har stöd för icke-engelska tecken, blank steg och Skift läge. 
  
  * Tillåten interpunktion innehåller punkter, under streck, bindestreck, apostrofer, hakparenteser (t. ex. **([{}])**) och avgränsare (t. **ex.,/;**).
  
  * displayName-egenskapen får inte ha ett @-Character. Om en @ ingår kan du hitta en överhoppad händelse i etablerings loggarna med beskrivningen "AttributeValidationFailed".

  * Endast uppdateringar om de här två inställningarna har kon figurer ATS i slack s arbets plats/organisation – **profil synkronisering är aktiverat** och **användarna inte kan ändra sina visnings namn**.

* Slackets **username** -attribut måste vara under 21 tecken och ha ett unikt värde.

* Slack tillåter endast matchning med attributen **användar namn** och **e-post**.  
  
* Vanliga erorr-koder finns dokumenterade i dokumentation om officiella slack – https://api.slack.com/scim#errors

## <a name="change-log"></a>Ändringslogg

* 06/16/2020 – attributet DisplayName har ändrats till att endast uppdateras när en ny användare skapas.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)