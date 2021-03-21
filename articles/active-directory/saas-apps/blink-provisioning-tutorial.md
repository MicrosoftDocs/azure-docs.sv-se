---
title: 'Självstudie: Konfigurera Blink för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till att blinka.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e67c3157f1d354fe79b2e4bb4b6e822aaf3e7506
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731399"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Blink för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i blinkande och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare till att blinka.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En blinkande klient](https://joinblink.com/pricing)
* Ett användar konto i blinkar med administratörs behörighet.

## <a name="assigning-users-to-blink"></a>Tilldela användare till blinkande

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupp medlemmar som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupp medlemmar i Azure AD som behöver komma åt att blinka. När du har bestämt dig kan du tilldela dessa användare och/eller grupper så att de blinkar genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>Viktiga tips för att tilldela användare till blinkande

* Vi rekommenderar att en enda Azure AD-användare är tilldelad att blinka för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare att blinka måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-blink-for-provisioning"></a>Konfigurera blinkning för etablering

1. Logga ett [support ärende](https://support.joinblink.com) eller skicka ett e-postmeddelande till **blinkande** support@joinblink.com för att begära en scim-token.

2.  Kopiera **scim-autentiseringstoken**. Det här värdet anges i fältet Hemlig token på fliken etablering i programmet blinkar i Azure Portal.

## <a name="add-blink-from-the-gallery"></a>Lägg till Blink från galleriet

Innan du konfigurerar blinka för automatisk användar etablering med Azure AD måste du lägga till Blink från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Blink från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **blinkar**, väljer **blinkar** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Blinkar i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>Konfigurerar automatisk användar etablering till blinkande 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare i blinkar baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för att blinka, genom att följa anvisningarna i [själv studie kursen om att blinka enkla inloggningar](./blink-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om de här två funktionerna är på varandra

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>Konfigurera automatisk användar etablering för blinkar i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **blinka**.

    ![Länken blinkar i listan program](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://api.joinblink.com/scim` i **klient-URL**. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till blinka. Om anslutningen Miss lyckas ser du till att ditt blinkande konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare att blinka**.

    ![Blinkande användar mappningar](media/blink-provisioning-tutorial/User_mappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD och blinkar i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Blink för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |aktiv|Boolesk|
   |title|Sträng|
   |emails[type eq "work"].value|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |externalId|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Referens|
   |urn: IETF: params: scim: schemas: tillägg: blinka: 2.0: användare: företag|Sträng|
   urn: IETF: params: scim: schemas: tillägg: blinka: 2.0: användare: Beskrivning|Sträng|
   urn: IETF: params: scim: schemas: tillägg: blinka: 2.0: användare: plats|Sträng|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Blink ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare som du vill etablera för att blinka genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare som definierats i **området** **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Blink.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

* Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
* Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  


## <a name="change-log"></a>Ändringslogg

* 01/14/2021 – anpassade tilläggsfiler attribut **företag**, **Beskrivning** och **plats** har lagts till.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)