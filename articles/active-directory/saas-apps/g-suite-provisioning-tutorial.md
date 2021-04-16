---
title: 'Självstudie: Konfigurera G Suite för automatisk användareablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du etablerar och avetabler användarkonton automatiskt från Azure AD till G Suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: Zhchia
ms.openlocfilehash: b8513f62b6f181a1490d136062c5de81db847ba7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533397"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Självstudie: Konfigurera G Suite för automatisk användareablering

Den här självstudien beskriver de steg du behöver utföra i både G Suite och Azure Active Directory (Azure AD) för att konfigurera automatisk användareablering. När Azure AD konfigureras etablerar och avetabler automatiskt användare och grupper till [G Suite](https://gsuite.google.com/) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> I den här självstudien beskrivs en anslutningsapp som bygger på Azure AD-tjänsten för användareablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i G Suite
> * Ta bort användare i G Suite när de inte längre behöver åtkomst
> * Håll användarattribut synkroniserade mellan Azure AD och G Suite
> * Etablera grupper och gruppmedlemskap i G Suite
> * [Enkel inloggning till](./google-apps-tutorial.md) G Suite (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Scenariot som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* [En G Suite-klientorganisation](https://gsuite.google.com/pricing.html)
* Ett användarkonto på en G Suite med administratörsbehörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Fastställ vilka data som [ska mappa mellan Azure AD och G Suite](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera G Suite för att stödja etablering med Azure AD

Innan du konfigurerar G Suite för automatisk användareablering med Azure AD måste du aktivera SCIM-etablering på G Suite.

1. Logga in på [G Suite-administratörskonsolen](https://admin.google.com/) med ditt administratörskonto och välj sedan **Säkerhet.** Om du inte ser länken kan den vara dold under menyn **Fler** kontroller längst ned på skärmen.

    ![G Suite-säkerhet](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. På sidan **Säkerhet** väljer du **API-referens.**

    ![G Suite API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Välj **Aktivera API-åtkomst.**

    ![G Suite API aktiverat](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > För varje användare som du tänker etablera till G Suite måste deras användarnamn i Azure AD **vara** kopplat till en anpassad domän. Användarnamn som ser ut som bob@contoso.onmicrosoft.com godkänns till exempel inte av G Suite. Å andra sidan bob@contoso.com accepteras. Du kan ändra en befintlig användares domän genom att följa instruktionerna [här.](../fundamentals/add-custom-domain.md)

4. När du har lagt till och verifierat dina önskade anpassade domäner med Azure AD måste du verifiera dem igen med G Suite. Se följande steg för att verifiera domäner i G Suite:

    a. I [G Suite-administratörskonsolen](https://admin.google.com/)väljer du **Domäner.**

    ![G Suite-domäner](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Välj **Lägg till en domän eller ett domänalias.**

    ![G Suite Lägg till domän](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Välj **Lägg till en annan** domän och skriv sedan namnet på den domän som du vill lägga till.

    ![G Suite Lägg till en till](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Välj **Fortsätt och verifiera domänägarskapet.** Följ sedan stegen för att kontrollera att du äger domännamnet. Omfattande anvisningar om hur du verifierar din domän med Google finns i [Verifiera ditt webbplatsägarskap.](https://support.google.com/webmasters/answer/35179)

    e. Upprepa föregående steg för eventuella ytterligare domäner som du tänker lägga till i G Suite.

5. Fastställ sedan vilket administratörskonto du vill använda för att hantera användareablering i G Suite. Gå till **Administratörsroller.**

    ![G Suite-administratör](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. För **administratörsrollen** för det kontot redigerar du **behörigheterna** för den rollen. Se till att aktivera alla **administratörs-API-behörigheter** så att det här kontot kan användas för etablering.

    ![Administratörsbehörighet för G Suite](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till G Suite från Azure AD-programgalleriet

Lägg till G Suite från Azure AD-programgalleriet för att börja hantera etablering till G Suite. Om du tidigare har ställt in G Suite för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till G Suite måste du välja en annan roll än **Standardåtkomst.** Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Steg 5. Konfigurera automatisk användareablering till G Suite 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!NOTE]
> Mer information om G Suites slutpunkt för katalog-API finns i [Katalog-API.](https://developers.google.com/admin-sdk/directory)

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Så här konfigurerar du automatisk användareablering för G Suite i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**. Användarna måste logga in på portal.azure.com och kommer inte att kunna använda aad.portal.azure.com

    ![Bladet Företagsprogram](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Bladet Alla program](./media/g-suite-provisioning-tutorial/all-applications.png)

2. I programlistan väljer du **G Suite**.

    ![G Suite-länken i programlistan](common/all-applications.png)

3. Välj **fliken Etablering.** Klicka på **Kom igång.**

    ![Skärmbild av hanteringsalternativen med alternativet Etablering utvalt.](common/provisioning.png)

      ![Kom igång-bladet](./media/g-suite-provisioning-tutorial/get-started.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärmbild av listrutan Etableringsläge med alternativet Automatiskt.](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** klickar du på **Auktorisera**. Du omdirigeras till en dialogruta för Google-auktorisering i ett nytt webbläsarfönster.

      ![G Suite-auktorisera](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Bekräfta att du vill ge Azure AD behörighet att göra ändringar i din G Suite-klientorganisation. Välj **Acceptera**.

     ![Autentisering av G Suite-klientorganisation](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. I dialogrutan Azure Portal du på **Testa anslutning för att** se till att Azure AD kan ansluta till G Suite. Om anslutningen misslyckas ser du till att ditt G Suite-konto har administratörsbehörighet och försöker igen. Försök sedan **auktorisera** steget igen.

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Etablera Azure Active Directory Användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till G Suite i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha användarkontona i G Suite för uppdateringsåtgärder. Om du väljer att ändra det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att G Suite-API:et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att genomföra ändringarna.

   |Attribut|Typ|
   |---|---|
   |primaryEmail|Sträng|
   |Förbindelser. [type eq "manager"].value|Sträng|
   |name.familyName|Sträng|
   |name.givenName|Sträng|
   |pausas|Sträng|
   |externalIds. [type eq "custom"].value|Sträng|
   |externalIds. [type eq "organization"].value|Sträng|
   |Adresser. [type eq "work"].country|Sträng|
   |Adresser. [type eq "work"].streetAddress|Sträng|
   |Adresser. [type eq "work"].region|Sträng|
   |Adresser. [type eq "work"].locality|Sträng|
   |Adresser. [type eq "work"].postalCode|Sträng|
   |Email. [type eq "work"].address|Sträng|
   |Organisationer. [type eq "work"].department|Sträng|
   |Organisationer. [type eq "work"].title|Sträng|
   |phoneNumbers. [type eq "work"].value|Sträng|
   |phoneNumbers. [type eq "mobile"].value|Sträng|
   |phoneNumbers. [type eq "work_fax"].value|Sträng|
   |Email. [type eq "work"].address|Sträng|
   |Organisationer. [type eq "work"].department|Sträng|
   |Organisationer. [type eq "work"].title|Sträng|
   |phoneNumbers. [type eq "work"].value|Sträng|
   |phoneNumbers. [type eq "mobile"].value|Sträng|
   |phoneNumbers. [type eq "work_fax"].value|Sträng|
   |Adresser. [type eq "home"].country|Sträng|
   |Adresser. [type eq "home"].formatted|Sträng|
   |Adresser. [type eq "home"].locality|Sträng|
   |Adresser. [type eq "home"].postalCode|Sträng|
   |Adresser. [type eq "home"].region|Sträng|
   |Adresser. [type eq "home"].streetAddress|Sträng|
   |Adresser. [type eq "other"].country|Sträng|
   |Adresser. [type eq "other"].formatted|Sträng|
   |Adresser. [type eq "other"].locality|Sträng|
   |Adresser. [type eq "other"].postalCode|Sträng|
   |Adresser. [type eq "other"].region|Sträng|
   |Adresser. [type eq "other"].streetAddress|Sträng|
   |Adresser. [type eq "work"].formatted|Sträng|
   |changePasswordAtNextLogin|Sträng|
   |Email. [type eq "home"].address|Sträng|
   |Email. [type eq "other"].address|Sträng|
   |externalIds. [type eq "account"].value|Sträng|
   |externalIds. [type eq "custom"].customType|Sträng|
   |externalIds. [type eq "customer"].value|Sträng|
   |externalIds. [type eq "login_id"].value|Sträng|
   |externalIds. [type eq "network"].value|Sträng|
   |gender.type|Sträng|
   |GeneratedImmutableId|Sträng|
   |Identifierare|Sträng|
   |Ims. [type eq "home"].protocol|Sträng|
   |Ims. [type eq "other"].protocol|Sträng|
   |Ims. [type eq "work"].protocol|Sträng|
   |includeInGlobalAddressList|Sträng|
   |ipWhitelisted|Sträng|
   |Organisationer. [type eq "school"].costCenter|Sträng|
   |Organisationer. [type eq "school"].department|Sträng|
   |Organisationer. [type eq "school"].domain|Sträng|
   |Organisationer. [type eq "school"].fullTimeEquivalent|Sträng|
   |Organisationer. [type eq "school"].location|Sträng|
   |Organisationer. [type eq "school"].name|Sträng|
   |Organisationer. [type eq "school"].symbol|Sträng|
   |Organisationer. [type eq "school"].title|Sträng|
   |Organisationer. [type eq "work"].costCenter|Sträng|
   |Organisationer. [type eq "work"].domain|Sträng|
   |Organisationer. [type eq "work"].fullTimeEquivalent|Sträng|
   |Organisationer. [type eq "work"].location|Sträng|
   |Organisationer. [type eq "work"].name|Sträng|
   |Organisationer. [type eq "work"].symbol|Sträng|
   |OrgUnitPath|Sträng|
   |phoneNumbers. [type eq "home"].value|Sträng|
   |phoneNumbers. [type eq "other"].value|Sträng|
   |Webbplatser. [type eq "home"].value|Sträng|
   |Webbplatser. [type eq "other"].value|Sträng|
   |Webbplatser. [type eq "work"].value|Sträng|
   

10. Under avsnittet **Mappningar** väljer du **Etablera Azure Active Directory Grupper.**

11. Granska de gruppattribut som synkroniseras från Azure AD till G Suite i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha grupperna i G Suite för uppdateringsåtgärder. Välj knappen **Spara** för att genomföra ändringarna.

      |Attribut|Typ|
      |---|---|
      |e-post|Sträng|
      |Medlemmar|Sträng|
      |name|Sträng|
      |beskrivning|Sträng|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för G Suite ändrar du **Etableringsstatus** **till På** i **avsnittet** Inställningar.

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till G Suite genom att välja önskade värden i **Omfång** i **avsnittet** Inställningar.

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs.

> [!NOTE]
> Om användarna redan har ett befintligt personligt/konsumentkonto som använder Azure AD-användarens e-postadress kan det orsaka ett problem som kan lösas med hjälp av Google Transfer Tool innan katalogsynkronisering.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Ändringslogg

* 2020-10-17 – Stöd har lagts till för ytterligare användar- och gruppattribut för G Suite.
* 2020-10-17 – Uppdaterade G Suite-målattributnamn för att matcha det som definieras [här.](https://developers.google.com/admin-sdk/directory)
* 2020-10-17 – Standardattributmappningar har uppdaterats.
* 2021-03-18 – Chefs-e-post synkroniseras nu i stället för ID för alla nya användare. För befintliga användare som har etablerats med en chef som ett ID kan du göra en omstart via [Microsoft Graph](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http&preserve-view=true) med omfånget "fullständigt" för att säkerställa att e-postmeddelandet har etablerats. Den här ändringen påverkar endast GSuite-etableringsjobbet och inte det äldre etableringsjobbet som börjar med Goov2OutDelta. Observera att chefens e-postadress etableras när användaren först skapas eller när chefen ändras. Chefens e-postadress etableras inte om chefen ändrar sin e-postadress. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
