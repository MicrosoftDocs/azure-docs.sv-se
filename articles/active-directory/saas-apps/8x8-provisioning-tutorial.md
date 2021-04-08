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
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: a6aa4ad009d037e6ea0d1ade3cc9735351bd634a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558869"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Självstudie: Konfigurera 8x8 för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både 8x8-administratörskonsolen och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [8x8](https://www.8x8.com) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i 8x8
> * Ta bort användare i 8x8 när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och 8x8
> * [Enkel inloggning](./8x8virtualoffice-tutorial.md) till 8x8 (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör).
* En prenumeration på 8x8 X-serien på alla nivåer.
* Ett 8x8-användarkonto med administratörs behörighet i [Administratörs konsolen](https://vo-cm.8x8.com).
* [Enskilda Sign-On med Azure AD](./8x8virtualoffice-tutorial.md) har redan kon figurer ATS.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och 8x8](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera 8x8 för att ge stöd för etablering med Azure AD

Det här avsnittet vägleder dig genom stegen för att konfigurera 8x8 för att ge stöd för etablering med Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-admin-console"></a>Så här konfigurerar du en åtkomsttoken för användar etablering i 8x8-administratörs konsolen:

1. Logga in på [Administratörs konsolen](https://admin.8x8.com). Välj **identitets hantering**.

   ![Administratör](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Klicka på länken **Visa information om användar etablering** för att generera en token.

   ![Visa](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Kopiera **8X8 URL** -och **8X8 API-token** -värden. Dessa värden anges i fälten klient- **URL** och **hemlig token** på fliken etablering i 8x8-programmet i Azure Portal.

   ![Token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till 8x8 från Azure AD-programgalleriet

Lägg till 8x8 från Azure AD-programgalleriet för att börja hantera etablering till 8x8. Om du tidigare har konfigurerat 8x8 för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Detta är det enklare alternativet och används av de flesta personer.

Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till 8x8 måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Steg 5. Konfigurera automatisk användar etablering till 8x8 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i 8x8 baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Konfigurera automatisk användar etablering för 8x8 i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Bladet Alla program](./media/8x8-provisioning-tutorial/all-applications.png)

2. I listan program väljer du **8x8**.

    ![8x8-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** . Klicka på **Kom igång**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

   ![Kom igång-blad](./media/8x8-provisioning-tutorial/get-started.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , kopierar du **8x8-URL: en** från administratörs konsolen till **klient-URL: en**. Kopiera **8x8-API-token** från administratörs konsolen till en **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till 8x8. Om anslutningen Miss lyckas kontrollerar du att 8x8-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar dialog rutan admin-autentiseringsuppgifter där du kan ange din klient U R L och hemlig token.](./media/8x8-provisioning-tutorial/provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till 8x8 i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i 8x8 för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att 8x8-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Kommentarer|
   |---|---|---|
   |userName|Sträng|Anger både användar namn och Federations-ID|
   |externalId|Sträng||
   |aktiv|Boolesk||
   |title|Sträng||
   |emails[type eq "work"].value|Sträng||
   |name.givenName|Sträng||
   |name.familyName|Sträng||
   |phoneNumbers[type eq "mobile"].value|Sträng|Personlig kontakt nummer|
   |phoneNumbers[type eq "work"].value|Sträng|Personlig kontakt nummer|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng||
   |urn: IETF: params: scim: schemas: tillägg: 8x8:1.1: användare: webbplats|Sträng|Kan inte uppdateras efter att användaren har skapats|
   |locale|Sträng|Inte mappad som standard|
   |timezone|Sträng|Inte mappad som standard|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för 8x8 ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till 8x8 genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som har etablerats eller har misslyckats.
2. Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)