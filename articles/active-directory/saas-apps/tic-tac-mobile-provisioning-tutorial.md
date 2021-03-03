---
title: 'Självstudie: Konfigurera Tic-Tac Mobile för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Tic-Tac Mobile.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 91ae51b9a2785dbc40c55fa58b26763916e8d16c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644586"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Tic-Tac Mobile för automatisk användar etablering

I den här självstudien beskrivs de steg som du måste utföra i både Tic-Tac Mobile och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras etablerar och avetablerar Azure AD automatiskt användare och grupper i [luffarschack-TAC-mobil](https://www.tictacmobile.com/) med hjälp av Azure AD Provisioning-tjänsten. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program (program vara som en tjänst) med Azure AD](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Funktioner som stöds

> [!div class="checklist"]
> * Skapa användare i Tic-Tac Mobile.
> * Ta bort användare i Tic-Tac Mobile när de inte behöver åtkomst längre.
> * Behåll användarattribut synkroniserade mellan Azure AD och Tic-Tac Mobile.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etableringen. Exempel är program administratör, moln program administratör, program ägare eller global administratör.
* Ett [luffarschack-TAC-mobil](https://www.tictacmobile.com/) konto med en superadministratörs roll.


## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen

1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
1. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Ta reda på vilka data som ska [mappas mellan Azure AD och Tic-Tac Mobile](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Tic-Tac Mobile för att ge stöd för etablering med Azure AD

Kontakta support@tictacmobile.com för att få din **klient-URL** och en **hemlig token**. Du måste ha en Super admin-roll i Tic-Tac Mobile för att ta emot en token. Token anges i rutan **hemlig token** på fliken **etablering** i Tic-Tac mobilt program i Azure Portal.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Tic-Tac mobil från Azure AD-programgalleriet

Lägg till Tic-Tac Mobile från Azure AD-programgalleriet för att börja hantera etablering till Tic-Tac Mobile. Om du tidigare har konfigurerat Tic-Tac Mobile för enkel inloggning kan du använda samma program. Skapa en separat app när du testar integreringen från början. Mer information om hur du lägger till ett program från galleriet finns i [attribut-baserad program etablering med omfångs filter](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget

Med Azure AD Provisioning-tjänsten kan du omfånget som ska tillhandahållas baserat på tilldelning till programmet eller baserat på attribut för användaren eller gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning följer du stegen i [hantera användar tilldelning för en app i Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på användarens eller gruppens attribut använder du ett omfångs filter enligt beskrivningen i [attribut-baserad program etablering med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* När du tilldelar användare och grupper till Tic-Tac mobil måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller.
* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering har angetts till tilldelade användare och grupper, kan du behålla kontrollen genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper, kan du ange ett [attribut-baserat omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Steg 5. Konfigurera automatisk användar etablering till Tic-Tac Mobile

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper i TestApp baserat på användar-eller grupp tilldelningar i Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Konfigurera automatisk användar etablering för Tic-Tac Mobile i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**.

    ![Skärm bild som visar fönstret företags program.](common/enterprise-applications.png)

1. I listan program väljer du **luffarschack-Tac Mobile**.

    ![Skärm bild som visar Tic-Tac Mobile-länken i program listan.](common/all-applications.png)

1. Välj fliken **Etablering**.

    ![Skärm bild som visar fliken etablering.](common/provisioning.png)

1. Ange **etablerings läget** till **Automatisk**.

    ![Skärm bild som visar fliken etablering i automatiska alternativ.](common/provisioning-automatic.png)

1. Under avsnittet **admin credentials** kan du mata in din Tic-Tac mobila **klient-URL** och **hemlig token**. Välj **Testa anslutning** för att se till att Azure AD kan ansluta till Tic-Tac Mobile. Om anslutningen Miss lyckas kontrollerar du att Tic-Tac Mobile-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar rutan hemlig token.](common/provisioning-testconnection-tenanturltoken.png)

1. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Skärm bild som visar rutan e-postavisering.](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Tic-Tac Mobile**.

1. Granska de användarattribut som synkroniseras från Azure AD till Tic-Tac Mobile i avsnittet **attribut-mappning** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i Tic-Tac Mobile för uppdaterings åtgärder. Om du ändrar det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att Tic-Tac Mobile API stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |externalId|Sträng|
   |title|Sträng|
   |emails[type eq "work"].value|Sträng|
   |preferredLanguage|Sträng|
   |externalId|Sträng|
   |userType|Sträng|
   |locale|Sträng|
   |timezone|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: organisation|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|

1. Information om hur du konfigurerar omfångs filter finns i kursen i avsnittet [omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för Tic-Tac Mobile ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Skärm bild som visar etablerings statusen växlad på.](common/provisioning-toggle-on.png)

1. Definiera de användare eller grupper som du vill etablera Tic-Tac mobil genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Skärm bild som visar etablerings omfånget.](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Skärm bild som visar hur du sparar etablerings konfigurationen.](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen

När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen.

1. Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som har etablerats eller har misslyckats.
1. Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
1. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Mer information om karantän tillstånd finns i [program etablering i karantän status](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)