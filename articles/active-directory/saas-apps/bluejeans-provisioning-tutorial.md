---
title: 'Självstudie: Konfigurera BlueJeans för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 58cd69ebe97d9d0965d7e648b0ded012ac71cd0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646137"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Självstudie: Konfigurera BlueJeans för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både BlueJeans och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare för [BlueJeans](https://www.bluejeans.com/pricing) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i BlueJeans
> * Ta bort användare i BlueJeans när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och BlueJeans
> * [Enkel inloggning](./bluejeans-tutorial.md) till BlueJeans (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En BlueJeans-klient med [min företags](https://www.bluejeans.com/pricing) plan eller bättre aktive rad.
* Ett användar konto i BlueJeans med administratörs behörighet.
* SCIM-etablering har Aktiver ATS i BlueJeans Enterprise.

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av [BlueJeans-API: et](https://BlueJeans.github.io/developer), som är tillgängligt för BlueJeans-team i standard planen eller bättre.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och BlueJeans](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera BlueJeans för att ge stöd för etablering med Azure AD

1. Logga in på BlueJeans-administratörskonsolen. Gå till grupp inställningar > säkerhet.
2. Välj **enkel inloggning** och **Konfigurera nu**.

    ![nu](./media/bluejeans-provisioning-tutorial/configure.png)

3. I fönstret **etablera & integrering** väljer du **skapa och hantera användar konton via IDP** och klickar på **generera token**.

    ![generera](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Kopiera och spara token. 
5. BlueJeans-klientens URL är `https://api.bluejeans.com/v2/scim` . **Klient-URL:** en och den **hemliga token** från föregående steg kommer att anges på fliken etablering i BlueJeans-programmet i Azure Portal.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till BlueJeans från Azure AD-programgalleriet

Lägg till BlueJeans från Azure AD-programgalleriet för att börja hantera etablering till BlueJeans. Om du tidigare har konfigurerat BlueJeans för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på användarens attribut. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på användarens attribut kan du använda ett omfångs filter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare till BlueJeans måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare kan du kontrol lera detta genom att tilldela en eller två användare till appen. När omfång är inställt på alla användare kan du ange ett [omfångs filter för attribut](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>Steg 5. Konfigurera automatisk användar etablering till BlueJeans

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare i TestApp baserat på användar tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurera automatisk användar etablering för BlueJeans i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **BlueJeans**.

    ![Länken BlueJeans i listan med program](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , skriver du in din BlueJeans-klient-URL och den hemliga token som hämtades i steg 2. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till BlueJeans. Om anslutningen Miss lyckas kontrollerar du att BlueJeans-kontot har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)


6. I fältet **e-** postadress för avisering anger du e-postadressen till den person som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till BlueJeans**.

9. Granska de användarattribut som synkroniseras från Azure AD till BlueJeans i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i BlueJeans för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att BlueJeans-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

|Attribut|Typ|Stöds för filtrering|
|---|---|---|
|userName|Sträng|&check;|
|aktiv|Boolesk|
|title|Sträng|
|emails[type eq "work"].value|Sträng|
|name.givenName|Sträng|
|name.familyName|Sträng|
|phoneNumbers[type eq "work"].value|Sträng|
|urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Sträng|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för BlueJeans ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare som du vill etablera till BlueJeans genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare som definierats i **området** **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Kopplings begränsningar

* BlueJeans tillåter inte användar namn som överstiger 30 tecken.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)