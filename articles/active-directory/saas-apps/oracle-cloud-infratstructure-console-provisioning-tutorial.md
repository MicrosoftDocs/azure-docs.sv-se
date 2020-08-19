---
title: 'Självstudie: Konfigurera Oracle Cloud Infrastructure-konsolen för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till en Oracle Cloud Infrastructure-konsol.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 0c3d68698621fe963074c7216e4636208d93deca
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543881"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Självstudie: Konfigurera en Oracle Cloud Infrastructure-konsol för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Oracle Cloud Infrastructure-konsolen och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [Oracle Cloud Infrastructure-konsolen](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Oracle Cloud Infrastructure-konsolen
> * Ta bort användare i Oracle Cloud Infrastructure-konsolen när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Oracle Cloud Infrastructure Console
> * Etablera grupper och grupp medlemskap i Oracle Cloud Infrastructure-konsolen
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) till Oracle Cloud Infrastructure Console (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör). 
* En Oracle Cloud Infrastructure-kontroll [klient](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* Ett användar konto i Oracle Cloud Infrastructure Control med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Oracle Cloud Infrastructure-konsolen så att den stöder etablering med Azure AD

1. Logga in på administrations portalen för Oracle Cloud Infrastructure-konsolen. I det övre vänstra hörnet på skärmen navigerar du till **Identity > Federation**.

    ![Oracle-administratör](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Klicka på den URL som visas på sidan bredvid Oracle Identity Cloud Service-konsolen.

    ![Oracle-URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Skapa en ny identitetsprovider genom att klicka på **Lägg till identitets leverantör** . Spara IdP-ID: t som ska användas som en del av klient-URL: en. Klicka på plus-ikonen bredvid fliken **program** för att skapa en OAuth-klient och tilldela IDCS identitet domän administratör AppRole.

    ![Ikon för Oracle-moln](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Konfigurera ditt program genom att följa skärm bilderna nedan. När konfigurationen är färdig klickar du på **Spara**.

    ![Oracle-konfiguration](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Utgivnings princip för Oracle-token](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Under fliken konfigurationer i programmet expanderar du alternativet **allmän information** för att hämta klient-ID och klient hemlighet.

    ![Generering av Oracle-token](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Så här skapar du en hemlig token base64 koda klient-ID och klient hemlighet i formatet **klient-ID: klient hemlighet**. Spara den hemliga token. Det här värdet anges i fältet **hemlig token** på fliken etablering i program vara för Oracle Cloud Infrastructure-konsolen i Azure Portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Oracle Cloud Infrastructure-konsolen från Azure AD-programgalleriet

Lägg till Oracle Cloud Infrastructure-konsolen från Azure AD-programgalleriet för att börja hantera etablering till Oracle Cloud Infrastructure Console. Om du tidigare har konfigurerat Oracle Cloud Infrastructure-konsolen för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till en Oracle Cloud Infrastructure-konsol måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Steg 5. Konfigurera automatisk användar etablering till Oracle Cloud Infrastructure Console 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Konfigurera automatisk användar etablering för Oracle Cloud Infrastructure-konsolen i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Oracle Cloud Infrastructure-konsol**.

    ![Länk till Oracle Cloud Infrastructure-konsolen i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , skriver du in **klient webb adressen** i formatet `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Till exempel `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Oracle Cloud Infrastructure-konsolen. Om anslutningen Miss lyckas ser du till att du har administratörs behörighet för ditt konto för Oracle Cloud Infrastructure-konsolen och försöker igen.

    ![etablerings](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Oracle Cloud Infrastructure-konsolen**.

9. Granska de användarattribut som synkroniseras från Azure AD till Oracle Cloud Infrastructure-konsolen i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Oracle Cloud Infrastructure-konsolen för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att Oracle Cloud Infrastructure Console API stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |userName|Sträng|
      |aktiv|Boolesk|
      |rubrik|Sträng|
      |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|
      |preferredLanguage|Sträng|
      |Name. givenName|Sträng|
      |Name. familyName|Sträng|
      |adresser [Type EQ "Work"]. formaterad|Sträng|
      |adresser [Type EQ "Work"]. plats|Sträng|
      |adresser [Type EQ "Work"]. region|Sträng|
      |adresser [Type EQ "Work"]. Postnr|Sträng|
      |adresser [Type EQ "Work"]. land|Sträng|
      |adresser [Type EQ "Work"]. streetAddress|Sträng|
      |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
      |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
      |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
      |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
      |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
      |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: organisation|Sträng|
      |urn: IETF: params: scim: schemas: Oracle: idcs: tillägg: användare: användare: bypassNotification|Boolesk|
      |urn: IETF: params: scim: schemas: Oracle: idcs: tillägg: användare: användare: isFederatedUser|Boolesk|

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Oracle Cloud Infrastructure-konsolen**.

11. Granska gruppattributen som synkroniseras från Azure AD till Oracle Cloud Infrastructure console i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Oracle Cloud Infrastructure-konsolen för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externalId|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Oracle Cloud Infrastructure-konsolen ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Oracle Cloud Infrastructure-konsolen genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
* Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
* Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
