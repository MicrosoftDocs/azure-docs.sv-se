---
title: 'Självstudie: Konfigurera AlertMedia för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till AlertMedia.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: a5df0dd7-05a3-4744-9d51-ec33e89a934f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: Zhchia
ms.openlocfilehash: b15b20ee8e643dc260578d17913810aaf68cc929
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181109"
---
# <a name="tutorial-configure-alertmedia-for-automatic-user-provisioning"></a>Självstudie: Konfigurera AlertMedia för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både AlertMedia och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [AlertMedia](https://www.alertmedia.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i AlertMedia
> * Ta bort användare i AlertMedia när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och AlertMedia
> * Etablera grupper och grupp medlemskap i AlertMedia
> * [Enkel inloggning](./alertmedia-tutorial.md) till AlertMedia (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En [Azure AD-klient](../develop/quickstart-create-new-tenant.md).
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En [AlertMedia-klient](https://dashboard.alertmedia.com/#/login).
* Ett användar konto i AlertMedia med administratörs behörighet för att konfigurera en API-integration.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och AlertMedia](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-alertmedia-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera AlertMedia för att ge stöd för etablering med Azure AD

1. Logga in på ditt AlertMedia-konto. Navigera till **företags >-API**.
2. Klicka på **Lägg till ny**.
3. Välj att ge **API-integration** ett namn som hjälper dig att enkelt identifiera var nycklarna används.
4. Välj den administratör som du vill koppla integrationen till.
5. Klicka på knappen **generera nycklar** och **Spara** .
6. Kopiera och spara **klientens token** från din integrering. Detta används som **hemlig token** på fliken etablering i ditt AlertMedia-program i Azure Portal.


## <a name="step-3-add-alertmedia-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till AlertMedia från Azure AD-programgalleriet

Lägg till AlertMedia från Azure AD-programgalleriet för att börja hantera etablering till AlertMedia. Om du tidigare har konfigurerat AlertMedia för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till AlertMedia måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-alertmedia"></a>Steg 5. Konfigurera automatisk användar etablering till AlertMedia 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-alertmedia-in-azure-ad"></a>Konfigurera automatisk användar etablering för AlertMedia i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **AlertMedia**.

    ![AlertMedia-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du mata in din AlertMedia- **klient-URL** som en av följande.
      * (ingen anpassad domän) `https://dashboard.alertmedia.com/api/scim/v3`

      * (anpassad domän) `https://subdomain.alertmedia.com/api/scim/v3`

      Mata in den **hemliga token** som hämtades tidigare i steg 2. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till AlertMedia. Om anslutningen Miss lyckas kontrollerar du att AlertMedia-kontot har administratörs behörighet och försöker igen.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till AlertMedia**.

9. Granska de användarattribut som synkroniseras från Azure AD till AlertMedia i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i AlertMedia för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att AlertMedia-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |aktiv|Boolesk|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: first_name|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: last_name|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: e-post|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: email2|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: email3|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: title|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: mobile_phone|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: mobile_phone_post_dial|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: mobile_phone2|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: mobile_phone2_post_dial|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: mobile_phone3|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: mobile_phone3_post_dial|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: home_phone|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: home_phone_post_dial|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: office_phone|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: office_phone_post_dial|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: adress|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: Address2|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: stad|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: User: State|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: land|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: Postummer|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: anteckningar|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: customer_user_id|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: alertmedia: 2.0: CustomAttribute: användare: user_type|Sträng|

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till AlertMedia**.

11. Granska gruppattributen som synkroniseras från Azure AD till AlertMedia i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i AlertMedia för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för AlertMedia ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till AlertMediaAlertMedia genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

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