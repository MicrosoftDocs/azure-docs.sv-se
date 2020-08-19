---
title: 'Självstudie: Konfigurera Samanage för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Samanage.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 21a3c81d9a24cc63d3fc77c95c94df1e9113d292
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543456"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Samanage för automatisk användar etablering
I den här självstudien beskrivs de steg du behöver utföra i både Samanage och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [Samanage](https://www.samanage.com/pricing/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-samange-application"></a>Migrera till det nya Samange-programmet

Om du har en befintlig integrering med Samanage kan du läsa avsnittet nedan om kommande ändringar. Om du konfigurerar Samanage för första gången kan du hoppa över det här avsnittet och flytta till funktioner som **stöds**.

#### <a name="whats-changing"></a>Vad förändras?
* Ändringar på Azure AD-sidan: autentiseringsmetoden för att etablera användare i Samange har tidigare varit **grundläggande autentisering**. Snart kommer du att se autentiseringsmetoden har ändrats till den **hemliga token för lång livs längd**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Vad behöver jag för att migrera min befintliga anpassade integrering till det nya programmet?
Om du har en befintlig Samanage-integrering med giltiga administratörsautentiseringsuppgifter, **krävs ingen åtgärd**. Vi migrerar automatiskt kunder till det nya programmet. Den här processen är helt klar bakom kulisserna. Om de befintliga autentiseringsuppgifterna upphör att gälla, eller om du behöver ge åtkomst till programmet igen, måste du skapa en hemlig token för lång livs längd. Om du vill generera en ny token, se steg 2 i den här artikeln.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hur ser jag om mitt program har migrerats? 
När ditt program migreras ersätts fälten **Administratörs användar namn** och **Administratörs lösen ord** i avsnittet **admin-autentiseringsuppgifter** med ett enda **hemligt token** -fält.

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Samanage
> * Ta bort användare i Samanage när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Samanage
> * Etablera grupper och grupp medlemskap i Samanage
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) till Samanage (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör). 
* En [Samanage-klient](https://www.samanage.com/pricing/) med Professional-paketet.
* Ett användar konto i Samanage med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Samanage](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-samanage-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Samanage för att ge stöd för etablering med Azure AD

Om du vill generera en hemlig token för autentisering kan du se [detta](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-samanage-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Samanage från Azure AD-programgalleriet

Lägg till Samanage från Azure AD-programgalleriet för att börja hantera etablering till Samanage. Om du tidigare har konfigurerat Samanage för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Samanage måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-samanage"></a>Steg 5. Konfigurera automatisk användar etablering till Samanage 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurera automatisk användar etablering för Samanage i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Samanage**.

    ![Samanage-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://api.samanage.com` i **klient-URL**.  Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Samanage. Om anslutningen Miss lyckas kontrollerar du att Samanage-kontot har administratörs behörighet och försöker igen

    ![etablerings](./media/samanage-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Samanage**.

9. Granska de användarattribut som synkroniseras från Azure AD till Samanage i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Samanage för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att Samanage-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

      ![Samange användar mappningar](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Samanage**.

11. Granska gruppattributen som synkroniseras från Azure AD till Samanage i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Samanage för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      ![Samange grupp mappningar](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Samanage ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Samanage genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
2. Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
3. Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Kopplings begränsningar

Om du väljer alternativet **synkronisera alla användare och grupper** och konfigurerar ett värde för attributet Samanage **roles** måste värdet under **standardvärdet om null (är valfritt)** anges i följande format:

- {"displayName": "roll"}, där roll är det standardvärde som du vill använda.

## <a name="change-log"></a>Ändringslogg

* 04/22/2020 – uppdaterad auktoriseringskod från grundläggande autentisering till lång livs längd på den hemliga token.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
