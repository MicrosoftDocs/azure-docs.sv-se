---
title: 'Självstudie: Konfigurera 8x8 för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till 8x8.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: 7aaa87691f1ebfa79cd58dae56bf9bd1b979131b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538611"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Självstudie: Konfigurera 8x8 för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både 8x8 Configuration Manager och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [8x8](https://www.8x8.com) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i 8x8
> * Ta bort användare i 8x8 när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och 8x8
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) till 8x8 (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör).
* En prenumeration på 8x8 X-serien på alla nivåer.
* Ett 8x8-användarkonto med administratörs behörighet i [Configuration Manager](https://vo-cm.8x8.com).
* [Enkel inloggning med Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) har redan kon figurer ATS.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och 8x8](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera 8x8 för att ge stöd för etablering med Azure AD

Det här avsnittet vägleder dig genom stegen för att konfigurera 8x8 för att ge stöd för etablering med Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>Så här konfigurerar du en åtkomsttoken för användar etablering i 8x8 Configuration Manager:

1. Logga in på [Configuration Manager](https://vo-cm.8x8.com). Välj **identitets hantering**.

   ![Identitets hantering](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Klicka på länken **Visa information om användar etablering** för att generera en token.

   ![Visa användar etablering](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Kopiera **8X8 URL** -och **8X8 API-token** -värden. Dessa värden anges i fälten klient- **URL** och **hemlig token** på fliken etablering i 8x8-programmet i Azure Portal.

   ![Kopiera URL och token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till 8x8 från Azure AD-programgalleriet

Lägg till 8x8 från Azure AD-programgalleriet för att börja hantera etablering till 8x8. Om du tidigare har konfigurerat 8x8 för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Detta är det enklare alternativet och används av de flesta personer.

Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till 8x8 måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Steg 5. Konfigurera automatisk användar etablering till 8x8 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i 8x8 baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Konfigurera automatisk användar etablering för 8x8 i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Bladet Alla program](./media/8x8-provisioning-tutorial/all-applications.png)

2. I listan program väljer du **8x8**.

    ![8x8-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** . Klicka på **Kom igång**.

    ![Fliken etablering](common/provisioning.png)

   ![Kom igång-blad](./media/8x8-provisioning-tutorial/get-started.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , kopierar du **8x8-URL: en** från Configuration Manager till **klient-URL: en**. Kopiera **8x8-API-token** från Configuration Manager till **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till 8x8. Om anslutningen Miss lyckas kontrollerar du att 8x8-kontot har administratörs behörighet och försöker igen.

    ![Etablering](./media/8x8-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till 8x8 i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i 8x8 för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att 8x8-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Kommentarer|
   |---|---|---|
   |userName|Sträng|Anger både användar namn och Federations-ID|
   |externalId|Sträng||
   |aktiv|Boolesk||
   |rubrik|Sträng||
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng||
   |Name. givenName|Sträng||
   |Name. familyName|Sträng||
   |phoneNumbers [Type EQ "Mobile"]. värde|Sträng|Personlig kontakt nummer|
   |phoneNumbers [typ EQ "Work"]. värde|Sträng|Personlig kontakt nummer|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng||
   |urn: IETF: params: scim: schemas: tillägg: 8x8:1.1: användare: webbplats|Sträng|Kan inte uppdateras efter att användaren har skapats|
   |locale|Sträng|Inte mappad som standard|
   |informationen|Sträng|Inte mappad som standard|

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för 8x8 ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till 8x8 genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats.
2. Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras.
3. Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
