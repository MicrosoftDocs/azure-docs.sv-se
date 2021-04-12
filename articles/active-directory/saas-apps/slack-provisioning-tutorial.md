---
title: 'Självstudier: Användarförsörjning för Slack – Azure AD'
description: Lär dig att konfigurera Azure Active Directory till att automatiskt etablera och avetablera användarkonton till Slack.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 92c2ae13b840d7a73d86365ce88584bcafc878e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181466"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Slack för automatisk användarförsörjning

Syftet med den här självstudien är att visa vilka steg du behöver utföra i Slack och Azure AD för att automatiskt etablera och avetablera användarkonton från Azure AD till Slack. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Slack
> * Ta bort användare i Slack när de inte behöver någon åtkomst längre
> * Behålla användarattribut som är synkroniserade mellan Azure AD och Slack
> * Etablera grupper och gruppmedlemskap i Slack
> * [Enkel inloggning](./slack-tutorial.md) till Slack (rekommenderas)


## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i självstudien förutsätter att du redan har följande objekt:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör).
* En Slack-klientorganisation med [plusplan](https://aadsyncfabric.slack.com/pricing) eller bättre aktiverat.
* Ett användarkonto i Slack med teamadministratörsbehörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Slack](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Steg 2. Lägg till Slack från Azure AD-programgalleriet

Lägg till Slack från Azure AD-programgalleriet och börja hantera etablering till Slack. Om du tidigare har konfigurerat Slack för enkel inloggning, kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Steg 3. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Slack, måste du välja en annan roll än **Standardåtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Steg 4. Konfigurera automatisk användaretablering till Slack 

I det här avsnittet får du hjälp med att ansluta Azure AD till Slack-API:et för användarkontoetablering och konfigurera etableringstjänsten för att skapa, uppdatera och inaktivera tilldelade användarkonton i Slack, baserat på användar- och grupptilldelning i Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurera automatisk användarkontoetablering till Slack i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Slack** i programlistan.

    ![Slack-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **Autentiseringsuppgifter för administratör** klickar du på **Auktorisera**. En dialogruta för Slack-auktorisering öppnas i ett nytt webbläsarfönster.

    ![Skärmbild som visar knappen Auktorisera autentiseringsuppgifter för administratör.](media/slack-provisioning-tutorial/authorization.png)


6. I det nya fönstret loggar du in på Slack med ditt teamadministratörskonto. i dialogrutan för auktorisering väljer du det Slack-team som du vill aktivera etablering för. Välj sedan **Auktorisera**. När du är klar går du tillbaka till Azure-portalen för att slutföra etableringskonfigurationen.

    ![Dialogrutan Auktorisering](./media/slack-provisioning-tutorial/slackauthorize.png)

7. I Azure-portalen klickar du på **Testa anslutning** för att se att Azure AD kan ansluta till din Slack-app. Om anslutningen misslyckas kontrollerar du att ditt Slack-konto har teamadministratörsbehörighet och försöker sedan igen.

8. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

9. Välj **Spara**.

10. Under avsnittet Mappningar väljer du **Synkronisera Azure Active Directory-användare till Slack**.

11. I avsnittet **Attributmappningar** granskar du de användarattribut som ska synkroniseras från Azure AD till Slack. Observera att de attribut som väljs som **Matchande** egenskaper kommer att användas för att matcha användarkontona i Slack vid uppdateringsåtgärder. Välj knappen Spara för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |aktiv|Boolesk|
   |externalId|Sträng|
   |displayName|Sträng|
   |name.familyName|Sträng|
   |name.givenName|Sträng|
   |title|Sträng|
   |emails[type eq "work"].value|Sträng|
   |userName|Sträng|
   |nickName|Sträng|
   |addresses[type eq "untyped"].streetAddress|Sträng|
   |addresses[type eq "untyped"].locality|Sträng|
   |addresses[type eq "untyped"].region|Sträng|
   |addresses[type eq "untyped"].postalCode|Sträng|
   |addresses[type eq "untyped"].country|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |roles[primary eq "True"].value|Sträng|
   |locale|Sträng|
   |name.honorificPrefix|Sträng|
   |photos[type eq "photo"].value|Sträng|
   |profileUrl|Sträng|
   |timezone|Sträng|
   |userType|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.department|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Referens|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Sträng|
   |urn:scim:schemas:extension:enterprise:1.0.division|Sträng|

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper till Slack**.

13. I avsnittet **Attributmappningar** granskar du de gruppattribut som kommer att synkroniseras från Azure AD till Slack. Observera att de attribut som väljs som **Matchande** egenskaper kommer att användas för att matcha grupperna i Slack vid uppdateringsåtgärder. Välj knappen Spara för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |medlemmar|Referens|

14. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Slack, ändrar du **Etableringsstatus** till **På** i avsnittet **Inställningar**

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till Slack genom att välja önskade värden i **Omfång** i avsnittet **Inställningar**.

    ![Etableringsomfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-5-monitor-your-deployment"></a>Steg 5. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Felsökningstips

* När du konfigurerar Slack-attributet **displayName** bör du vara medveten om följande beteenden:

  * Värdena är inte helt unika (t.ex. kan två användare ha samma visningsnamn)

  * Har stöd för icke-engelska tecken, blanksteg och skiftläge. 
  
  * Tillåtna skiljetecken inkluderar punkter, understreck, bindestreck, apostrofer, hakparenteser (t.ex. **( [ { } ] )** ) och avgränsare (t.ex. **, / ;** ).
  
  * displayName-egenskapen får inte innehålla något ”@”-tecken. Om ett ”@” ingår kan det finnas en överhoppad händelse i etableringsloggarna med beskrivningen ”AttributeValidationFailed”.

  * Uppdateringar görs endast om de här två inställningarna har konfigurerats i Slacks arbetsplats/organisation – **Profilsynkronisering har aktiverats** och **Användarna inte kan ändra sina visningsnamn**.

* Slacks **userName**-attribut måste kortare än 21 tecken och ha ett unikt värde.

* Slack tillåter endast matchning med attributen **userName** och **email**.  
  
* Vanliga felkoder finns dokumenterade i den officiella Slack-dokumentationen – https://api.slack.com/scim#errors

## <a name="change-log"></a>Ändringslogg

* 06/16/2020 – Attributet DisplayName har ändrats till att endast uppdateras när en ny användare skapas.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)