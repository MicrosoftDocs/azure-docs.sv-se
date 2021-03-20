---
title: 'Självstudie: Konfigurera SolarWinds Service Desk (tidigare Samanage) för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till SolarWinds Service Desk (tidigare Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: cca46d47003a1611c861986f8df839de57500db6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181496"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Självstudie: Konfigurera SolarWinds Service Desk (tidigare Samanage) för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både SolarWinds Service Desk (tidigare Samanage) och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [SolarWinds Service Desk](https://www.samanage.com/pricing/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migrera till det nya SolarWinds Service Desk-programmet

Om du har en befintlig integrering med SolarWinds Service Desk, se följande avsnitt om kommande ändringar. Om du konfigurerar SolarWinds Service Desk för första gången kan du hoppa över det här avsnittet och flytta till funktioner som **stöds**.

#### <a name="whats-changing"></a>Vad förändras?

* Ändringar på Azure AD-sidan: autentiseringsmetoden för att etablera användare i Samange har tidigare varit **grundläggande autentisering**. Snart kommer du att se autentiseringsmetoden har ändrats till den **hemliga token för lång livs längd**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Vad behöver jag för att migrera min befintliga anpassade integrering till det nya programmet?

Om du har en befintlig SolarWinds för service skriv bord med giltiga administratörsautentiseringsuppgifter **krävs ingen åtgärd**. Vi migrerar automatiskt kunder till det nya programmet. Den här processen är helt klar bakom kulisserna. Om de befintliga autentiseringsuppgifterna upphör att gälla, eller om du behöver ge åtkomst till programmet igen, måste du skapa en hemlig token för lång livs längd. Om du vill generera en ny token, se steg 2 i den här artikeln.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hur ser jag om mitt program har migrerats? 

När ditt program migreras ersätts fälten **Administratörs användar namn** och **Administratörs lösen ord** i avsnittet **admin-autentiseringsuppgifter** med ett enda **hemligt token** -fält.

## <a name="capabilities-supported"></a>Funktioner som stöds

> [!div class="checklist"]
> * Skapa användare i SolarWinds Service Desk
> * Ta bort användare i SolarWinds Service Desk när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och SolarWinds Service Desk
> * Etablera grupper och grupp medlemskap i SolarWinds Service Desk
> * [Enkel inloggning](./samanage-tutorial.md) till SolarWinds Service Desk (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En [SolarWinds Service Desk-klient](https://www.samanage.com/pricing/) med Professional-paketet.
* Ett användar konto i SolarWinds Service Desk med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och SolarWinds Service Desk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera SolarWinds Service Desk som stöd för etablering med Azure AD

Information om hur du skapar en hemlig token för autentisering finns i [självstudie-token-autentisering för API-integrering](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till SolarWinds Service Desk från Azure AD Application Gallery

Lägg till SolarWinds Service Desk från Azure AD-programgalleriet för att börja hantera etablering till SolarWinds Service Desk. Om du tidigare har konfigurerat SolarWinds-helpdesk för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till SolarWinds-helpdesk måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Steg 5. Konfigurera automatisk användar etablering till SolarWinds-helpdesk 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Konfigurera automatisk användar etablering för SolarWinds Service Desk i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SolarWinds Service Desk**.

3. Välj fliken **Etablering**.

    ![Skärm bild som visar fliken etablering vald.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild som visar etablerings läget inställt på automatiskt.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://api.samanage.com` i **klient-URL**.  Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Välj **Testa anslutning** för att se till att Azure AD kan ansluta till SolarWinds-helpdesk. Om anslutningen Miss lyckas kontrollerar du att ditt SolarWinds-konto har administratörs behörighet och försöker igen.

    ![Skärm bild som visar knappen Testa anslutning vald.](./media/samanage-provisioning-tutorial/provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till SolarWinds-supportavdelningen**.

9. Granska de användarattribut som synkroniseras från Azure AD till SolarWinds Service Desk i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i SolarWinds-helpdesk för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att SolarWinds Service Desk-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

      ![Samange användar mappningar](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att SolarWinds Service Desk**.

11. Granska gruppattributen som synkroniseras från Azure AD till SolarWinds Service Desk i avsnittet **attribut-mappning** . De attribut som väljs som **matchande** egenskaper används för att matcha grupperna i SolarWinds-helpdesk för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      ![Samange grupp mappningar](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för SolarWinds Service Desk ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till SolarWinds-helpdesk genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera väljer du **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

Om du väljer alternativet **synkronisera alla användare och grupper** och konfigurerar ett värde för attributet SolarWinds Service Desk- **roller** , måste värdet under **standardvärdet om värdet är null (valfritt)** anges i följande format:

- {"displayName": "roll"}, där roll är det standardvärde som du vill använda.

## <a name="change-log"></a>Ändringslogg

* 09/14/2020 – ändra företags namnet i två SaaS-självstudier från Samanage till SolarWinds Service Desk (tidigare Samanage) per https://github.com/ravitmorales .
* 04/22/2020 – uppdaterad auktoriseringskod från grundläggande autentisering till lång livs längd på den hemliga token.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)