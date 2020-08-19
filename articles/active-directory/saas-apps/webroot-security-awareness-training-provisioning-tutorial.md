---
title: 'Självstudie: Konfigurera säkerhets medvetenhets utbildning för Webroot för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Webroot Security medvetenhet Training.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: 9345aaac306c8c009d6fc0a01c57beed3a22b10b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523498"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Självstudie: Konfigurera säkerhets medvetenhets utbildning för Webroot för automatisk användar etablering

I den här självstudien beskrivs de steg som du behöver utföra i både säkerhets medvetenhets träningen för Webroot och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS, etablerar och avetablerar Azure AD automatiskt användare och grupper i [säkerhets medvetenheten för säkerhets medvetenhet](https://www.webroot.com/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i informations utbildning för säkerhets medvetenhet i Webroot
> * Ta bort användare i säkerhets medvetenhets träning för Webroot när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Webroot Security medvetenhet Training
> * Etablera grupper och grupp medlemskap i säkerhets medvetenhets träning för Webroot

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör).
* En hanterad tjänst leverantörs konsol med säkerhets medvetenhets utbildning för Webroot aktiverat för minst en av dina platser.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Webroot Security medvetenhet Training](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera säkerhets medvetenhets träning för Webroot som stöder etablering med Azure AD

### <a name="obtain-a-secret-token"></a>Hämta en hemlig token

Om du vill ansluta din webbplats till Azure AD måste du skaffa en **hemlig token** för platsen i hanterings konsolen för Webroot.

1. Logga in på din [Webroot Management-konsol](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. På fliken **platser** klickar du på kugg hjuls ikonen i kolumnen utbildning av säkerhets medvetenhet för den plats som du vill ansluta till Azure AD.

    ![Kugg hjuls ikon](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Klicka på knappen för att **Konfigurera Azure AD-integrering**.

    ![Konfigurera Azure AD-integrering](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Kopiera och spara den **hemliga token**. Det här värdet anges i fältet hemligt token på fliken etablering i övnings programmet för säkerhets medvetenhet för Webroot i Azure Portal.

5. Klicka på **Klar**.

    ![Kopiera hemlig token](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till säkerhets medvetenhets utbildning för Webroot från Azure AD-programgalleriet

Lägg till webinriktad-utbildning för säkerhets medvetenhet från Azure AD-programgalleriet för att börja hantera etablering till säkerhets medvetenhets träning för Webroot. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till säkerhets medvetenhets utbildningen i Webroot måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>Steg 5. Konfigurera automatisk användar etablering för säkerhets medvetenhets utbildning i Webroot 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Konfigurera automatisk användar etablering för säkerhets medvetenhets träning för Webroot i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **säkerhets medvetenhets utbildning för Webroot**.

    ![Länken för säkerhets medvetenhet för Webroot i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://awarenessapi.webrootanywhere.com/api/v2/scim` i **klient-URL**. Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till säkerhets medvetenhets utbildningen för Webroot. Om anslutningen Miss lyckas kontrollerar du att ditt konto för säkerhets medvetenhet i Webroot har administratörs behörighet och försöker igen.

    ![etablerings](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till Webroot Security medvetenhet Training i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i säkerhets medvetenhets träningen för Webroot för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att API: t för säkerhets medvetenheten Webroot stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |externalId|Sträng|&check;|
   |Name. givenName|Sträng|
   |Name. familyName|Sträng|
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|

10. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory grupper**.

11. Granska gruppattributen som synkroniseras från Azure AD till Webroot Security medvetenhet Training i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i säkerhets medvetenhets träningen för Webroot för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |medlemmar|Referens|
      |externalId|Sträng|

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för säkerhets medvetenhets träning för Webroot, ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Webroot-utbildningen för säkerhets medvetenhet genom att välja önskade värden i **området** i avsnittet **Inställningar** .

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
