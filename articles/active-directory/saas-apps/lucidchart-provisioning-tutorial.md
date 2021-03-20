---
title: 'Självstudie: Konfigurera Lucidchart för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Lucidchart.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: e7447a8d742caef7a2c2ff0211e7e8002307b9e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96178037"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Lucidchart för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Lucidchart och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [Lucidchart](https://www.lucidchart.com/user/117598685#/subscriptionLevel) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Lucidchart
> * Ta bort användare i Lucidchart när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Lucidchart
> * Etablera grupper och grupp medlemskap i Lucidchart
> * [Enkel inloggning](./lucidchart-tutorial.md) till Lucidchart (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En LucidChart-klient med [företags planen](https://www.lucidchart.com/user/117598685#/subscriptionLevel) eller bättre aktive rad.
* Ett användar konto i LucidChart med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Lucidchart](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-lucidchart-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Lucidchart för att ge stöd för etablering med Azure AD

1. Logga in på [Lucidchart-administratörskonsolen](https://www.lucidchart.com). Gå till **Team > app integration**.

      :::image type="content" source="./media/lucidchart-provisioning-tutorial/team1.png" alt-text="Skärm bild av Lucidchart-administratörskonsolen. Grupp-menyn är markerad och öppen. Under admin är integrering av appar markerat." border="false":::

2. Navigera till **scim**.

      :::image type="content" source="./media/lucidchart-provisioning-tutorial/scim.png" alt-text="Skärm bild av Lucidchart-administratörskonsolen. I ett stort S C I M-knapp är texten S C I M markerad och en aktive rad banderoll visas." border="false":::

3. Rulla nedåt för att se **Bearer-token** och **LUCIDCHART bas-URL**. Kopiera och spara **Bearer-token**. Det här värdet anges i fältet **hemlig token** * på fliken etablering i ditt Lucidchart-program i Azure Portal. 

      ![Lucidchart-token](./media/lucidchart-provisioning-tutorial/token.png)

## <a name="step-3-add-lucidchart-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Lucidchart från Azure AD-programgalleriet

Lägg till Lucidchart från Azure AD-programgalleriet för att börja hantera etablering till Lucidchart. Om du tidigare har konfigurerat Lucidchart för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Lucidchart måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-lucidchart"></a>Steg 5. Konfigurera automatisk användar etablering till Lucidchart 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-lucidchart-in-azure-ad"></a>Konfigurera automatisk användar etablering för Lucidchart i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Lucidchart**.

    ![Lucidchart-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du mata in värdet för **Bearer-token**  som hämtades tidigare i fältet **hemligt token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Lucidchart. Om anslutningen Miss lyckas kontrollerar du att Lucidchart-kontot har administratörs behörighet och försöker igen.

      ![etablerings](./media/Lucidchart-provisioning-tutorial/lucidchart1.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Lucidchart**.

9. Granska de användarattribut som synkroniseras från Azure AD till Lucidchart i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Lucidchart för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att Lucidchart-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |emails[type eq "work"].value|Sträng|
   |aktiv|Boolesk|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: organisation|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
   |urn: IETF: params: scim: schemas: tillägg: Lucidchart: 1.0: användare: canEdit|Boolesk|

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Lucidchart**.

11. Granska gruppattributen som synkroniseras från Azure AD till Lucidchart i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Lucidchart för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Lucidchart ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Lucidchart genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

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

* 04/30/2020 – stöd för Enterprise Extension-attributet och det anpassade attributet "CanEdit" för användare har lagts till.
* 06/15/2020 – mjuk borttagning av användare är aktiverat (stöder [aktivt](https://tools.ietf.org/html/rfc7643) attribut).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)