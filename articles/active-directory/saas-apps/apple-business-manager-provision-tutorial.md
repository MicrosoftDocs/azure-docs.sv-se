---
title: 'Självstudie: Konfigurera Apple Business Manager för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Apple Business Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: b4f24c9beffcd67fb84940c2e159da615496d9aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180378"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Apple Business Manager för automatisk användar etablering



I den här självstudien beskrivs de steg du behöver utföra i både Apple Business Manager och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare till [Apple Business Manager](https://business.apple.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Apple Business Manager
> * Ta bort användare i Apple Business Manager när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Apple Business Manager

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör).
* Ett Apple Business Manager-konto med rollen som administratör eller personal Manager.

> [!NOTE]
> Att överföra token till Azure AD och upprätta en lyckad anslutning måste slutföras inom 4 Kalender dagar eller så måste processen startas igen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Apple Business Manager](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Apple Business Manager för att ge stöd för etablering med Azure AD

1. Logga in med ett konto som har rollen administratör eller person ansvarig i Apple Business Manager.
2. Klicka på Inställningar längst ned på sid panelen, klicka på data källa under organisations inställningar och klicka sedan på Anslut till data källa.
3. Klicka på Anslut bredvid SCIM, Läs varningen noggrant och klicka på Kopiera. Klicka sedan på Stäng.
[Fönstret Anslut till SCIM som innehåller en token och en kopierings knapp under den.] Lämna fönstret öppet för att kopiera klient webb adressen från Apple Business Manager till Azure AD, som är: https://federation.apple.com/feeds/business/scim

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Den hemliga token får inte delas med någon annan än Azure AD-administratören.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Apple Business Manager från Azure AD-programgalleriet

Lägg till Apple Business Manager från Azure AD-programgalleriet för att börja hantera etablering till Apple Business Manager. Om du tidigare har installerat Apple Business Manager för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare till Apple Business Manager måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>Steg 5. Konfigurera automatisk användar etablering till Apple Business Manager

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Apple Business Manager**.

    ![Apple Business Manager i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **bas-URL: en för scim 2,0 och få åtkomst till token** som hämtats från Apple Business Manager i **klient-URL** och **hemlig token** ... Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Apple Business Manager. Om anslutningen Miss lyckas kontrollerar du att ditt Apple Business Manager-konto har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Om anslutningen lyckas, visar Apple Business Manager SCIM-anslutningen som aktiv. Den här processen kan ta upp till 60 sekunder innan Apple Business Manager visar den senaste anslutnings statusen.

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Apple Business Manager**.

9. Granska de användarattribut som synkroniseras från Azure AD till Apple Business Manager i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Apple Business Manager för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |aktiv|Boolesk|
   |userName|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |name.givenName|Sträng|
   |externalId|Sträng|
   |locale|Sträng|
   |timezone|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Apple Business Manager ändrar du **etablerings statusen** till **på** i avsnittet Inställningar.

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Apple Business Manager genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Granska SCIM-krav för Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Hur ett person-ID används i Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [Använd SCIM för att importera användare till Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Lösa SCIM användar konto konflikter i Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Ta bort Azure AD-konton som visas i Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [Visa SCIM-aktivitet i Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Hantera befintliga SCIM-token och anslutningar i Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Koppla från SCIM-anslutningen i Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd609be3a61)
* [Felsöka SCIM-anslutningen i Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd403a0f3bd/web)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)