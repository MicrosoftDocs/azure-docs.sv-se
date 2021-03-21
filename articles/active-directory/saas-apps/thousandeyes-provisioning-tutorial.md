---
title: 'Självstudie: användar etablering för ThousandEyes – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735123"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Självstudie: Konfigurera ThousandEyes för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i ThousandEyes och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till ThousandEyes. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klient
* En ThousandEyes-klient med [standard planen](https://www.thousandeyes.com/pricing) eller bättre aktive rad 
* Ett användar konto i ThousandEyes med administratörs behörighet 

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av [THOUSANDEYES scim-API: et](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), som är tillgängligt för ThousandEyes-team i standard planen eller bättre.

## <a name="assigning-users-to-thousandeyes"></a>Tilldela användare till ThousandEyes

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till ThousandEyes-appen. När du har bestämt dig kan du tilldela dessa användare till ThousandEyes-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Viktiga tips för att tilldela användare till ThousandEyes

* Vi rekommenderar att en enda Azure AD-användare tilldelas ThousandEyes för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till ThousandEyes måste du välja antingen **användar** rollen eller en annan giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. **Standard åtkomst** rollen fungerar inte för etablering, och dessa användare hoppas över.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Konfigurera användar etablering till ThousandEyes 

Det här avsnittet vägleder dig genom att ansluta din Azure AD till ThousandEyes-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i ThousandEyes baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserade enkla Sign-On för ThousandEyes, genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Konfigurera automatisk etablering av användar konton till ThousandEyes i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Om du redan har konfigurerat ThousandEyes för enkel inloggning söker du efter din instans av ThousandEyes med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **ThousandEyes** i program galleriet. Välj ThousandEyes från Sök resultaten och Lägg till den i listan över program.

    ![ThousandEyes-länken i program listan](common/all-applications.png)
    
3. Välj din instans av ThousandEyes och välj sedan fliken **etablering** .

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

![Skärm bild som visar fliken etablering för ThousandEyes med automatiskt valt för etablerings läge.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. Under avsnittet **admin credentials**  kan du mata in **OAuth Bearer-token** som genererats av ditt ThousandEyes-konto (du kan söka efter och eller generera en token under din ThousandEyes konto **profil** ).

    ![Skärm bild som visar var du hittar länken konto inställningar för den aktuella konto gruppen.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din ThousandEyes-app. Om anslutningen Miss lyckas kontrollerar du att ThousandEyes-kontot har administratörs behörighet och försöker sedan steg 5 igen.

7. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till ThousandEyes**.

10. Granska de användarattribut som synkroniseras från Azure AD till ThousandEyes i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha de användar konton som kan parsas för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att det parsade API: et stöder filtrering av användare baserat på attributet. Välj knappen **Spara** för att spara ändringarna.

     |Attribut|Typ|Stöds för filtrering|
     |---|---|---|
     |externalId|Sträng|&check;|
     |userName|Sträng|&check;|
     |aktiv|Boolesk|
     |displayName|Sträng|
     |emails[type eq "work"].value|Sträng|
     |namn. formaterad|Sträng|


11. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD Provisioning-tjänsten för ThousandEyes ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till ThousandEyes genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

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