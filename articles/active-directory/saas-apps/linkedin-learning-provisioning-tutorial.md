---
title: 'Självstudie: Konfigurera LinkedIn-utbildning för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till LinkedIn Learning.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/30/2020
ms.author: Zhchia
ms.openlocfilehash: 55b69fd33f9e891ed0b1cf1e8ae3051776a6791b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549848"
---
# <a name="tutorial-configure-linkedin-learning-for-automatic-user-provisioning"></a>Självstudie: Konfigurera LinkedIn-utbildning för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både LinkedIn-utbildning och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper till [LinkedIn-inlärning](https://learning.linkedin.com/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare på LinkedIn-utbildning
> * Ta bort användare på LinkedIn-utbildning när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och LinkedIn Learning
> * Etablera grupper och grupp medlemskap på LinkedIn-utbildning
> * [Enkel inloggning](linkedinlearning-tutorial.md) till LinkedIn-utbildning (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör). 
* Godkännande-och SCIM aktiverat för LinkedIn-utbildning (kontakta via e-post).

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och LinkedIn Learning](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-linkedin-learning-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera LinkedIn-utbildning för att stödja etablering med Azure AD
1. Logga in på [LinkedIn-inlärnings inställningar](https://www.linkedin.com/learning-admin/settings/global). Välj **scim-installation** och välj sedan **Lägg till ny scim-konfiguration**.

   ![Konfiguration av SCIM-konfiguration](./media/linkedin-learning-provisioning-tutorial/learning-scim-settings.png)

2. Ange ett namn för konfigurationen och ange **automatiskt tilldela licenser** till på. Klicka sedan på **generera token**.

   ![Konfigurations namn för SCIM](./media/linkedin-learning-provisioning-tutorial/learning-scim-configuration.png)

3. När konfigurationen har skapats ska en **åtkomsttoken** genereras. Behåll detta kopierat för senare.

   ![SCIM-åtkomsttoken](./media/linkedin-learning-provisioning-tutorial/learning-scim-token.png)

4. Du kan utfärda eventuella befintliga konfigurationer igen (vilket kommer att generera en ny token) eller ta bort dem.

## <a name="step-3-add-linkedin-learning-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till LinkedIn-inlärning från Azure AD Application Gallery

Lägg till LinkedIn-inlärning från Azure AD-programgalleriet för att börja hantera etablering till LinkedIn-inlärning. Om du tidigare har konfigurerat LinkedIn-inlärning för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till LinkedIn-inlärning måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-linkedin-learning"></a>Steg 5. Konfigurera automatisk användar etablering till LinkedIn-utbildning 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-linkedin-learning-in-azure-ad"></a>Konfigurera automatisk användar etablering för LinkedIn-inlärning i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **LinkedIn-inlärning**.

    ![LinkedIn Learning länken i listan med program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://api.linkedin.com/scim` i **klient-URL**. Mata in värdet för åtkomsttoken som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till LinkedIn-inlärning. Om anslutningen Miss lyckas kontrollerar du att ditt LinkedIn Learning-konto har administratörs behörighet och försöker igen.

    ![etablerings](./media/linkedin-learning-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till LinkedIn-inlärning i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i LinkedIn-inlärning för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att LinkedIn Learning API stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |externalId|Sträng|&check;|
   |userName|Sträng|
   |Name. givenName|Sträng|
   |Name. familyName|Sträng|
   |displayName|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |rubrik|Sträng|
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|

10. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory grupper**.

11. Granska gruppattributen som synkroniseras från Azure AD till LinkedIn-inlärning i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i LinkedIn-inlärning för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    |Attribut|Typ|Stöds för filtrering|
    |---|---|---|
    |displayName|Sträng|&check;|
    |medlemmar|Referens|
    |externalId|Sträng|

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för LinkedIn-utbildning ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till LinkedIn-inlärning genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

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
