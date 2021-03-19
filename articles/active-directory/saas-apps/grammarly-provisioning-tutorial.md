---
title: 'Självstudie: Konfigurera grammatik för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till grammatik.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: acfe74577f58cbdc0b1fe235e7fc1f661543fd90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609983"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Självstudie: Konfigurera grammatik för automatisk användar etablering

I den här självstudien beskrivs de steg som du måste utföra i både grammatik och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS, etablerar och avetablerar Azure AD automatiskt användare och grupper för att [grammatiskt](https://www.grammarly.com/) använda Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i grammatik
> * Ta bort användare i grammatik när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och grammatik

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* Ett grammatiskt företags konto med administratörs åtkomst.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Ta reda på vem som finns i [etableringsomfånget](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Ta reda på vilka data som ska [mappas mellan Azure AD och grammatik](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera grammatik för att stödja etablering med Azure AD

Kontakta din grammatik eller Skriv till <support@grammarly.com> att begära din etablerings-token.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till grammatik från program galleriet i Azure AD

Lägg till grammatiken från Azure AD-programgalleriet för att börja hantera etablering till grammatik. Om du tidigare har konfigurerat grammatik för SSO kan du använda samma program. Vi rekommenderar att du skapar en separat app när du testar integrationen från början. Mer information om hur du lägger till ett program från galleriet finns i [den här snabb](../manage-apps/add-application-portal.md)starten.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget

Du kan använda Azure AD Provisioning-tjänsten för det omfång som ska tillhandahållas baserat på tilldelning till programmet eller baserat på attribut för användaren eller gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska etableras baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen i [etablera appar med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* När du tilldelar användare och grupper till grammatiskt måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller.

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du kontrol lera det här alternativet genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper, kan du ange ett [attribut-baserat omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>Steg 5. Konfigurera automatisk användar etablering till grammatik

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper i TestApp baserat på användar-eller grupp tilldelningar i Azure AD.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Konfigurera automatisk användar etablering för grammatik i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**.

    ![Skärm bild som visar fönstret företags program.](common/enterprise-applications.png)

1. I listan med program väljer du **grammatik**.

    ![Skärm bild som visar hyperlänken med grammatik i listan över program.](common/all-applications.png)

1. Välj fliken **Etablering**.

    ![Skärm bild som visar fliken etablering.](common/provisioning.png)

1. Ange **etablerings läget** till **Automatisk**.

    ![Skärm bild som visar etablerings läget inställt på automatiskt.](common/provisioning-automatic.png)

1. I avsnittet **admin-autentiseringsuppgifter** anger du din **webb adress** för grammatik och information om **hemliga token** . Välj **Testa anslutning** för att säkerställa att Azure AD kan ansluta till grammatik. Om anslutningen Miss lyckas kontrollerar du att ditt konto för grammatik har administratörs behörighet och försöker igen.

    ![Skärm bild som visar rutorna för klient-URL och hemlig token.](common/provisioning-testconnection-tenanturltoken.png)

1. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Skärm bild som visar rutan e-postavisering.](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory användare till grammatik**.

1. Granska de användarattribut som synkroniseras från Azure AD till grammatik i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i grammatik för uppdaterings åtgärder. Om du ändrar det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du kontrol lera att det grammatikbaserade API: et stöder filtrering av användare baserat på detta attribut. Välj **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |externalId|Sträng|
   |aktiv|Boolesk|
   |displayName|Sträng|
   |emails[type eq "work"].value|Sträng|


1. Information om hur du konfigurerar omfångs filter finns i anvisningarna i [artikeln omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för grammatik ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Skärm bild som visar etablerings statusen växlad på.](common/provisioning-toggle-on.png)

1. Definiera de användare eller grupper som du vill etablera till grammatiken genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

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
