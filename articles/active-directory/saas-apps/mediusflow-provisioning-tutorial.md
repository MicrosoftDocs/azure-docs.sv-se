---
title: 'Självstudie: Konfigurera MediusFlow för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: a49258208f7a5945ac71c8f17db56fccfdcd6515
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662008"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Självstudie: Konfigurera MediusFlow för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både MediusFlow och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [MediusFlow](https://www.mediusflow.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i MediusFlow
> * Ta bort användare i MediusFlow när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och MediusFlow
> * Etablera grupper och grupp medlemskap i MediusFlow
> * Enkel inloggning till MediusFlow (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En aktiv MediusFlow-prenumeration med en kvalitets säkrings-eller produktions klient.
* Ett användar konto i MediusFlow med administratörs behörighet för att kunna utföra konfigurationen i MediusFlow.
* Företag som lagts till i MediusFlow-klienten där användarna ska tillhandahållas.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och MediusFlow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera MediusFlow för att ge stöd för etablering med Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Aktivera Microsoft 365-appen i MediusFlow
Börja med att aktivera åtkomst till Azure AD-inloggningen och Azure AD-konfigurationsverktyget i MediusFlow genom att utföra följande steg:

#### <a name="user-login"></a>Användar inloggning
Om du vill aktivera inloggnings flödet till Microsoft 365/Azure AD hänvisar du till [den] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) artikeln.

#### <a name="user-transfer-configuration"></a>Konfiguration av användar överföring
Om du vill aktivera konfigurations portalen för användare för etablering från Azure AD kan du läsa [den här](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) artikeln.

#### <a name="configure-user-provisioning"></a>Konfigurera användarförsörjning

1.  Logga in på [MediusFlow-administratörskonsolen](https://office365.cloudapp.mediusflow.com/) genom att ange klient-ID: t.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Skärm bild av MediusFlow-administratörskonsolen. Rutan MediusFlow-klient namn och knappen autentisera är markerad i det första integrerings steget." border="false":::

2. Kontrol lera anslutningen till MediusFlow.

    ![Verifiera](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Ange Azure AD-klient-ID.

    ![Ange klient-ID](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Du kan läsa mer i [vanliga frågor och svar](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) om hur du hittar det.

4. Spara konfigurationen.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Skärm bild av MediusFlow-administratörskonsolen som visar det fjärde integrerings steget. Knappen Spara konfiguration är markerad." border="false":::

5. Välj användar etablering och klicka på **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Skärm bild av MediusFlow-administratörskonsolen som visar det femte integrerings steget. Knapparna Använd användar etablering och OK är markerade." border="false":::

6. Klicka på **generera hemlig nyckel**. Kopiera och spara det här värdet. Det här värdet anges i fältet **hemlig token** på fliken **etablering** i ditt MediusFLow-program i Azure Portal.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Skärm bild av fliken Konfiguration av användar etablering i MediusFlow-administratörskonsolen. Knapparna generera hemlig nyckel och kopiera markeras." border="false":::

7. Klicka på **OK**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Skärm bild av MediusFlow-administratörskonsolen med ett meddelande om att användarna klickar på OK för att skapa en ny hemlig nyckel. Knappen OK är markerad." border="false":::

8. För att användarna ska kunna importeras med en fördefinierad uppsättning roller, företag och andra allmänna konfigurationer i MediusFlow måste du konfigurera det först. Börja med att lägga till konfigurationen genom att klicka på **Lägg till ny konfiguration**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Skärm bild av fliken Konfiguration av användar etablering i MediusFlow-administratörskonsolen. Knappen Lägg till ny konfiguration är markerad." border="false":::

9. Ange standard inställningarna för användarna. I den här vyn är det möjligt att ange standardattributet. Om standardinställningarna är OK räcker det att bara ge ett giltigt företags namn. Eftersom dessa konfigurations inställningar hämtas från MediusFlow måste de konfigureras först. Mer information finns i avsnittet **krav** i den här artikeln.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Skärm bild av MediusFlow Lägg till nytt konfigurations fönster. Många inställningar visas, inklusive nationella inställningar, ett filter och användar roller." border="false":::

10. Spara användar konfigurationen genom att klicka på **Spara** .

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Skärm bild av fliken Konfiguration av användar etablering i MediusFlow-administratörskonsolen. Knappen Spara är markerad." border="false":::

11. Hämta länken för användar etablering genom att klicka på **Kopiera scim länk**. Kopiera och spara det här värdet. Det här värdet anges i fältet **klient-URL** på fliken **etablering** i MediusFLow-programmet i Azure Portal.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Skärm bild av fliken Konfiguration av användar etablering i MediusFlow-administratörskonsolen. Länk knappen Kopiera S C I M är markerad." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till MediusFlow från Azure AD-programgalleriet

Lägg till MediusFlow från Azure AD-programgalleriet för att börja hantera etablering till MediusFlow. Om du tidigare har konfigurerat MediusFlow för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till MediusFlow måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du kontrol lera det genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Steg 5. Konfigurera automatisk användar etablering till MediusFlow 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Konfigurera automatisk användar etablering för MediusFlow i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **MediusFlow**.

    ![MediusFlow-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du mata in klient webb adressen som hämtades tidigare i **klient-URL: en**. Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till MediusFlow. Om anslutningen Miss lyckas kontrollerar du att MediusFlow-kontot har administratörs behörighet och försöker igen.

      ![Skärm bild som visar dialog rutan admin-autentiseringsuppgifter där du kan ange din klient U R L och hemlig token.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till MediusFlow**.

9. Granska de användarattribut som synkroniseras från Azure AD till MediusFlow i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Mediusflow för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att MediusFlow-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |emails[type eq "work"].value|Sträng|
   |Name. displayName|Sträng|
   |aktiv|Boolesk|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |namn. formaterad|Sträng|
   |externalId|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
   |urn: IETF: params: scim: schemas: tillägg: Medius: 2.0: användare: configurationFilter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Medius: 2.0: användare: identityProvider|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Medius: 2.0: användare: nameIdentifier|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Medius: 2.0: användare: customFieldText1|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Medius: 2.0: användare: customFieldText2|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Medius: 2.0: användare: customFieldText3|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Medius: 2.0: användare: customFieldText4|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Medius: 2.0: användare: customFieldText5|Sträng|


10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till MediusFlow**.

11. Granska gruppattributen som synkroniseras från Azure AD till MediusFlow i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Mediusflow för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    | Attribut | Typ |
    |--|--|
    | displayName | Sträng |
    | externalID | Sträng |
    | medlemmar | Referens |

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för MediusFlow ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till MediusFlow genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

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

* 01/21/2021 – anpassade tilläggs-attribut **configurationFilter**, **identityProvider**, **nameIdentifier**, **customFieldText1**, **customFieldText2**, **customFieldText3**, **customFieldText3** och **customFieldText5** har lagts till.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)