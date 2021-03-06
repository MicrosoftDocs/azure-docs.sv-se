---
title: 'Självstudie: Konfigurera PureCloud av gener för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till PureCloud av gener.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: bbb9b47e42ce195a98801ee08d177efd409c597e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181675"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Självstudie: Konfigurera PureCloud av gener för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i båda PureCloud av gener och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS etablerar och avetablerar Azure AD automatiskt användare och grupper till [PureCloud av gener](https://www.genesys.com) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i PureCloud av gener
> * Ta bort användare i PureCloud av gener när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och PureCloud av gener
> * Etablera grupper och grupp medlemskap i PureCloud av gener
> * [Enkel inloggning](./purecloud-by-genesys-tutorial.md) till PureCloud av gener (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En PureCloud [organisation](https://help.mypurecloud.com/?p=81984).
* En användare med [behörighet](https://help.mypurecloud.com/?p=24360) att skapa en OAuth-klient.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bestäm vilka data som ska [mappas mellan Azure AD och PureCloud av gener](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera PureCloud av gener som stöder etablering med Azure AD

1. Skapa en [OAuth-klient](https://help.mypurecloud.com/?p=188023) som kon figurer ATS i din PureCloud-organisation.
2. Generera en token [med OAuth-klienten](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html).
3. Om du vill etablera grupp medlemskap automatiskt i PureCloud måste du [skapa grupper](https://help.mypurecloud.com/?p=52397) i PureCloud med ett identiskt namn för gruppen i Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till PureCloud från gener från Azure AD-programgalleriet

Lägg till PureCloud från gener från Azure AD-programgalleriet för att börja hantera etablering till PureCloud av gener. Om du tidigare har konfigurerat PureCloud av gener för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till PureCloud av gener måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Steg 5. Konfigurera automatisk användar etablering till PureCloud av gener 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Konfigurera automatisk användar etablering för PureCloud av gener i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **PureCloud by Genesys**.

    ![PureCloud by Genesys-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in din PureCloud av generad API-URL och OAuth-token i fälten för **klient-URL** och **hemlig token** . API-URL: en är strukturerad som `{{API Url}}/api/v2/scim/v2` med hjälp av API-URL: en för din PureCloud-region från [PureCloud Developer Center](https://developer.mypurecloud.com/api/rest/index.html). Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till PureCloud av gener. Om anslutningen Miss lyckas kontrollerar du att PureCloud-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar dialog rutan admin-autentiseringsuppgifter där du kan ange din klient U R L och hemlig token.](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till PureCloud av gener**.

9. Granska de användarattribut som synkroniseras från Azure AD till PureCloud av gener i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i PureCloud av gener för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att PureCloud-API: n stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

     |Attribut|Typ|
     |---|---|
     |userName|Sträng|
     |aktiv|Boolesk|
     |displayName|Sträng|
     |emails[type eq "work"].value|Sträng|
     |title|Sträng|
     |phoneNumbers[type eq "mobile"].value|Sträng|
     |phoneNumbers[type eq "work"].value|Sträng|
     |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
     |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
     |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
     

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att PureCloud av gener**.

11. Granska gruppattributen som synkroniseras från Azure AD till PureCloud av gener i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i PureCloud av gener för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna. PureCloud av gener stöder inte grupp skapande eller borttagning och stöder bara uppdatering av grupper.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externalId|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för PureCloud av gener ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till PureCloud av gener genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

* Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
* Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Ändringslogg

09/10 – stöd för företags-attributet "employeeNumber" har lagts till.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)