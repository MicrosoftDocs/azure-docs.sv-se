---
title: 'Självstudie: Konfigurera OpenText Directory-tjänster för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till OpenText Directory Services.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 2f31eddab1070d073d3fd5a4761dad597e42a2e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181910"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Självstudie: Konfigurera OpenText Directory-tjänster för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både OpenText Directory-tjänster och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras etablerar och avetablerar Azure AD automatiskt användare och grupper i OpenText Directory-tjänster med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i OpenText Directory-tjänster
> * Ta bort användare i OpenText Directory Services när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och OpenText Directory Services
> * Etablera grupper och grupp medlemskap i OpenText Directory-tjänster
> * [Enkel inloggning](./opentext-directory-services-tutorial.md) till OpenText Directory-tjänster (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En OTDS-installation som är tillgänglig för Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och OpenText Directory Services](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera OpenText Directory-tjänster så att de stöder etablering med Azure AD

> [!NOTE]
> Stegen nedan gäller för en OpenText Directory Services-installation. De gäller inte för OpenText CoreShare eller OpenText OT2-klienter.

1. Skapa en dedikerad konfidentiell **OAuth-klient**.
2. Ange **livs längd för token** för lång åtkomst.

      ![Livs längd för åtkomsttoken](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Ange inga omdirigerings-URL: er. De är inte obligatoriska. 
4. OTDS kommer att generera och visa **klient hemligheten**. Spara **klient-ID** och **klient hemlighet** på en säker plats.

      ![Client Secret (Klienthemlighet)](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Skapa en partition för de användare och grupper som ska synkroniseras från Azure AD.

      ![Sidan partition](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Bevilja administrativa rättigheter till OAuth-klienten du skapade på den partition som du ska använda för Azure AD-användare och-grupper som synkroniseras.    
      * Åtgärder för partitions-> – > redigera administratörer

      ![Sidan administratör](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. En hemlig token måste hämtas och konfigureras i Azure AD. Alla HTTP-klientprogram kan användas för detta. Nedan visas steg som du kan hämta med Swagger-API-programmet som ingår i OTDS.
      * I en webbläsare går du till {OTDS URL}/otdsws/OAuth2
      * Gå till/token och klicka på Lås ikonen längst upp till höger. Ange OAuth-klient-ID och hemlighet som hämtades tidigare som användar namn och lösen ord. Klicka på Auktorisera.

      ![Knappen auktorisering](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Välj **client_credentials** för grant_type och klicka på **Kör**.

      ![Kör buton](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. Åtkomsttoken i svaret ska användas i fältet **hemligt token** i Azure AD.

      ![Åtkomsttoken](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till OpenText Directory-tjänster från Azure AD-programgalleriet

Lägg till OpenText Directory-tjänster från Azure AD-programgalleriet för att börja hantera etablering till OpenText Directory Services. Om du tidigare har konfigurerat OpenText Directory Services för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till OpenText Directory Services måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Steg 5. Konfigurera automatisk användar etablering till OpenText Directory-tjänster 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Konfigurera automatisk användar etablering för OpenText Directory-tjänster i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **OpenText Directory Services**.

    ![Länken OpenText Directory Services i listan program](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials (autentiseringsuppgifter** ) skriver du klient-URL för OpenText Directory Services
   * Icke-specifc klient-URL: {OTDS URL}/scim/{partitionName}
   * Viss klient-URL: {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. Ange den hemliga token som hämtades från steg 2. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till OpenText Directory-tjänster. Om anslutningen Miss lyckas ser du till att ditt OpenText Directory Services-konto har administratörs behörighet och försöker igen.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till OpenText Directory Services**.

9. Granska de användarattribut som synkroniseras från Azure AD till OpenText Directory Services i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i OpenText Directory Services för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att OpenText Directory Services API stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |aktiv|Boolesk|
   |displayName|Sträng|
   |title|Sträng|
   |emails[type eq "work"].value|Sträng|
   |preferredLanguage|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |namn. formaterad|Sträng|
   |adresser [Type EQ "Work"]. formaterad|Sträng|
   |adresser [Type EQ "Work"]. streetAddress|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |adresser [Type EQ "Work"]. region|Sträng|
   |adresser [Type EQ "Work"]. Postnr|Sträng|
   |adresser [Type EQ "Work"]. land|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |phoneNumbers [Type EQ "fax"]. värde|Sträng|
   |externalId|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens| 

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper för att OpenText Directory Services**.

11. Granska gruppattributen som synkroniseras från Azure AD till OpenText Directory Services i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i OpenText Directory Services för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externalId|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för OpenText Directory-tjänster ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till OpenText Directory-tjänster genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

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