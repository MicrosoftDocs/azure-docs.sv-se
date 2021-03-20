---
title: 'Självstudie: Konfigurera Bizagi Studio för digital process Automation för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Bizagi Studio för digital process automatisering.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 72e021f47bb8db4dedf0e434d0d94bb2118a4c00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98728208"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Bizagi Studio för digital process Automation för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Bizagi Studio för automatisering av digitala processer och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras för att göra det, etablerar och avetablerar Azure AD automatiskt användare och grupper i [Bizagi Studio för automatisering av digitala processer](https://www.bizagi.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Bizagi Studio för digital process Automation
> * Ta bort användare i Bizagi Studio för digital process automatisering när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Bizagi Studio för digital process Automation
> * [Enkel inloggning](./bizagi-studio-for-digital-process-automation-tutorial.md) till Bizagi Studio för digital process Automation (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md). 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etableringen. Exempel är program administratör, moln program administratör, program ägare eller global administratör. 
* Bizagi Studio för digital process Automation version 11.2.4.2 X eller senare.

## <a name="plan-your-provisioning-deployment"></a>Planera etablering av distributionen
Följ de här stegen för att planera:

1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som kommer att vara [inom omfånget för etablering](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Bizagi Studio för digital process automatisering](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Konfigurera för att stödja etablering med Azure AD
Följ dessa steg om du vill konfigurera Bizagi Studio för digital process Automation för att stödja etablering med Azure AD:

1. Logga in på din arbets portal som en användare med **administratörs behörighet**.

2. Gå till **admin**-  >  **säkerhet**  >  **OAuth 2-program**.

   ![Skärm bild av Bizagi med OAuth 2-program markerade.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Välj **Lägg till**.
4. För **beviljande typ** väljer du **Bearer-token**. För **tillåtet omfång** väljer du **API** och **användar synkronisering**. Välj sedan **Spara**.

   ![Skärm bild av register program, med beviljande typ och tillåtet omfång markerat.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Kopiera och spara **klient hemligheten**. För Bizagi Studio för Automation-program för digital process på fliken **etablering** , anges klientens hemliga värde i fältet **hemligt token** . Azure Portal

   ![Skärm bild av OAuth med klient hemlighet highlighed.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Lägg till programmet från Azure AD-galleriet

För att börja hantera etablering till Bizagi Studio för digital process Automation, lägger du till appen från Azure AD-programgalleriet. Om du tidigare har konfigurerat Bizagi Studio för digital process automatisering för enkel inloggning kan du använda samma program. När du först testar integrationen bör du dock skapa en separat app. Mer information finns i [snabb start: lägga till ett program till din Azure Active Directory (Azure AD)-klient](../manage-apps/add-application-portal.md). 

## <a name="define-who-is-in-scope-for-provisioning"></a>Definiera vem som är inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som är baserat på tilldelning till programmet, baserat på attribut för användaren och gruppen, eller både och. Om du använder tilldelning, se stegen i [tilldela eller ta bort tilldelning av användare och grupper, för en app som använder Graph API](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du endast baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter. Mer information finns i [attribut-baserad program etablering med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Observera följande saker om scope:

* När du tilldelar användare och grupper till Bizagi Studio för digital process Automation måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etableringen och markeras i etablerings loggarna som markeras som inte faktiskt berättigade. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfånget för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attribut-baserat omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="configure-automatic-user-provisioning"></a>Konfigurera automatisk användaretablering 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper. Du gör detta i testappen, baserat på användar-och grupp tilldelningar i Azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Konfigurera automatisk användar etablering för Bizagi Studio för digital process Automation i Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**.

    ![Skärm bild av Azure Portal med företags program och alla program markerade.](common/enterprise-applications.png)

2. I listan program väljer du **Bizagi Studio för automatisering av digitala processer**.

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för hantering, med etablering markerat.](common/provisioning.png)

4. Ange **etablerings läget** till **Automatisk**.

    ![Screenshotof etablerings läges kontroll, med automatiskt markerat.](common/provisioning-automatic.png)

5. I avsnittet **admin credentials** anger du klient-URL och hemlig token för Bizagi Studio för digital process Automation. 

      * **Klient-URL:** Ange Bizagi SCIM-slutpunkten med följande struktur:  `<Your_Bizagi_Project>/scim/v2/` .
         Exempel: `https://my-company.bizagi.com/scim/v2/`.

      * **Hemlig token:** Det här värdet hämtas från det steg som beskrivs tidigare i den här artikeln.

      För att säkerställa att Azure AD kan ansluta till Bizagi Studio för digital process Automation väljer du **Testa anslutning**. Om anslutningen Miss lyckas kontrollerar du att Bizagi Studio för Automation-kontot för digital process har administratörs behörighet och försöker igen.

   ![Skärm bild av administratörsautentiseringsuppgifter, med test anslutning markerat.](common/provisioning-testconnection-tenanturltoken.png)

6. För **e-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Välj alternativet för att **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![Skärm bild av e-postalternativ för meddelanden.](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory användare till Bizagi Studio för digital process automatisering**.

9. I avsnittet **attribut-mappning** granskar du de användarattribut som synkroniseras från Azure AD till Bizagi Studio för digital process automatisering. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Bizagi Studio för digital process automatisering för uppdaterings åtgärder. Om du ändrar det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att Bizagi Studio för digital process Automation API stöder filtrering av användare baserat på detta attribut. Välj **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |aktiv|Boolesk|
   |emails[type eq "work"].value|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |namn. formaterad|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|

   Du kan lägga till anpassade tilläggsfunktioner genom att gå till **Visa avancerade alternativ > redigera attributlistan för Bizagi**. Attributen för anpassade tillägg måste föregås av **urn: IETF: parametrar: scim: schemas: tillägg: Bizagi: 2.0: UserProperties:**. Om till exempel attributet anpassat tillägg är **IdentificationNumber**, måste attributet läggas till som **urn: IETF: params: scim: schemas: tillägg: Bizagi: 2.0: UserProperties: IdentificationNumber**. Välj **Spara** för att genomföra ändringarna.
   
    ![Redigera attributlistan.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Mer information om hur du lägger till anpassade attribut finns i [Anpassa programattribut](../app-provisioning/customize-application-attributes.md).

> [!NOTE]
> Endast grundläggande typ egenskaper stöds (till exempel String, heltal, Boolean, DateTime osv.). De egenskaper som är länkade till parameter tabeller eller flera typer stöds inte ännu.

10. Information om hur du konfigurerar omfångs filter finns i [kursen om omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Bizagi Studio för digital process Automation, ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Skärm bild av växla mellan etablerings status.](common/provisioning-toggle-on.png)

12. Definiera de användare och grupper som du vill etablera till Bizagi Studio för automatisering av digitala processer. I avsnittet **Inställningar** väljer du önskade värden i **omfång**.

    ![Skärm bild av alternativ för omfånget.](common/provisioning-scope.png)

13. När du är redo att etablera väljer du **Spara**.

    ![Skärm bild av Spara kontroll.](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="monitor-your-deployment"></a>Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

- Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som har etablerats eller har misslyckats.
- Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
- Om etablerings konfigurationen är i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Mer information finns i [program etablering i karantän status](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)