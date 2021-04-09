---
title: 'Självstudie: Konfigurera Olfeo SAAS för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Olfeo SAAS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105549319"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Olfeo SAAS för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver göra i både Olfeo SAAS och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras etablerar och avetablerar Azure AD automatiskt användare och grupper i [OLFEO SaaS](https://www.olfeo.com) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Olfeo-SAAS
> * Ta bort användare i Olfeo-SAAS när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Olfeo SAAS
> * Etablera grupper och grupp medlemskap i Olfeo SAAS
> * [Enkel inloggning](olfeo-saas-tutorial.md) till Olfeo SaaS (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* En [OLFEO SaaS-klient](https://www.olfeo.com/).
* Ett användar konto i Olfeo-SAAS med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen

1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
1. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Ta reda på vilka data som ska [mappas mellan Azure AD och OLFEO SaaS](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Olfeo-SAAS för att ge stöd för etablering med Azure AD

1. Logga in på Olfeo SAAS Admin Console. 
1. Gå till **konfigurations > Annuaires**.
1. Skapa en ny katalog och ge den namnet.
1. Välj **Azure** -Provider och klicka sedan på **CR er** för att spara den nya katalogen. 
1. Gå till fliken **Synchronisation** om du vill se **klient-URL:** en och **jeton-hemligheten**. Dessa värden kopieras och klistras in i fälten **klient-URL** och **hemligt token** på fliken etablering i Olfeo SAAS-programmet i Azure Portal.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Olfeo-SAAS från Azure AD-programgalleriet

Lägg till Olfeo SAAS från Azure AD-programgalleriet för att börja hantera etablering till Olfeo SAAS. Om du tidigare har konfigurerat Olfeo SAAS för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Olfeo-SAAS måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>Steg 5. Konfigurera automatisk användar etablering till Olfeo SAAS 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper i Olfeo SAAS-appen baserat på användar-och grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Konfigurera automatisk användar etablering för Olfeo-SAAS i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. I listan program väljer du **OLFEO SaaS**.

    ![Olfeo SAAS-länken i program listan](common/all-applications.png)

1. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

1.  Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

1. I avsnittet **admin credentials** anger du din Olfeo SaaS- **klient-URL** och information om **hemliga token** . Välj **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Olfeo SaaS. Om anslutningen Miss lyckas kontrollerar du att Olfeo SAAS-kontot har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. I fältet **e-** postadress för avisering anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postavisering](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. I avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Olfeo SaaS**.

1. Granska de användarattribut som synkroniseras från Azure AD till Olfeo SAAS i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i OLFEO-SaaS för uppdaterings åtgärder. Om du ändrar det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att Olfeo SaaS-API: et stöder filtrering av användare baserat på det attributet. Välj **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |displayName|Sträng|
   |aktiv|Boolesk|
   |emails[type eq "work"].value|Sträng|
   |preferredLanguage|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |namn. formaterad|Sträng|
   |externalId|Sträng|  

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Olfeo SaaS**.

1. Granska gruppattributen som synkroniseras från Azure AD till Olfeo SAAS i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i OLFEO-SaaS för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |externalId|Sträng|
      |medlemmar|Referens|

1. Information om hur du konfigurerar omfångs filter finns i anvisningarna i [artikeln omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för Olfeo-SAAS ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

1. Definiera de användare eller grupper som du vill etablera till Olfeo-SAAS genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid än nästa cykler, som inträffar om var 40: e minut så länge som Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen

När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som etablerades lyckades eller inte.
* Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Mer information om karantäns tillstånd finns i [Application etablerings status för karantän](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Fler resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)