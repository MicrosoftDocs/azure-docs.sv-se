---
title: 'Självstudie: Konfigurera global vidarebefordring identitets synkronisering för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till global synkronisering av relä identitet.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 0c4a3bf0-d0a6-4eab-909b-6cf9f9234e4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: d003a512ebde626b8726dfccc58110e53f1cd467
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180922"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Självstudie: Konfigurera global vidarebefordring identitets synkronisering för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både global synkronisering av relä identitet och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS, etablerar och avetablerar Azure AD automatiskt användare och grupper i global vidarebefordra identitets synkronisering med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i global Relay Identity Sync
> * Ta bort användare i global relä identitets synkronisering när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och global Relay Identity Sync
> * Etablera grupper och grupp medlemskap i global vidarebefordring identitets synkronisering


> [!NOTE]
> Global Relay Identity Sync Provisioning Connector använder en SCIM som inte längre stöds på grund av säkerhets problem. Ansträngningarna är i gång med global relä för att växla till en säkrare Authorization-metod.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör).

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och global Relay Identity Sync](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera global vidarebefordra identitets synkronisering till stöd för etablering med Azure AD

Kontakta din globala Relay Identity Sync-representant för att ta emot klient-URL: en. Det här värdet anges i fältet **klient-URL** på fliken etablering i ditt globala program för relä-id i Azure Portal.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till global Relay Identity Sync från Azure AD Application Gallery

Lägg till global Relay Identity Sync från Azure AD-programgalleriet för att börja hantera etablering till global Relay-synkronisering av identiteter. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>Steg 5. Konfigurera automatisk användar etablering till global vidarebefordra identitets synkronisering 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i global Relay Identity sync-app baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Konfigurera automatisk användar etablering för global vidarebefordra identitets synkronisering i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Global relä identitets synkronisering**.

    ![Länken global Relay Identity Sync i listan program](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **URL: en** för din globala Relay Identity sync-klient. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till global relä identitets synkronisering. Om anslutningen Miss lyckas, se till att ditt globala Relay Identity Sync-konto har administratörs behörighet och kontakta din globala relä representant för att lösa problemet.

    ![Knappen auktorisering](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till global vidarebefordra identitets synkronisering**.

9. Granska de användarattribut som synkroniseras från Azure AD till global vidarebefordra identitets synkronisering i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i global Relay Identity Sync för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du kontrol lera att det globala relä-ID: t för synkronisering stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |aktiv|Boolesk|
   |displayName|Sträng|
   |title|Sträng|
   |preferredLanguage|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |namn. formaterad|Sträng|
   |adresser [Type EQ "Work"]. formaterad|Sträng|
   |adresser [Type EQ "Work"]. streetAddress|Sträng|
   |emails[type eq "work"].value|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |adresser [Type EQ "Work"]. region|Sträng|
   |adresser [Type EQ "Work"]. Postnr|Sträng|
   |adresser [Type EQ "Work"]. land|Sträng|
   |adresser [Type EQ "other"]. formaterad|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |phoneNumbers [Type EQ "fax"]. värde|Sträng|
   |externalId|Sträng|
   |name.honorificPrefix|Sträng|
   |Name. honorificSuffix|Sträng|
   |nickName|Sträng|
   |userType|Sträng|
   |locale|Sträng|
   |timezone|Sträng|
   |e-postmeddelanden [typ EQ "hem"]. värde|Sträng|
   |e-postmeddelanden [Type EQ "Övrigt"]. värde|Sträng|
   |phoneNumbers [Skriv EQ "hem"]. värde|Sträng|
   |phoneNumbers [Type EQ "Övrigt"]. värde|Sträng|
   |phoneNumbers [Type EQ "pager"]. värde|Sträng|
   |adresser [Type EQ "Home"]. streetAddress|Sträng|
   |adresser [Type EQ "Home"]. plats|Sträng|
   |adresser [Type EQ "Home"]. region|Sträng|
   |adresser [Type EQ "Home"]. Postnr|Sträng|
   |adresser [Type EQ "Home"]. land|Sträng|
   |adresser [Type EQ "Home"]. formaterat|Sträng|
   |adresser [Type EQ "other"]. streetAddress|Sträng|
   |adresser [Type EQ "other"]. plats|Sträng|
   |adresser [Type EQ "other"]. region|Sträng|
   |adresser [Type EQ "other"]. Postnr|Sträng|
   |adresser [Type EQ "other"]. land|Sträng|
   |roller [Primary EQ "true"]. Visa|Sträng|
   |roller [Primary EQ "true"]. typ|Sträng|
   |roles[primary eq "True"].value|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: organisation|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: proxyAddresses|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute1|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute2|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute3|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute4|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute5|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute6|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute7|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute8|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute9|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute10|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute11|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute12|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute13|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute14|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: GlobalRelay: 2.0: användare: extensionAttribute15|Sträng|



10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till global vidarebefordra identitets synkronisering**.

11. Granska gruppattributen som synkroniseras från Azure AD till global vidarebefordra identitets synkronisering i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i global Relay Identity Sync för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för global vidarebefordra identitets synkronisering ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till global synkronisering av relä identitet genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

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