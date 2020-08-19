---
title: 'Självstudie: Konfigurera MediusFlow för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: e8a64b3f42e52363f70d536963594bb6083a0992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548080"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Självstudie: Konfigurera MediusFlow för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både MediusFlow och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [MediusFlow](https://www.mediusflow.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i MediusFlow
> * Ta bort användare i MediusFlow när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och MediusFlow
> * Etablera grupper och grupp medlemskap i MediusFlow
> * Enkel inloggning till MediusFlow (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En aktiv MediusFlow-prenumeration med en kvalitets säkrings-eller produktions klient.
* Ett användar konto i MediusFlow med administratörs behörighet för att kunna utföra konfigurationen i MediusFlow.
* Företag som lagts till i MediusFlow-klienten där användarna ska tillhandahållas.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och MediusFlow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera MediusFlow för att ge stöd för etablering med Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Aktivera Microsoft 365-appen i MediusFlow
Börja med att aktivera åtkomst till Azure AD-inloggningen och Azure AD-konfigurationsverktyget i MediusFlow genom att utföra följande steg:

#### <a name="user-login"></a>Användar inloggning
Om du vill aktivera inloggnings flödet till Microsoft 365/Azure AD hänvisar du till [den] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) artikeln.

#### <a name="user-transfer-configuration"></a>Konfiguration av användar överföring
Om du vill aktivera konfigurations portalen för användare för etablering från Azure AD kan du läsa [den här](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) artikeln.

#### <a name="configure-user-provisioning"></a>Konfigurera användarförsörjning

1.  Logga in på [MediusFlow-administratörskonsolen](https://office365.cloudapp.mediusflow.com/) genom att ange klient-ID: t.

    ![Autentisera](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. Kontrol lera anslutningen till MediusFlow.

    ![Verifiera](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Ange Azure AD-klient-ID.

    ![Ange klient-ID](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Du kan läsa mer i [vanliga frågor och svar](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) om hur du hittar det.

4. Spara konfigurationen.

    ![Autentisera](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. Välj användar etablering och klicka på **OK**.

    ![Autentisera](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. Klicka på **generera hemlig nyckel**. Kopiera och spara det här värdet. Det här värdet anges i fältet **hemlig token** på fliken **etablering** i ditt MediusFLow-program i Azure Portal.

    ![Autentisera](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. Klicka på **OK**.

    ![Autentisera](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. För att användarna ska kunna importeras med en fördefinierad uppsättning roller, företag och andra allmänna konfigurationer i MediusFlow måste du konfigurera det först. Börja med att lägga till konfigurationen genom att klicka på **Lägg till ny konfiguration**.

    ![Konfigurera användare](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. Ange standard inställningarna för användarna. I den här vyn är det möjligt att ange standardattributet. Om standardinställningarna är OK räcker det att bara ge ett giltigt företags namn. Eftersom dessa konfigurations inställningar hämtas från MediusFlow måste de konfigureras först. Mer information finns i avsnittet **krav** i den här artikeln.

    ![Konfigurera användare](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. Spara användar konfigurationen genom att klicka på **Spara** .

    ![Konfigurera användare](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. Hämta länken för användar etablering genom att klicka på **Kopiera scim länk**. Kopiera och spara det här värdet. Det här värdet anges i fältet **klient-URL** på fliken **etablering** i MediusFLow-programmet i Azure Portal.
 
    ![Konfigurera användare](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till MediusFlow från Azure AD-programgalleriet

Lägg till MediusFlow från Azure AD-programgalleriet för att börja hantera etablering till MediusFlow. Om du tidigare har konfigurerat MediusFlow för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till MediusFlow måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du kontrol lera det genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Steg 5. Konfigurera automatisk användar etablering till MediusFlow 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Konfigurera automatisk användar etablering för MediusFlow i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **MediusFlow**.

    ![MediusFlow-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du mata in klient webb adressen som hämtades tidigare i **klient-URL: en**. Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till MediusFlow. Om anslutningen Miss lyckas kontrollerar du att MediusFlow-kontot har administratörs behörighet och försöker igen.

      ![etablerings](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till MediusFlow**.

9. Granska de användarattribut som synkroniseras från Azure AD till MediusFlow i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Mediusflow för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att MediusFlow-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|
   |Name. displayName|Sträng|
   |aktiv|Boolesk|
   |Name. givenName|Sträng|
   |Name. familyName|Sträng|
   |namn. formaterad|Sträng|
   |externalID|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|


10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till MediusFlow**.

11. Granska gruppattributen som synkroniseras från Azure AD till MediusFlow i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Mediusflow för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externalID|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för MediusFlow ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till MediusFlow genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
2. Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
3. Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
