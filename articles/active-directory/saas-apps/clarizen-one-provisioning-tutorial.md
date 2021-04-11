---
title: 'Självstudie: Konfigurera Clarizen en för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Clarizen.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: f3a19d3c3bf3e4340bb36fd683453541fa15eb6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650849"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Clarizen en för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Clarizen en och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS etablerar och avetablerar Azure AD automatiskt användare och grupper för att [Clarizen en](https://www.clarizen.com/) med hjälp av Azure AD Provisioning-tjänsten. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program (program vara som en tjänst) med Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funktioner som stöds

> [!div class="checklist"]
> * Skapa användare i Clarizen en.
> * Ta bort användare i Clarizen en när de inte behöver åtkomst längre.
> * Behåll användarattribut synkroniserade mellan Azure AD och Clarizen One.
> * Etablera grupper och grupp medlemskap i Clarizen One.
> * [Enkel inloggning (SSO)](./clarizen-tutorial.md) till Clarizen rekommenderas.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etableringen. Exempel är program administratör, moln program administratör, program ägare eller global administratör.
* Ett användar konto i Clarizen med **Administratörs** [behörigheter](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)för **integrations användare** och lite.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen

1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
1. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Ta reda på vilka data som ska [mappas mellan Azure AD och Clarizen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Clarizen som stöder etablering med Azure AD

1. Välj en av de fyra följande klient webb adresserna enligt din Clarizen-miljö och ett Data Center:
      * US Production Data Center: https://servicesapp2.clarizen.com/scim/v2
      * EU: s produktions Data Center: https://serviceseu1.clarizen.com/scim/v2
      * Data Center för USA, Sandbox: https://servicesapp.clarizentb.com/scim/v2
      * Data Center för begränsat läge för EU: https://serviceseu.clarizentb.com/scim/v2

1. Generera en [API-nyckel](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Det här värdet kommer att anges i rutan **hemlig token** på fliken **etablering** i Clarizen i ett program i Azure Portal.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Clarizen från Azure AD-programgalleriet

Lägg till Clarizen från Azure AD-programgalleriet för att börja hantera etableringen till Clarizen One. Om du tidigare har konfigurerat Clarizen en för enkel inloggning kan du använda samma program. Skapa en separat app när du testar integreringen från början. Mer information om hur du lägger till ett program från galleriet finns i [lägga till ett program i Azure AD-klienten](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget

Med Azure AD Provisioning-tjänsten kan du omfånget som ska tillhandahållas baserat på tilldelning till programmet eller baserat på attribut för användaren eller gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning följer du stegen i [hantera användar tilldelning för en app i Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på användarens eller gruppens attribut använder du ett omfångs filter enligt beskrivningen i [attribut-baserad program etablering med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* När du tilldelar användare och grupper till Clarizen en måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller.
* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering har angetts till tilldelade användare och grupper, kan du behålla kontrollen genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper, kan du ange ett [attribut-baserat omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Steg 5. Konfigurera automatisk användar etablering för att Clarizen en

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper i TestApp baserat på användar-eller grupp tilldelningar i Azure AD.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Konfigurera automatisk användar etablering för Clarizen en i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**.

      ![Skärm bild som visar fönstret företags program.](common/enterprise-applications.png)

1. I listan program väljer du **Clarizen ett**.

      ![Skärm bild som visar Clarizen för en länk i program listan.](common/all-applications.png)

1. Välj fliken **Etablering**.

      ![Skärm bild som visar fliken etablering.](common/provisioning.png)

1. Ange **etablerings läget** till **Automatisk**.

      ![Skärm bild som visar fliken etablering i automatiska alternativ.](common/provisioning-automatic.png)

1. Under avsnittet **admin credentials** kan du mata in din Clarizen med en **klient-URL och en** **hemlig token**. Välj **Testa anslutning** för att se till att Azure AD kan ansluta till Clarizen en. Om anslutningen Miss lyckas kontrollerar du att Clarizen ett konto har administratörs behörighet och försöker igen.

    ![Skärm bild som visar rutan hemlig token.](common/provisioning-testconnection-tenanturltoken.png)

1. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Skärm bild som visar rutan e-postavisering.](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare att Clarizen ett**.

1. Granska de användarattribut som synkroniseras från Azure AD till Clarizen ett i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Clarizen ett för uppdaterings åtgärder. Om du ändrar det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att CLARIZEN ett API stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |displayName|Sträng|
   |aktiv|Boolesk|
   |title|Sträng|
   |emails[type eq "work"].value|Sträng|
   |e-postmeddelanden [typ EQ "hem"]. värde|Sträng|
   |e-postmeddelanden [Type EQ "Övrigt"]. värde|Sträng|
   |preferredLanguage|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |namn. formaterad|Sträng|
   |name.honorificPrefix|Sträng|
   |Name. honorificSuffix|Sträng|
   |adresser [Type EQ "other"]. formaterad|Sträng|
   |adresser [Type EQ "Work"]. formaterad|Sträng|
   |adresser [Type EQ "Work"]. land|Sträng|
   |adresser [Type EQ "Work"]. region|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |adresser [Type EQ "Work"]. Postnr|Sträng|
   |adresser [Type EQ "Work"]. streetAddress|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |phoneNumbers [Type EQ "fax"]. värde|Sträng|
   |phoneNumbers [Skriv EQ "hem"]. värde|Sträng|
   |phoneNumbers [Type EQ "Övrigt"]. värde|Sträng|
   |phoneNumbers [Type EQ "pager"]. värde|Sträng|
   |externalId|Sträng|
   |nickName|Sträng|
   |locale|Sträng|
   |roller [Primary EQ "true". typ]|Sträng|
   |roller [Primary EQ "true". värde]|Sträng|
   |timezone|Sträng|
   |userType|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|

1. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper för att Clarizen ett**.

1. Granska gruppattributen som synkroniseras från Azure AD till Clarizen ett i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Clarizen en för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externalId|Sträng|
      |medlemmar|Referens|

1. Information om hur du konfigurerar omfångs filter finns i kursen i avsnittet  [omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för Clarizen, ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

      ![Skärm bild som visar etablerings statusen växlad på.](common/provisioning-toggle-on.png)

1. Definiera de användare eller grupper som du vill etablera för att Clarizen en genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

      ![Skärm bild som visar etablerings omfånget.](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

      ![Skärm bild som visar hur du sparar etablerings konfigurationen.](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen

När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen.

1. Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som har etablerats eller har misslyckats.
1. Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
1. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Mer information om karantän tillstånd finns i [program etablering i karantän status](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Felsökningstips

När du tilldelar en användare till Clarizen en Galleri-app väljer du bara **användar** rollen. Följande roller är ogiltiga:

* Administratör (admin)
* E-postrapportering
* Extern användare
* Finansiell användare
* Social användare
* Superuser
* Tid & utgifts användare

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)