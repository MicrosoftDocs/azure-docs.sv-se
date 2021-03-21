---
title: 'Självstudie: Konfigurera getAbstract för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure Active Directory till getAbstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616153"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Självstudie: Konfigurera getAbstract för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både getAbstract och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras etablerar och avetablerar Azure AD automatiskt användare och grupper i [getAbstract](https://www.getabstract.com) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program (program vara som en tjänst) med Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funktioner som stöds

> [!div class="checklist"]
> * Skapa användare i getAbstract.
> * Ta bort användare i getAbstract när de inte behöver åtkomst längre.
> * Behåll användarattribut synkroniserade mellan Azure AD och getAbstract.
> * Etablera grupper och grupp medlemskap i getAbstract.
> * Aktivera [enkel inloggning (SSO)](getabstract-tutorial.md) till getAbstract (rekommenderas).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etableringen. Exempel är program administratör, moln program administratör, program ägare eller global administratör.
* En getAbstract-klient (getAbstract Corporate License).
* SSO aktiverat på Azure AD-klienten och getAbstract-klienten.
* Godkännande och system för SCIM (Cross-Domain Identity Management) som aktive ras för getAbstract. (Skicka e-post till b2b.itsupport@getabstract.com .)

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen

1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
1. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Ta reda på vilka data som ska [mappas mellan Azure AD och getAbstract](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera getAbstract för att ge stöd för etablering med Azure AD

1. Logga in på getAbstract.
1. Välj ikonen Inställningar i det övre högra hörnet och välj **mitt centrala administratörs** alternativ.

    ![Skärm bild som visar getAbstract My Central admin.](media/getabstract-provisioning-tutorial/my-account.png)

1. Leta upp och välj alternativet **scim admin** .

    ![Skärm bild som visar getAbstract SCIM-administratören.](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Välj **gå** till.

    ![Skärm bild som visar getAbstract SCIM-klient-ID.](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Välj **generera ny token**.

    ![Skärm bild som visar getAbstract SCIM-token 1.](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Om du är säker väljer du **generera ny token**. Annars väljer du **Avbryt**.

    ![Skärm bild som visar getAbstract SCIM-token 2.](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Välj antingen kopiera till Urklipp-ikonen eller Välj hela token och kopiera den. Notera också att URL: en för klient organisationen/basen är `https://www.getabstract.com/api/scim/v2` . Dessa värden anges i rutorna **hemlig token** och **klient webb adress** på fliken **etablering** i getAbstract-programmet i Azure Portal.

    ![Skärm bild som visar getAbstract SCIM-token 3.](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till getAbstract från Azure AD-programgalleriet

Lägg till getAbstract från Azure AD-programgalleriet för att börja hantera etablering till getAbstract. Om du tidigare har konfigurerat getAbstract för SSO kan du använda samma program. Vi rekommenderar att du skapar en separat app när du testar integrationen från början. Mer information om hur du lägger till ett program från galleriet finns i [den här snabb](../manage-apps/add-application-portal.md)starten.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget

Du kan använda Azure AD Provisioning-tjänsten för det omfång som ska tillhandahållas baserat på tilldelning till programmet eller baserat på attribut för användaren eller gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska etableras baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen i [etablera appar med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* När du tilldelar användare och grupper till getAbstract måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller.

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du kontrol lera det här alternativet genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper, kan du ange ett [attribut-baserat omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Steg 5. Konfigurera automatisk användar etablering till getAbstract

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper i TestApp baserat på användar-eller grupp tilldelningar i Azure AD.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Konfigurera automatisk användar etablering för getAbstract i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**.

    ![Skärm bild som visar fönstret företags program.](common/enterprise-applications.png)

1. I listan med program väljer du **getAbstract**.

    ![Skärm bild som visar getAbstract-länken i listan över program.](common/all-applications.png)

1. Välj fliken **Etablering**.

    ![Skärm bild som visar fliken etablering.](common/provisioning.png)

1. Ange **etablerings läget** till **Automatisk**.

    ![Skärm bild som visar etablerings läget inställt på automatiskt.](common/provisioning-automatic.png)

1. I avsnittet **admin credentials** anger du din GetAbstract- **klient-URL** och information om **hemliga token** . Välj **Testa anslutning** för att se till att Azure AD kan ansluta till getAbstract. Om anslutningen Miss lyckas kontrollerar du att getAbstract-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar rutorna för klient-URL och hemlig token.](common/provisioning-testconnection-tenanturltoken.png)

1. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Skärm bild som visar rutan e-postavisering.](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. I avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till getAbstract**.

1. Granska de användarattribut som synkroniseras från Azure AD till getAbstract i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i getAbstract för uppdaterings åtgärder. Om du ändrar det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att getAbstract-API: et stöder filtrering av användare baserat på det attributet. Välj **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |aktiv|Boolesk|
   |emails[type eq "work"].value|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |externalId|Sträng|
   |preferredLanguage|Sträng|

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till getAbstract**.

1. Granska gruppattributen som synkroniseras från Azure AD till getAbstract i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i getAbstract för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    |Attribut|Typ|Stöds för filtrering|
    |---|---|---|
    |displayName|Sträng|&check;|
    |externalId|Sträng|
    |medlemmar|Referens|

1. Information om hur du konfigurerar omfångs filter finns i anvisningarna i [artikeln omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för getAbstract ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Skärm bild som visar etablerings statusen växlad på.](common/provisioning-toggle-on.png)

1. Definiera de användare eller grupper som du vill etablera till getAbstract genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Skärm bild som visar etablerings omfånget.](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Skärm bild som visar knappen Spara.](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket sker ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen

När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som etablerades lyckades eller inte.
* Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Mer information om karantäns tillstånd finns i [Application etablerings status för karantän](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
