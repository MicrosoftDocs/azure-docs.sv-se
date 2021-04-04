---
title: 'Självstudie: Konfigurera 15Five för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till 15Five.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 00f713e8d319d7ad8dcea014429c57d7fba40541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181228"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Självstudie: Konfigurera 15Five för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i 15Five och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till [15Five](https://www.15five.com/pricing/). Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory.

> [!NOTE]
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i 15Five
> * Ta bort användare i 15Five när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och 15Five
> * Etablera grupper och grupp medlemskap i 15Five
> * [Enkel inloggning](./15five-tutorial.md) till 15Five (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) .
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör).
* [En 15Five-klient](https://www.15five.com/pricing/).
* Ett användar konto i 15Five med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och 15Five](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera 15Five för att ge stöd för etablering med Azure AD

Innan du konfigurerar 15Five för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på 15Five.

1. Logga in på din [15Five-administratörs konsol](https://my.15five.com/). Navigera till **funktioner > integreringar**.

    :::image type="content" source="media/15five-provisioning-tutorial/integration.png" alt-text="Skärm bild av 15Five-administratörskonsolen. Integreringarna visas under funktioner i en meny och båda funktionerna och Integreringarna är markerade." border="false":::

2.  Klicka på **SCIM 2,0**.

    :::image type="content" source="media/15five-provisioning-tutorial/image00.png" alt-text="Skärm bild av sidan integreringar i 15Five-administratörskonsolen. Under verktyg är S C I M 2,0 markerad." border="false":::

3.  Navigera till **scim-integration > generera OAuth-token**.

    :::image type="content" source="media/15five-provisioning-tutorial/image02.png" alt-text="Skärm bild av sidan S C i M-integration i 15Five-administratörskonsolen. Generera OAuth-token har marker ATS." border="false":::

4.  Kopiera värdena för **SCIM 2,0-bas-URL** och **åtkomsttoken**. Det här värdet anges i fältet **klient-URL** och **hemligt token** på fliken etablering i 15Five-programmet i Azure Portal.
    
    :::image type="content" source="media/15five-provisioning-tutorial/image03.png" alt-text="Skärm bild av sidan S C I M-integration. I tabellen token är värdena bredvid S C I M 2,0 Base U R L och åtkomsttoken markerade." border="false":::

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till 15Five från Azure AD-programgalleriet

Lägg till 15Five från Azure AD-programgalleriet för att börja hantera etablering till 15Five. Om du tidigare har konfigurerat 15Five för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till 15Five måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>Steg 5. Konfigurera automatisk användar etablering till 15Five 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i 15Five baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Konfigurera automatisk användar etablering för 15Five i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **15Five**.

    ![Länken för 15Five i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5.  Under avsnittet admin credentials måste du skriva in **SCIM 2,0-bas-URL: en och de åtkomsttoken** som hämtades tidigare i fälten för **klient-URL** och **hemlig token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till 15Five. Om anslutningen Miss lyckas kontrollerar du att 15Five-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till 15Five**.

9. Granska de användarattribut som synkroniseras från Azure AD till 15Five i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i 15Five för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.


   |Attribut|Typ|
   |---|---|
   |aktiv|Boolesk|
   |title|Sträng|
   |emails[type eq "work"].value|Sträng|
   |userName|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |externalId|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: 15Five: 2.0: användare: plats|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: 15Five: 2.0: användare: StartDate|Sträng|

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till 15Five**.

11. Granska gruppattributen som synkroniseras från Azure AD till 15Five i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i 15Five för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |externalId|Sträng|
      |displayName|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för 15Five ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till 15Five genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  
    
## <a name="connector-limitations"></a>Kopplings begränsningar

* 15Five stöder inte mjuka borttagningar för användare.

## <a name="change-log"></a>Ändringslogg

* 06/16/2020 – stöd för företags tilläggets attribut "Manager" och anpassade attribut "location" och "start datum" för användare har lagts till.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md).