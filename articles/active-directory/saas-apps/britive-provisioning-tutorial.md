---
title: 'Självstudie: Konfigurera Britive för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Britive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225823"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Britive för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Britive och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [Britive](https://www.britive.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Britive
> * Ta bort användare i Britive när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Britive
> * Etablera grupper och grupp medlemskap i Britive
> * [Enkel inloggning](britive-tutorial.md) till Britive (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En [Britive](https://www.britive.com/) -klient.
* Ett användar konto i Britive med administratörs behörighet.


## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
1. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Ta reda på vilka data som ska [mappas mellan Azure AD och Britive](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Britive för att ge stöd för etablering med Azure AD

Programmet måste konfigureras manuellt med hjälp av de steg som anges nedan:
1. Logga in på Britive-program med administratörs behörighet
1. Klicka på **admin->User administration – >identitets leverantörer**
1. Klicka på **Lägg till identitets leverantör**. Ange namn och beskrivning. Klicka på knappen Lägg till identitetsprovider.

    ![Identitetsprovider](media/britive-provisioning-tutorial/identity.png)

1. En konfigurations sida som liknar den som visas nedan kommer att visas.

    ![Konfigurations sida](media/britive-provisioning-tutorial/configuration.png)

1. Klicka på fliken **scim** . Ändra SCIM-providern från generisk till Azure och spara ändringarna. Kopiera SCIM-URL: en och anteckna den. Värdena anges i rutorna för **klient-URL** på fliken etablering i Britive-programmet i Azure Portal.

    ![Sidan SCIM](media/britive-provisioning-tutorial/scim.png)

1. Klicka på **skapa token**. Välj giltigheten för token efter behov och klicka på knappen skapa token.

    ![Skapa token](media/britive-provisioning-tutorial/create-token.png)

1. Kopiera den genererade token och anteckna den. Klicka på OK. Observera att användaren inte kan se token igen. Klicka på Re-Create knappen om du vill generera en ny token vid behov. De här värdena anges i rutorna **hemlig token** och klient webb adress på fliken etablering i getAbstract-programmet i Azure Portal.

    ![Kopiera token](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Britive från Azure AD-programgalleriet

Lägg till Britive från Azure AD-programgalleriet för att börja hantera etablering till Britive. Om du tidigare har konfigurerat Britive för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Britive måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>Steg 5. Konfigurera automatisk användar etablering till Britive 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Britive baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Konfigurera automatisk användar etablering för Britive i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. I listan program väljer du **Britive**.

    ![Britive-länken i program listan](common/all-applications.png)

1. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

1. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

1. Under avsnittet **admin credentials** , skriver du in din Britive-klient-URL och en hemlig token. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Britive. Om anslutningen Miss lyckas kontrollerar du att Britive-kontot har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Britive**.

1. Granska de användarattribut som synkroniseras från Azure AD till Britive i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Britive för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att Britive-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;
   |aktiv|Boolesk|
   |displayName|Sträng|
   |title|Sträng|
   |externalId|Sträng|
   |preferredLanguage|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |nickName|Sträng|
   |userType|Sträng|
   |locale|Sträng|
   |timezone|Sträng|
   |e-postmeddelanden [typ EQ "hem"]. värde|Sträng|
   |e-postmeddelanden [Type EQ "Övrigt"]. värde|Sträng|
   |emails[type eq "work"].value|Sträng|
   |phoneNumbers [Skriv EQ "hem"]. värde|Sträng|
   |phoneNumbers [Type EQ "Övrigt"]. värde|Sträng|
   |phoneNumbers [Type EQ "pager"]. värde|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |phoneNumbers [Type EQ "fax"]. värde|Sträng|
   |adresser [Type EQ "Work"]. formaterad|Sträng|
   |adresser [Type EQ "Work"]. streetAddress|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |adresser [Type EQ "Work"]. region|Sträng|
   |adresser [Type EQ "Work"]. Postnr|Sträng|
   |adresser [Type EQ "Work"]. land|Sträng|
   |adresser [Type EQ "Home"]. formaterat|Sträng|
   |adresser [Type EQ "Home"]. streetAddress|Sträng|
   |adresser [Type EQ "Home"]. plats|Sträng|
   |adresser [Type EQ "Home"]. region|Sträng|
   |adresser [Type EQ "Home"]. Postnr|Sträng|
   |adresser [Type EQ "Home"]. land|Sträng|
   |adresser [Type EQ "other"]. formaterad|Sträng|
   |adresser [Type EQ "other"]. streetAddress|Sträng|
   |adresser [Type EQ "other"]. plats|Sträng|
   |adresser [Type EQ "other"]. region|Sträng|
   |adresser [Type EQ "other"]. Postnr|Sträng|
   |adresser [Type EQ "other"]. land|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: organisation|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|


1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Britive**.

1. Granska gruppattributen som synkroniseras från Azure AD till Britive i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Britive för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;
      |externalId|Sträng|
      |medlemmar|Referens|

1. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för Britive ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

1. Definiera de användare och/eller grupper som du vill etablera till Britive genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

1. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

* Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
* Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)