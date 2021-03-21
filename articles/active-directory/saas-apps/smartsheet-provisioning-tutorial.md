---
title: 'Självstudie: Konfigurera Smartsheet för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: e9ee994564e175d3c41cfd5ce415ead8c67df353
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103591"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Smartsheet för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Smartsheet och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till [Smartsheet](https://www.smartsheet.com/pricing). Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Smartsheet
> * Ta bort användare i Smartsheet när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Smartsheet
> * Enkel inloggning till Smartsheet (rekommenderas)

> [!NOTE]
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör).
* [En Smartsheet-klient](https://www.smartsheet.com/pricing).
* Ett användar konto i ett Smartsheet Enterprise-eller Enterprise Premier-plan med system administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Smartsheet](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Smartsheet för att ge stöd för etablering med Azure AD

Innan du konfigurerar Smartsheet för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Smartsheet.

1. Logga in som **sysadmin** i Smartsheet- **[portalen](https://app.smartsheet.com/b/home)** och gå till **konto administratör**.

    ![Smartsheet-konto administratör](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Gå till **säkerhets kontroller > automatisk etablering av användare > redigera**.

    ![Smartsheet säkerhets kontroller](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Lägg till och validera e-postdomänerna för de användare som du planerar att etablera från Azure AD till Smartsheet. Välj **inte aktive rad** för att säkerställa att alla etablerings åtgärder endast kommer från Azure AD, och se till att din Smartsheet användar lista är synkroniserad med Azure AD-tilldelningar.

    ![Smartsheet användar etablering](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. När verifieringen är klar måste du aktivera domänen. 

    ![Smartsheet Aktivera domän](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generera den **hemliga token** som krävs för att konfigurera automatisk användar etablering med Azure AD genom att navigera till **appar och integreringar**.

    ![Skärm bild av administratörs sidan för Smartsheet med användar avataren och apparna & integrations alternativ som kallas för.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Välj **API-åtkomst**. Klicka på **Skapa ny** åtkomsttoken.

    ![Skärm bild av dialog rutan personliga inställningar med API-åtkomst och skapa nya alternativ för åtkomsttoken som kallas för.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definiera namnet på API-åtkomsttoken. Klicka på **OK**.

    ![Skärm bild av steg 1 av 2: generera API-åtkomsttoken med alternativet OK som kallas.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopiera API-åtkomsttoken och spara den eftersom det är den enda gången du kan visa den. Detta krävs i fältet **hemligt token** i Azure AD.

    ![Smartsheet-token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Smartsheet från Azure AD-programgalleriet

Lägg till Smartsheet från Azure AD-programgalleriet för att börja hantera etablering till Smartsheet. Om du tidigare har konfigurerat Smartsheet för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Smartsheet måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* För att säkerställa paritet i användar roll tilldelningar mellan Smartsheet och Azure AD, rekommenderar vi att du använder samma roll tilldelningar som är ifyllda i listan med fullständiga Smartsheet-användare. Om du vill hämta användar listan från Smartsheet navigerar du till **konto administratör > användar hantering > fler åtgärder > Ladda ned användar listan (CSV)**.

* För att få åtkomst till vissa funktioner i appen kräver Smartsheet att en användare har flera roller. Om du vill veta mer om användar typer och behörigheter i Smartsheet går du till [användar typer och behörigheter](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Om en användare har flera roller tilldelade i Smartsheet, **måste** du se till att roll tilldelningarna replikeras i Azure AD för att undvika ett scenario där användare kan förlora åtkomsten till Smartsheet-objekt permanent. Varje unik roll i Smartsheet **måste** tilldelas en annan grupp i Azure AD. Användaren **måste** sedan läggas till i var och en av de grupper som motsvarar de roller som önskas. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Steg 5. Konfigurera automatisk användar etablering till Smartsheet 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Smartsheet baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Konfigurera automatisk användar etablering för Smartsheet i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Smartsheet**.

    ![Smartsheet-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **scim 2,0-bas-URL: en och få åtkomsttoken** som hämtades tidigare från Smartsheet i **klient-URL: en** och den **hemliga token** .. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Smartsheet. Om anslutningen Miss lyckas kontrollerar du att ditt Smartsheet-konto har SysAdmin-behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Smartsheet**.

9. Granska de användarattribut som synkroniseras från Azure AD till Smartsheet i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Smartsheet för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |aktiv|Boolesk|
   |title|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |phoneNumbers [Type EQ "fax"]. värde|Sträng|
   |emails[type eq "work"].value|Sträng|
   |externalId|Sträng|
   |roller|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Sträng|


10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Smartsheet ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Smartsheet genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Kopplings begränsningar

* Smartsheet stöder inte mjuka borttagningar. När en användares **aktiva** attribut är inställt på false, tar Smartsheet bort användaren permanent.

## <a name="change-log"></a>Ändringslogg

* 06/16/2020 – stöd för företags tilläggets attribut "cost center", "Division", "chef" och "Department" för användare har lagts till.
* 02/10/2021 – stöd för Core-attribut "e-post [Type EQ" Work "]" har lagts till för användare.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)