---
title: 'Självstudie: Konfigurera Adobe Identity Management för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till Adobe Identity Management.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6ae05dc7-1265-44b4-a20c-512b5218b9d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: Zhchia
ms.openlocfilehash: a87a08db672c459138fc1efd865332dc0f19944e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586685"
---
# <a name="tutorial-configure-adobe-identity-management-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Adobe Identity Management för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Adobe Identity Management och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i Adobe Identity Management med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Adobe Identity Management
> * Ta bort användare i Adobe Identity Management när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Adobe Identity Management
> * Etablera grupper och grupp medlemskap i Adobe Identity Management
> * Enkel inloggning till Adobe Identity Management (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En federerad katalog i [Adobe admin-konsolen](https://adminconsole.adobe.com/) med verifierade domäner.

> [!NOTE]
> Om din organisation använder verktyget för användar synkronisering eller en UMAPI-integrering måste du först pausa integreringen. Lägg sedan till automatisk etablering i Azure AD för att automatisera användar hantering från Azure Portal. När automatisk etablering i Azure AD har kon figurer ATS och körs kan du ta bort verktyget för användar synkronisering eller UMAPI-integrering.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Adobe Identity Management](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-adobe-identity-management-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Adobe Identity Management för att ge stöd för etablering med Azure AD

1. Logga in på [Adobe admin-konsolen](https://adminconsole.adobe.com/). Navigera till **inställningar > katalog information > synkronisering**. 

2. Klicka på **Lägg till synkronisering**.

      ![Lägg till](media/adobe-identity-management-provisioning-tutorial/add-sync.png)

3. Välj **synkronisera användare från Microsoft Azure** och klicka på **Nästa**.

      ![Skärm bild som visar "synkronisera användare från Microsoft Azure Active Directory" vald.](media/adobe-identity-management-provisioning-tutorial/sync-users.png)

4. Kopiera och spara **klient-URL:** en och den **hemliga token**. Dessa värden anges i fälten klient- **URL** och **hemligt token** på fliken etablering i ditt Adobe Identity Management-program i Azure Portal.

      ![Synkronisera](media/adobe-identity-management-provisioning-tutorial/token.png)

## <a name="step-3-add-adobe-identity-management-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Adobe Identity Management från Azure AD Application Gallery

Lägg till Adobe Identity Management från Azure AD-programgalleriet för att börja hantera etablering till Adobe Identity Management. Om du tidigare har installerat Adobe Identity Management för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Adobe Identity Management måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-adobe-identity-management"></a>Steg 5. Konfigurera automatisk användar etablering till Adobe Identity Management 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-adobe-identity-management-in-azure-ad"></a>Konfigurera automatisk användar etablering för Adobe Identity Management i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Adobe Identity Management**.

    ![Länken för Adobe Identity Management i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du läsa in din Adobe Identity Management klient-URL och den hemliga token som hämtades tidigare från steg 2. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Adobe Identity Management. Om anslutningen Miss lyckas kontrollerar du att ditt Adobe Identity Management-konto har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Adobe Identity Management**.

9. Granska de användarattribut som synkroniseras från Azure AD till Adobe Identity Management i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Adobe Identity Management för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att Adobe Identity Management-API: et stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |emails[type eq "work"].value|Sträng|
   |aktiv|Boolesk|
   |adresser [Type EQ "Work"]. land|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Adobe: 2.0: användare: emailAliases|Sträng|

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till Adobe Identity Management**.

11. Granska gruppattributen som synkroniseras från Azure AD till Adobe Identity Management i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Adobe Identity Management för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Adobe Identity Management ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Adobe Identity Management genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)