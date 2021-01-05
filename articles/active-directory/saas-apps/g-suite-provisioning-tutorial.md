---
title: 'Självstudie: Konfigurera G Suite för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till G Suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 552322b9452d380dd5507fb579d7cc44e1a456fe
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898907"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Självstudie: Konfigurera G Suite för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både G-serien och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [G Suite](https://gsuite.google.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> G Suite Connector uppdaterades nyligen den 2019 oktober. Ändringar som görs i G Suite Connector inkluderar:
>
> * Stöd har lagts till för ytterligare användar-och Gruppattribut i G Suite.
> * Uppdaterade G Suite-målattribut för att matcha vad som definieras [här](https://developers.google.com/admin-sdk/directory).
> * Uppdaterade mappningar av standardattribut.

> [!NOTE]
> Den här artikeln innehåller referenser till termen *vitlista*, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i G Suite
> * Ta bort användare i G Suite när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och G Suite
> * Etablera grupper och grupp medlemskap i G Suite
> * [Enkel inloggning](./google-apps-tutorial.md) till G Suite (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* [En G Suite-klient](https://gsuite.google.com/pricing.html)
* Ett användar konto i en G-Svit med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och G Suite](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera G Suite för att stödja etablering med Azure AD

Innan du konfigurerar G Suite för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på G Suite.

1. Logga in på administratörs [konsolen för G Suite](https://admin.google.com/) med ditt administratörs konto och välj sedan **säkerhet**. Om du inte ser länken kan den vara dold under menyn **fler kontroller** längst ned på skärmen.

    ![G Suite-säkerhet](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. På sidan **säkerhet** väljer du **API-referens**.

    ![G Suite-API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Välj **Aktivera API-åtkomst**.

    ![G Suite API aktiverat](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > För varje användare som du tänker etablera till G Suite **måste** deras användar namn i Azure AD vara knutet till en anpassad domän. Till exempel godkänns inte användar namn som ser ut som om bob@contoso.onmicrosoft.com de är i G Suite. Å andra sidan bob@contoso.com accepteras. Du kan ändra en befintlig användares domän genom att följa instruktionerna [här](../fundamentals/add-custom-domain.md).

4. När du har lagt till och verifierat önskade anpassade domäner med Azure AD måste du verifiera dem igen med G Suite. Se följande steg för att verifiera domäner i G Suite:

    a. I [Administratörs konsolen för G Suite](https://admin.google.com/)väljer du **domäner**.

    ![G Suite-domäner](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Välj **Lägg till en domän eller ett domän Ali Aset**.

    ![G Suite Lägg till domän](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Välj **Lägg till en annan domän** och skriv sedan namnet på den domän som du vill lägga till.

    ![G Suite Lägg till ett annat](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Välj **Fortsätt och verifiera domän ägarskap**. Följ sedan stegen för att kontrol lera att du äger domän namnet. Omfattande instruktioner för hur du verifierar din domän med Google finns i [Verifiera din webbplats ägarskap](https://support.google.com/webmasters/answer/35179).

    e. Upprepa föregående steg för eventuella ytterligare domäner som du vill lägga till i G Suite.

5. Bestäm sedan vilket administratörs konto som du vill använda för att hantera användar etablering i G Suite. Gå till **Administratörs roller**.

    ![G Suite-administratör](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. Redigera **behörigheterna** för den rollen för **Administratörs rollen** för kontot. Se till att aktivera alla **Administratörs-API-privilegier** så att det här kontot kan användas för etablering.

    ![Administratörs privilegier för G Suite](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till G Suite från Azure AD-programgalleriet

Lägg till G Suite från Azure AD-programgalleriet för att börja hantera etablering till G Suite. Om du tidigare har konfigurerat G Suite för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till G Suite måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Steg 5. Konfigurera automatisk användar etablering till G Suite 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!NOTE]
> Om du vill veta mer om G Suites katalog-API-slutpunkt läser du [katalog-API](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Konfigurera automatisk användar etablering för G Suite i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**. Användarna måste logga in på portal.azure.com och kommer inte att kunna använda aad.portal.azure.com

    ![Bladet Företagsprogram](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Bladet Alla program](./media/g-suite-provisioning-tutorial/all-applications.png)

2. I listan program väljer du **G Suite**.

    ![G Suite-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** . Klicka på **Kom igång**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

      ![Kom igång-blad](./media/g-suite-provisioning-tutorial/get-started.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** klickar du på **auktorisera**. Du omdirigeras till en dialog ruta för Google-auktorisering i ett nytt webbläsarfönster.

      ![G Suite-auktorisering](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Bekräfta att du vill ge Azure AD-behörighet för att göra ändringar i G Suite-klienten. Välj **Acceptera**.

     ![G Suite-klientens auth](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till G Suite. Om anslutningen Miss lyckas kontrollerar du att ditt G Suite-konto har administratörs behörighet och försöker igen. Försök sedan att **godkänna** steget igen.

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till G Suite i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i G Suite för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att G Suite API stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |primaryEmail|Sträng|
   |relationer. [Type EQ "Manager"]. värde|Sträng|
   |name.familyName|Sträng|
   |name.givenName|Sträng|
   |avstängd|Sträng|
   |externalIds. [Type EQ anpassad]. värde|Sträng|
   |externalIds. [typ EQ "organisation"]. värde|Sträng|
   |adresser. [Skriv EQ "Work"]. land|Sträng|
   |adresser. [Skriv EQ "Work"]. streetAddress|Sträng|
   |adresser. [Skriv EQ "Work"]. region|Sträng|
   |adresser. [Skriv EQ "Work"]. plats|Sträng|
   |adresser. [Skriv EQ "Work"]. Postnr|Sträng|
   |e-postmeddelanden. [Skriv EQ "Work"]. address|Sträng|
   |företag. [Skriv EQ "Work"]. avdelning|Sträng|
   |företag. [Skriv EQ "Work"]. title|Sträng|
   |phoneNumbers. [Skriv EQ "Work"]. värde|Sträng|
   |phoneNumbers. [typ EQ "mobil"]. värde|Sträng|
   |phoneNumbers. [Type EQ "work_fax"]. värde|Sträng|
   |e-postmeddelanden. [Skriv EQ "Work"]. address|Sträng|
   |företag. [Skriv EQ "Work"]. avdelning|Sträng|
   |företag. [Skriv EQ "Work"]. title|Sträng|
   |phoneNumbers. [Skriv EQ "Work"]. värde|Sträng|
   |phoneNumbers. [typ EQ "mobil"]. värde|Sträng|
   |phoneNumbers. [Type EQ "work_fax"]. värde|Sträng|
   |adresser. [Skriv EQ "hem"]. land|Sträng|
   |adresser. [Skriv EQ "Start"]. formaterad|Sträng|
   |adresser. [Skriv EQ "hem"]. plats|Sträng|
   |adresser. [Skriv EQ "Home"]. Postnr|Sträng|
   |adresser. [Skriv EQ "hem"]. region|Sträng|
   |adresser. [Skriv EQ "hem"]. streetAddress|Sträng|
   |adresser. [Type EQ "Övrigt"]. land|Sträng|
   |adresser. [Type EQ "Övrigt"]. formaterad|Sträng|
   |adresser. [Type EQ "Övrigt"]. plats|Sträng|
   |adresser. [Type EQ "other"]. Postnr|Sträng|
   |adresser. [Type EQ "Övrigt"]. region|Sträng|
   |adresser. [Type EQ "other"]. streetAddress|Sträng|
   |adresser. [Skriv EQ "Work"]. formaterad|Sträng|
   |changePasswordAtNextLogin|Sträng|
   |e-postmeddelanden. [Skriv EQ "hem"]. adress|Sträng|
   |e-postmeddelanden. [Type EQ "other"]. address|Sträng|
   |externalIds. [Type EQ "konto"]. värde|Sträng|
   |externalIds. [Type EQ "Custom"]. customType|Sträng|
   |externalIds. [typ EQ "kund"]. värde|Sträng|
   |externalIds. [Type EQ "login_id"]. värde|Sträng|
   |externalIds. [Type EQ "nätverk"]. värde|Sträng|
   |kön. typ|Sträng|
   |GeneratedImmutableId|Sträng|
   |Identifierare|Sträng|
   |snabb. [Skriv EQ "hem"]. protokoll|Sträng|
   |snabb. [Type EQ "Övrigt"]. protokoll|Sträng|
   |snabb. [Skriv EQ "Work"]. protokoll|Sträng|
   |includeInGlobalAddressList|Sträng|
   |ipWhitelisted|Sträng|
   |företag. [Type EQ "skola"]. costCenter|Sträng|
   |företag. [Type EQ "skola"]. avdelning|Sträng|
   |företag. [Type EQ "skola"]. domän|Sträng|
   |företag. [Type EQ "skola"]. fullTimeEquivalent|Sträng|
   |företag. [Type EQ "skola"]. plats|Sträng|
   |företag. [Type EQ "skola"]. namn|Sträng|
   |företag. [Type EQ "skola"]. symbol|Sträng|
   |företag. [Type EQ "skola"]. rubrik|Sträng|
   |företag. [Skriv EQ "Work"]. costCenter|Sträng|
   |företag. [Skriv EQ "Work"]. domän|Sträng|
   |företag. [Skriv EQ "Work"]. fullTimeEquivalent|Sträng|
   |företag. [Skriv EQ "Work"]. plats|Sträng|
   |företag. [Skriv EQ "Work"]. namn|Sträng|
   |företag. [Skriv EQ "Work"]. symbol|Sträng|
   |OrgUnitPath|Sträng|
   |phoneNumbers. [Skriv EQ "hem"]. värde|Sträng|
   |phoneNumbers. [Type EQ "Övrigt"]. värde|Sträng|
   |webbplatser. [Skriv EQ "hem"]. värde|Sträng|
   |webbplatser. [Type EQ "Övrigt"]. värde|Sträng|
   |webbplatser. [Skriv EQ "Work"]. värde|Sträng|
   

10. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory grupper**.

11. Granska gruppattributen som synkroniseras från Azure AD till G Suite i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i G Suite för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |e-post|Sträng|
      |Medlemmar|Sträng|
      |name|Sträng|
      |description|Sträng|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för G Suite ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till G Suite genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs.

> [!NOTE]
> Om användarna redan har ett befintligt personligt/konsument konto med hjälp av e-postadressen för Azure AD-användaren kan det orsaka problem som kan lösas med hjälp av Google Transfer-verktyget innan katalogens synkronisering utförs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)