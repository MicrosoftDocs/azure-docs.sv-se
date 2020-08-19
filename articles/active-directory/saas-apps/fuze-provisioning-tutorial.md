---
title: 'Självstudie: Konfigurera Fuze för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Fuze.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 08fe2cd87200e1a22f36ab01b58c6cf82fd1f007
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550832"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Fuze för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Fuze och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till [Fuze](https://www.fuze.com/). Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Fuze
> * Ta bort användare i Fuze när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Fuze
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/fuze-tutorial) till Fuze (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör).
* [En Fuze-klient](https://www.fuze.com/).
* Ett användar konto i Fuze med administratörs behörighet.


## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Fuze](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Fuze för att ge stöd för etablering med Azure AD

Innan du konfigurerar Fuze för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Fuze. 

1. Börja med att kontakta din Fuze-representant för följande nödvändiga information:

    * Lista över Fuze produkt-SKU: er som används i ditt företag.
    * Lista över plats koder för företagets platser.
    * Lista över avdelnings koder för ditt företag.

2. Du hittar dessa SKU: er och koder i ditt Fuze-kontrakt och konfigurations dokument, eller genom att kontakta din Fuze-representant.

3. När kraven har tagits emot tillhandahåller Fuze-representanten den Fuze som krävs för att aktivera integreringen. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt Fuze-program i Azure Portal.

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till FUZE från Azure AD-programgalleriet

Lägg till FUZE från Azure AD-programgalleriet för att börja hantera etablering till Fuze. Om du tidigare har konfigurerat Fuze för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare till Fuze måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare kan du kontrol lera detta genom att tilldela en eller två användare till appen. När omfång är inställt på alla användare kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>Steg 5. Konfigurera automatisk användar etablering till Fuze 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Fuze baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Konfigurera automatisk användar etablering för Fuze i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Fuze**.

    ![Länken för Fuze i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du läsa in **scim 2,0-bas-URL och scim-autentiseringstoken** som hämtades tidigare från Fuze-representanten i **klient-URL** och **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Fuze. Om anslutningen Miss lyckas kontrollerar du att Fuze-kontot har administratörs behörighet och försöker igen.

    ![Klientens URL-token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Fuze**.

9. Granska de användarattribut som synkroniseras från Azure AD till Fuze i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Fuze för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |Name. givenName|Sträng|
   |Name. familyName|Sträng|
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|
   |aktiv|Boolesk|

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Fuze ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Fuze genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

1. Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
2. Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
3. Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Fuze stöder anpassade SCIM-attribut som kallas **rättigheter**. Dessa attribut kan bara skapas och uppdateras inte. 

## <a name="change-log"></a>Ändringslogg

* 06/15/2020 – hastighets begränsning för integrering justerat till 10 begär Anden/sekund.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md).