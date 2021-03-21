---
title: 'Självstudie: Konfigurera GroupTalk för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till GroupTalk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 0af41127577c172cdab74ae908f0645733d49a42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735766"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Självstudie: Konfigurera GroupTalk för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både GroupTalk och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [GroupTalk](https://www.grouptalk.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i GroupTalk
> * Ta bort användare i GroupTalk när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och GroupTalk
> * Etablera grupper och grupp medlemskap i GroupTalk

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* Ett användar konto i GroupTalk med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och GroupTalk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera GroupTalk för att ge stöd för etablering med Azure AD

1. Kontakta GroupTalk-supporten support@grouptalk.com med det **klient namn** och **ID** som du vill integrera med Azure AD.
2. När du har fått ett meddelande om att nödvändig installation av Azure AD-integreringen är klar loggar du in på GroupTalk admin och navigerar till din organisations vy. 
3. Ett konfigurations objekt för Azure AD-integration ska vara synligt. Klicka på den för att kontrol lera **klient namnet** och **ID: t**  för att hämta en **JWT (hemlig token)**. 
4. GroupTalk-klientens URL är `https://api.grouptalk.com/api/scim/` . **Klient-URL:** en och den **hemliga token** som hämtades i föregående steg kommer att anges på fliken etablering i GroupTalk-programmet i Azure Portal. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till GroupTalk från Azure AD-programgalleriet

Lägg till **GroupTalk** från Azure AD-programgalleriet för att börja hantera etablering till GroupTalk.

1. Klicka på knappen **Registrera dig för GroupTalk** , som leder dig till det administrativa GroupTalk-programmet.
2. Om du redan är inloggad på GroupTalk, logga ut för att komma till inloggnings skärmen. Välj fliken Azure AD och klicka på knappen **Logga in** .

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Logga in med ditt AD-administratörs konto och godkänn åtkomst rättigheterna för GroupTalk-programmet. Du får ett fel meddelande när det är färdigt som anger att användaren inte finns. Detta förväntas eftersom din användare inte har etablerats till GroupTalk men du har nu lagt till GroupTalk till din klient.
4. Gå tillbaka till Azure Portal och kontrol lera att **GroupTalk** nu har lagts till i dina företags program.

Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till GroupTalk måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>Steg 5. Konfigurera automatisk användar etablering till GroupTalk 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Konfigurera automatisk användar etablering för GroupTalk i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **GroupTalk**.

    ![GroupTalk-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , skriver du in din GroupTalk-klient-URL och den hemliga token som hämtades tidigare från steg 2. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till GroupTalk. Om anslutningen Miss lyckas kontrollerar du att GroupTalk-kontot har administratörs behörighet och försöker igen. Du kan alltid hämta en ny hemlig token enligt beskrivningen i steg 2.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till GroupTalk**.

9. Granska de användarattribut som synkroniseras från Azure AD till GroupTalk i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i GroupTalk för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att GroupTalk-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |phoneNumbers[type eq "mobile"].value|Sträng|&check;|
   |emails[type eq "work"].value|Sträng|&check;|
   |aktiv|Boolesk|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |externalId|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: organisation|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GroupTalk: 2.0: användare: Label1|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GroupTalk: 2.0: användare: Label2|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GroupTalk: 2.0: användare: Label3|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GroupTalk: 2.0: användare: Label4|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GroupTalk: 2.0: användare: Label5|Sträng|


10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till GroupTalk**.

11. Granska gruppattributen som synkroniseras från Azure AD till GroupTalk i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i GroupTalk för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |medlemmar|Referens|
      |externalId|Sträng|      
      |urn: IETF: params: scim: schemas: tillägg: GroupTalk: 2.0: grupp: Beskrivning|Sträng|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för GroupTalk ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till GroupTalk genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).
4. Du kan kontakta GroupTalk-supporten för ytterligare etablerings loggar som har kon figurer ATS som anpassade rapporter i GroupTalk-administratören. Dessa kan ge ytterligare tips om varför användare och grupper inte kan etableras korrekt.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)