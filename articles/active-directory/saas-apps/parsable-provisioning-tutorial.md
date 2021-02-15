---
title: 'Självstudie: Konfigurera parsning för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till att parsas.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 1ec33ea6-bff4-4665-bf2b-f4037ff28c09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 817b6b373f521543234cf02818cde8c4b4ba40c1
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526433"
---
# <a name="tutorial-configure-parsable-for-automatic-user-provisioning"></a>Självstudie: Konfigurera parsning för automatisk användar etablering

I den här självstudien beskrivs de steg som du måste utföra i både parsable och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS, etablerar och avetablerar Azure AD automatiskt användare och grupper som kan [tolkas](https://www.parsable.com/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare som går att parsa
> * Ta bort användare som kan parsas när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och parsning
> * Etablera grupper och grupp medlemskap i tolknings bara

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En Parsbar klient (Team).
* Ett användar konto som går att parsa med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bestäm vilka data som ska [mappas mellan Azure AD och parsas](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-parsable-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera tolkning som stöd för etablering med Azure AD

1. Kontakta den tolknings bara kund som är ansvarig för att delta i den här för hands versions funktionen.
2. De kommer att ytterligare hjälpa till att få fram ett support ärende för att erhålla nödvändig **Bearer-token** (hemlig token).
3. Kopiera och spara **Bearer-token**. Det här värdet anges i fältet **hemlig token** * på fliken etablering i det tolknings bara programmet i Azure Portal.

## <a name="step-3-add-parsable-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till parsning från Azure AD-programgalleriet

Lägg till parsning från Azure AD-programgalleriet för att börja hantera etablering till parsning. Om du tidigare har konfigurerat en parsning för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till att kunna parsas måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-parsable"></a>Steg 5. Konfigurera automatisk användar etablering till parsning 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-parsable-in-azure-ad"></a>Konfigurera automatisk användar etablering för parsning i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du kan **parsas**.

    ![Den parsade länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du skriva in den parsade klient-URL: en och den hemliga token. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till parsning. Om anslutningen Miss lyckas kontrollerar du att det går att parsa kontot administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare att parsa**.

9. Granska de användarattribut som synkroniseras från Azure AD till parsning i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha de användar konton som kan parsas för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att det parsade API: et stöder filtrering av användare baserat på attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |displayName|Sträng|

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper som ska kunna parsas**.

11. Granska gruppattributen som synkroniseras från Azure AD till parsning i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna som kan parsas för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |medlemmar|Referens|
12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill att Azure AD Provisioning-tjänsten ska kunna parsas ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till parsning genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Ändringslogg

* 02/15/2021-grupp etablering har Aktiver ATS.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)