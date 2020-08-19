---
title: 'Självstudie: konfigurera nya Relic per organisation för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till nya Relic efter organisation.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: c7061be06f8a87c2304b678790a2b1eb63ea81f7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554446"
---
# <a name="tutorial-configure-new-relic-by-organization-for-automatic-user-provisioning"></a>Självstudie: konfigurera nya Relic efter organisation för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både nya Relic av organisation och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [nya Relic av organisation](https://newrelic.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i nya Relic efter organisation
> * Ta bort användare i nya Relic efter organisation när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och nya Relic efter organisation
> * Etablera grupper och grupp medlemskap i nya Relic efter organisation
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) till New Relic efter organisation (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör). 
* Ett eller flera konton på nya Relic av organisation som du vill att användarna ska ha åtkomst till. 

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bestäm vilka data som ska [mappas mellan Azure AD och nya Relic per organisation](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-new-relic-by-organization-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera nya Relic per organisation för att ge stöd för etablering med Azure AD

Arbeta med din konto representant eller få support på support.newrelic.com för att konfigurera SCIM och SSO för din organisation. Du måste ange din konto representant med:

- Organisationens namn
- Lista över nya Relic-konto-ID: n som ska associeras med organisationen

Med den här informationen skapar din konto representant en organisations post åt dig i vårt nya system och kopplar dina konton till organisationen.

Din konto representant ger dig följande information som du behöver för att konfigurera det nya Relic SCIM/SSO-programmet för din identitetsprovider:

- SCIM-slutpunkt (klient-URL)
- SCIM Bearer-token (hemlig token)

SCIM Bearer-token tillåter etablering av dina användare på nya Relic, så se till att värdet är skyddat. Din konto representant kommer att överföra SCIM Bearer-token till dig på ett säkert sätt.

## <a name="step-3-add-new-relic-by-organization-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till nya Relic efter organisation från Azure AD-programgalleriet

Lägg till nya Relic efter organisation från Azure AD-programgalleriet för att börja hantera etablering till nya Relic efter organisation. Om du tidigare har konfigurerat nya Relic efter organisation för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till nya Relic per organisation måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-new-relic-by-organization"></a>Steg 5. Konfigurera automatisk användar etablering till nya Relic efter organisation 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-new-relic-by-organization-in-azure-ad"></a>Konfigurera automatisk användar etablering för nya Relic av organisation i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **ny Relic efter organisation**.

    ![Den nya Relic-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://scim-provisioning.service.newrelic.com/scim/v2` i klient-URL. Mata in SCIM-autentiseringstoken som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till nya Relic. Om anslutningen Miss lyckas kontrollerar du att det nya Relic-kontot har administratörs behörighet och försöker igen.

    ![etablerings](./media/new-relic-by-organization-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till nya Relic efter organisation**.

9. Granska de användarattribut som synkroniseras från Azure AD till nya Relic efter organisation i avsnittet **attribut-mappning** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i nya Relic efter organisation för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att den nya Relic av organisations-API: et stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |externalId|Sträng|
   |aktiv|Boolesk|
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|
   |Name. givenName|Sträng|
   |namn. formaterad|Sträng|
   |informationen|Sträng|

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till nya Relic efter organisation**.

11. Granska gruppattributen som synkroniseras från Azure AD till nya Relic efter organisation i avsnittet **attribut-mappning** . De attribut som väljs som **matchande** egenskaper används för att matcha grupperna i nya Relic efter organisation för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externalId|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för nya Relic per organisation ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till nya Relic av organisationen genom att välja önskade värden i **området** i avsnittet **Inställningar** .

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
