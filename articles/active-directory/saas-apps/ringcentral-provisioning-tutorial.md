---
title: 'Självstudie: Konfigurera RingCentral för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till RingCentral.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: 1891af9acae2b976a18f68983693a7df559b6476
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800777"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Självstudie: Konfigurera RingCentral för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både RingCentral och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [RingCentral](https://www.ringcentral.com/office/plansandpricing.html) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i RingCentral
> * Ta bort användare i RingCentral när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och RingCentral
> * [Enkel inloggning](./ringcentral-tutorial.md) till RingCentral (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* [En RingCentral-klient](https://www.ringcentral.com/office/plansandpricing.html)
* Ett användar konto i RingCentral med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och RingCentral](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-ringcentral-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera RingCentral för att ge stöd för etablering med Azure AD

Ett [RingCentral](https://www.ringcentral.com/office/plansandpricing.html) -administratörskonto krävs för att auktorisera i avsnittet admin credentials i steg 5.

> [!NOTE]
> Information om hur du tilldelar licenser till användare finns i video länken [här](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language).

## <a name="step-3-add-ringcentral-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till RingCentral från Azure AD-programgalleriet

Lägg till RingCentral från Azure AD-programgalleriet för att börja hantera etablering till RingCentral. Om du tidigare har konfigurerat RingCentral för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till RingCentral måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-ringcentral"></a>Steg 5. Konfigurera automatisk användar etablering till RingCentral 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Konfigurera automatisk användar etablering för RingCentral i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **RingCentral**.

    ![RingCentral-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** klickar du på **auktorisera**. Du kommer att omdirigeras till RingCentral inloggnings sida. Mata in e-post/telefonnummer och lösen ord och klicka på knappen **Logga in** . Klicka på **auktorisera** på sidan för RingCentral- **åtkomstbegäran** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till RingCentral. Om anslutningen Miss lyckas kontrollerar du att RingCentral-kontot har administratörs behörighet och försöker igen.

   ![AAD](./media/ringcentral-provisioning-tutorial/admincredentials.png)

   ![Access](./media/ringcentral-provisioning-tutorial/authorize.png)

   ![Auktorisera](./media/ringcentral-provisioning-tutorial/accessrequest.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till RingCentral**.

9. Granska de användarattribut som synkroniseras från Azure AD till RingCentral i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i RingCentral för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att RingCentral-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |externalId|Sträng|
   |aktiv|Boolesk|
   |title|Sträng|
   |emails[type eq "work"].value|Sträng|
   |adresser [Type EQ "Work"]. land|Sträng|
   |adresser [Type EQ "Work"]. region|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |adresser [Type EQ "Work"]. Postnr|Sträng|
   |adresser [Type EQ "Work"]. streetAddress|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för RingCentral ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till RingCentral genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Ändringslogg

* 09/10/2020 – stöd för attributen "displayName" och "Manager" har tagits bort.
* 03/15/2021 – uppdaterad Authorization-metod från permanent Bearer-token till OAuth Code Grant-flöde.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)