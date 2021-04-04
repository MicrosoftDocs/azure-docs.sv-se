---
title: 'Självstudie: Konfigurera HootSuite för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till HootSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2020
ms.author: Zhchia
ms.openlocfilehash: b371baec663ffc7ff5909e04965353e79acb9708
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96177080"
---
# <a name="tutorial-configure-hootsuite-for-automatic-user-provisioning"></a>Självstudie: Konfigurera HootSuite för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både HootSuite och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [HootSuite](https://hootsuite.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i HootSuite
> * Ta bort användare i HootSuite när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och HootSuite
> * Etablera grupper och grupp medlemskap i HootSuite
> * [Enkel inloggning](./hootsuite-tutorial.md) till HootSuite (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* Ett användar konto med [HootSuite](http://www.hootsuite.com/) som har behörighet att **Hantera medlemmar** i organisationen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och HootSuite](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-hootsuite-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera HootSuite för att ge stöd för etablering med Azure AD

Kontakta för dev.support@hootsuite.com att få en lång och Hemlig hemlig token som krävs i senare steg. 

## <a name="step-3-add-hootsuite-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till HootSuite från Azure AD-programgalleriet

Lägg till HootSuite från Azure AD-programgalleriet för att börja hantera etablering till HootSuite. Om du tidigare har konfigurerat HootSuite för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till HootSuite måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-hootsuite"></a>Steg 5. Konfigurera automatisk användar etablering till HootSuite 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-hootsuite-in-azure-ad"></a>Konfigurera automatisk användar etablering för HootSuite i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](./media/hootsuite-provisioning-tutorial/enterprise-applications.png)

    ![Bladet Alla program](./media/hootsuite-provisioning-tutorial/all-applications.png)

2. I listan program väljer du **HootSuite**.

    ![HootSuite-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** . Klicka på **Kom igång**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

    ![Kom igång-blad](./media/hootsuite-provisioning-tutorial/get-started.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://platform.hootsuite.com/scim/v2` i klient-URL. Mata in det långsiktiga hemliga token-värdet som hämtades tidigare i **steg 2**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till HootSuite. Om anslutningen Miss lyckas kontrollerar du att HootSuite-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar dialog rutan admin-autentiseringsuppgifter där du kan ange din klient U R L och hemlig token.](./media/hootsuite-provisioning-tutorial/provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till HootSuite i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i HootSuite för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att HootSuite-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |emails[type eq "work"].value|Sträng|
   |aktiv|Boolesk|
   |displayName|Sträng|
   |preferredLanguage|Sträng|
   |timezone|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper**.

11. Granska gruppattributen som synkroniseras från Azure AD till HootSuite i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i HootSuite för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externalId|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för HootSuite ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till HootSuite genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

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

* 10/22/2020 – stöd har lagts till för användarattribut "Name. givenName" och "Name. familyName". De anpassade tilläggs attributen "organizationIds" och "teamIds" har tagits bort för användare.
Stöd har lagts till för gruppattributen "displayName", "Members" och "externalId".

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)